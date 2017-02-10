+++
draft = false
date = "2017-02-10T09:20:33+07:00"
title = "Micro service"

+++

Micro-service là những component riêng biệt làm việc cùng nhau để cung cấp các chức năng và business logic cho một ứng dụng lớn, thường giao tiếp với nhau thông qua một giao thức mạng (như HTTP/2 hoặc vài binary transport) và được phân phối qua nhiều máy vật lý. Mỗi component được cô lập với các componet khác, và chúng nhận những inputs riêng biệt và sinh ra các output riêng biệt. Nhiều instance của cùng một service có thể chạy qua nhiều server và traffict có thể được cân bằng tải giữa chúng. Nếu được thiết kế đúng, nó có thể giúp khi một instance riêng lẻ bị fail nó cũng không gây down toàn hệ thống và giúp mở rộng hệ thống bằng cách bổ sung thêm instance mới trong khi runtime.

Go kit là một toolkit được phân phối dành cho việc xây dựng các ứng dụng sử dụng kiến trúc micro-service tạo bởi @perterbourgon và giờ đây được duy trì bởi các Gopher. Nó hướng tới giải quyết nhiều khía cạnh cơ bản của việc xây dựng những hệ thống như vậy và cũng khuyến khích các design pattern tốt, cho phép bạn tập trung vào business logic để chăm chút cho sản phẩm hoặc dịch vụ.

Go kit không cố giải quyết mọi vấn đề từ scratch, nó tích hợp với nhiều dịch vụ phổ biến có liên quan để giải quyết các vấn đề SOA (service oriented architecture) như serivce discovery, metrics, monitoring, logging, load balancing, circuit breakin, và nhiều khía cạnh quan trọng khác. Khi chúng ta xây dựng service của chúng ta bằng tay sử dụng go kit, bạn sẽ nhận thấy rằng chúng ta sẽ viết khá nhiều boilerplate hoặc scaffold code để có những thứ làm việc được.

Với những product và service nhỏ hơn cùng với một nhóm nhà phát triển nhỏ, bạn có thể quyết định dễ dàng là chỉ đưa ra một endpoint json đơn giản, nhưng go kit thực sự tốt cho những nhóm (team) lớn hơn, xây dựng những hệ thống lớn với nhiều service, mỗi service chạy hàng chục hoạc hàng trăm lần. logging nhất quán, instrumentation, distributed tracing, và mỗi service tương tự với cái tiếp theo đang chạy.

Trong chương này, chúng ta sẽ xây dựng vài micro-services nhắm tới nhiều thử thách an toàn khác nhau. Business sẽ được giữ cho rất đơn giản, cho phép chúng ta tập trung vào việc học những nguyên tắc xung quanh việc xây dựng những hệ thống micro-service.

Có vài lựa chọn thay thế cho go kit, hầu hết chúng đều có một giải pháp giống nhau nhưng với những độ ưu tiên, cú pháp, và pattern khác nhau. Đảm bảo rằng bạn tìm kiếm nhiều hướng với những tùy chọn khác nhau trước khi lao vào một project, nhưng những nguyên tắc bạn học trong chương này sẽ áp dụng phổ biến.

Đặc biệt trong chương này bạn sẽ học:

- How to hand code a micro-service using go kit
- What gRPC is and how to use it to build servers and clients.
- How to use google's protocol buffers and associated tools to describe services and communicate in a highly efficient binary format.
- How endpoints in go kit allow us to write a single service implementation and have it exposed via multiple transport protocols.
- How go kit included subpackages help us solve lots of common problems
- How middleware lets us wrap endpoints to adapt their behavior without touching the implementation itself
- How to describe method calls as requests and response message
- How to rate limit our services to protect from surges in traffic
- a few other idiomatic go tips and tricks

Một vài dòng code dài trong chương này trải trên nhiều dòng.

# Introducing gRPC
Có nhiều lựa chọn khi các service của chúng ta muốn giao tiếp với nhau, client muốn giao tiếp với service, và go kit không quan tâm điều này. Thực ế, chúng có thể thêm nhiều tùy chọn cho người dùng và để cho họ quyết định cái họ muốn dùng. Chúng ta sẽ hỗ trợ định dạng JSON quen thuộc thông qua HTTP, nhưng chúng cũng sẽ giới thiệu một công nghệ mới cho API là gRPC.

gRPC, ngắn gọn là Google's Remote Call Procedure Call, là một cơ chế mã nguồn mở được sử dụng để gọi code đang chạy từ xa thông qua mạng. Nó sử dụng HTTP/2 để vận chuyển và giao thức buffers để trình bày dữ liệu .

Một RPC service khác với RESTful web service là thay vì tạo thay đổi tới dữ liệu sử dụng các chuẩn HTTP riêng biệt, Khi bạn làm với RESTful(POST để tạo, PUT để cập nhật, DELETE để xóa, ...) bạn phát ra một remote function hoặc method, truyền vào những tham số mong muốn và nhận về dữ liệu mong muốn trong response.

Để làm rõ sự khác nhau, tưởng tượng rằng chúng ta đang tạo một user mới. Trong thế giới RESTful, chúng ta có thể tạo POST request như sau.

	POST /users
	{
		"name": "David",
		"twitter": "@thanhngvpt"
	}

Và nhận về một response.

	201 Created
	{
		"id": 1,
		"name": "David",
		"twitter": "@thanhngvpt"
	}

Lời gọi RESTful miêu tả các câu truy vấn hoặc thay đổi tới trạng thái của tài nguyên. Trong thế giới RPC, chúng ta sẽ dùng code được tạo ra (generated) để tạo ra binary serialized lời gọi thủ tục như vậy cảm giác giống những method hay function thông thường trong go.

Điểm khác nhau chính nữa là gRPC service sử dụng định dạng `protocol buffers` thay vì JSON, XML mà RESTful sử dụng.

# Protocol buffers
protocal buffers là một định dạng binary serialization với kích thước rất nhỏ và encode/decode cực kì nhanh. Bạn mô tả cấu trúc theo một cách trừu tượng sử dụng một ngôn ngữ khai báo mini, và sinh ra code (theo nhiều ngôn ngữ khác nhau) giúp cho việc đọc và ghi dữ liệu dễ dàng.

Bạn có thể nghĩ protocol buffer như một thay thế hiện đại cho XML, ngoại trừ việc định nghĩa cấu trúc dữ liệu được tách riêng với nội dung, và nội dung dưới dạng binary data hơn là text.

Tuy protocol buffer có nhiều lợi ích nhưng trong nhiều trường hợp thì sự lựa chọn XML hoặc JSON vẫn là hợp lý hơn nếu kích thước file không quan trọng.

# Protocol buffers language
Để định nghĩa cấu trúc dữ liệu, chúng ta sẽ dùng phiên bản 3 của protocol buffers language (còn gọi là proto 3) tạo file `vault/pb/vault.proto` với nội dung sau.

	syntax = "proto3";
	package pb;

	service Vault {
	    rpc Hash(HashRequest) returns (HashResponse) {}
	    rpc Validate(ValidateRequest) returns (ValidateResponse) {}
	}

	message HashRequest {
	    string password = 1;
	}

	message HashResponse {
	    string hash = 1;
	    string err =2;
	}

	message ValidateRequest {
	    string password = 1;
	    string hash = 2;
	}

	message ValidateResponse {
	    string valid = 1;
	}

File trên định nghĩa một service có tên `Vault` với 2 rpc method là `Hash` và `Validate`, các message `HashRequest`, `HashResponse`, `ValidateRequest`, `ValidateResponse` trong mỗi message  ta định nghĩa các field với cấu trúc `type name = position` type là một string miêu tả các scalar value type như: string, bool, double, float ..., name là một string mô tả field, position là một giá trị integer cho biết nơi field xuất hiện trong data stream, điều này khá quan trọng vì nội dung là một stream of bytes và được xắp thứ tự theo định nghĩa. Ngoài ra nếu chúng ta thêm hoặc đổi tên các field mà không làm hỏng các component.

Cần lưu ý rằng mỗi method call có một cặp request/response được liên kết. Những message này sẽ được gửi đi thông qua mạng khi remote method được gọi.

# Generating go code
Go không hiểu code của file .proto vì vậy ta phải biên dịch nó sang code go dùng câu lệnh `protoc vault.proto --go_out=plugins=grpc:.`

# Building the service

Chúng ta vừa định nghĩa package `vault`, interface `Service` gồm 2 method `Hash` và `Validate` (Có thể bạn nghĩ tên `VaultService` sẽ hợp lý hơn là `Service` nhưng hãy lưu ý là chúng ta đang xây dựng một package, và trong go khi sử dụng package này từ bên ngoài sẽ có dạng `vault.Service`). Mỗi method nhận tham số đầu tiên là `context.Context` (chú ý là ở đây chúng ta sử dụng package `golang.org/x/net/context` không phải `context` - được đổi tên từ go 1.7)

Một phần của việc thiết kế micro-service là quan tâm tới việc nơi lưu trữ trạng thái. Mặc dù bạn có cài đặt các method của một service trong một file đơn lẻ với truy cập vào các biến toàn cục, bạn đừng bao giờ sử dụng chúng để lưu trữ mỗi request hoặc thậm chí mỗi trạng thái service. Quan trọng là phải nhớ mỗi service giống như đang chạy trên nhiều máy vật lý nhiều lần, mỗi cái không truy cập vào các biến toàn cục của cái khác.

Chúng ta sẽ cái đặt service của chún ta sử dụng một empty struct, thực chất là một thủ thuật trong go dùng để gom các method cùng nhau để implement một interface mà không lưu trữ state nào (Nếu việc cài đặt yêu cầu dependencies như database connection, configuration object) bạn có thể lưu chúng bên trong struct và dùng method receiver trong thân của function.

# Starting with tests

Bắt đầu bằng việc viết test code có nhiều lợi thế thường giúp tăng chất lượng và bảo trì code. Chúng ta sẽ viết một unit test sử dụng service mới của chúng ta để hash và sau đó validate password. `vault/service_test.go`.

	package vault

	import (
		"testing"
		"golang.org/x/net/context"
	)

	func TestHasherService(t *testing.T) {
		// create new service
		srv := NewService()
		ctx := context.Background()
		h, err := srv.Hash(ctx, "password")
		if err != nil {
			t.Errorf("Hash: %s", err)
		}
		// validate with valid password
		ok, err := srv.Validate(ctx, "password", h)
		if err != nil {
			t.Errorf("Valid: %s", err)
		}
		if !ok {
			t.Error("Expected true from valid")
		}
		// try validate with wrong password
		ok, err = srv.Validate(ctx, "wrong password", h)
		if err != nil {
			t.Errorf("Valid: %s", err)
		}
		if ok {
			t.Error("Expected false from Valid")
		}
	}

# Constructor in go

Một constructor trong các ngôn ngữ lập trình hướng đối tượng là một function đặc biệt để tạo các instance của một class. Nó thực hiện khởi tạo và nhận các tham số bắt buộc như là những dependecies. Nó thường là cách duy nhất để tạo một object trong những ngôn ngữ này, nó thướng có cú pháp không tự nhiên hoặc dựa trên việc đặt tên thuận tiện (như tên function giống với tên class ...).

Go không có constructor, nó khá đơn giản và chỉ có các function, bởi function có thể trả về các argument, một constructor chỉ là một global function trả về một instance của một struct. Triết lý của go là đưa việc quyết định những vấn đề này cho những nhà thiết kế ngôn ngữ hơn là việc ép mọi người phải học về những khái niệm mới của việc kiến tạo object, developers chỉ phải học cách các function làm việc và họ có thể build những constructor với chún.

Thậm chí nếu chúng ta không phải làm những côn việc đặc biệc trong việc kiến tạo một object (như khởi tạo các field, validate dependecies...). Trong trường hợp của chúng ta, chúng không muốn làm phức tạp API bằng việc exposing kiểu `vaultService` vì chúng ta đã có `Service` interface đã exposed và ẩn bên trong một constructor là một cách hay để đạt được điều này.

Điều này không chỉ giúp chúng ta ngăn internal code bị lộ, nhưng nếu trong tương lai chúng ta cần làm nhiều việc để cho `vaultService` sẵn sàng sử dụng, chúng ta cũng có thể làm điều đó mà không cần thay đổi API và bởi vậy không yêu cầu người dùng thay đổi bất cứ thứ gì, đây chính là một thắng lợi lớn khi thiết kế API.

# Hashing and validating passwords with bcrypt

method đầu tiên chúng ta sẽ implement trong service là `Hash`. Nó sẽ nhận một password và tạo ra hash. hash kết quả có thể sau đó được truyền vào (cùng với password) method `Validate` sau đó.

Điểm chính của service của chúng ta là để đảm bảo rằng password không bao giờ được lưu trữ trong database, vì đó là một sự mạo hiểm trong bảo mật, nếu ai đó có thể truy cập vào database. Để thay thế, bạn có thể tạo ra hash one-way (không thể decode) điều đó có thể giúp lưu trữ được an toàn, và khi người dùng thử xác thực, bạn có thể thực hiện kiểm tra để xem password có đúng hay không.

package `bcrypt` cung cấp các method làm công việc này cho chúng ta theo cách an toàn và đáng tin cậy.

# Modeling method calls with requests and responses.

Vì service của chúng ta sẽ bị exposed qua các giao thức truyền tải khác nhau, chúng ta sẽ cần một cần một cách để model các request và response vào và ra service của chúng ta. Chúng ta sẽ làm điều này bằng cách thêm định nghĩa cấu trúc cho mỗi message mà service của chúng ta sẽ chấp nhận và trả về.

Để cho ai đó gọi method `Hash` và nhận về một hashed password, chúng ta sẽ bổ sung thêm 2 struct `vault/service.go`

	// Modeling request and response message
	type hashRequest struct {
		Password string `json:"password"`
	}
	type hashResponse struct {
		Hash string `json:"hash"`
		Err string `json:"err,omitempty"`
	}

Kiểu `hashRequest` chứa một field duy nhất `password` và `hashResponse` chứa kết quả `Hash` và `Err`. Để model các remote method call, về cơ bản bạn tạo một `struct` cho incoming argument và một struct cho return arguments.

Trước khi tiếp tục hãy xem có nên model request và response cho `Validate` method không. Hãy xem signature trong `Service` interface, hãy hình dung các arguments mà nó chấp nhận và suy nghĩ về kiểu của response mà chúng ta sẽ cần.

Chúng ta sẽ bổ sung một helper method ( kiểu `http.DecodeRequestFunc` từ go kit) có thể decode json body của `http.Request`.