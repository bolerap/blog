+++
title = "String, bytes, runes and character in Go"
draft = true
date = "2016-11-21T11:48:37+07:00"

+++

**Introduction**

Bài viết trước đã giải thích cách slice hoạt động thông qua các ví dụ mô minh họa cơ chế được cài đặt đằng sau. Trên nền tảng đó bài viết này sẽ thảo luận về string trong go. Đầu tiên, string có thể đơn giản như một topic cho một bài viết của blog. Nhưng để sử dụng chúng tốt yêu cầu phải hiểu không chỉ cách chúng hoạt động mà còn phải hiểu sự khác nhau giữa byte, character, rune và sự khác nhau giữ unicode với utf-8, sử khác nhau giữa string và string literal.

Một cách tiếp cận chủ đề này là nghĩ nó như một câu trả lời cho FAG. Khi nào index một go string tại một vị trí n? tại sao không lấy character thứ n? Như bạn thấy những câu hỏi này đưa chúng ta tới nhiều chi tiết bề cách text hoạt động trong thế giới hiện đại.

Một bài giới thiệu tuyệt với về những vấn đề này, độc lập với go là của Joel Spolsky một blog nổi tiếng.

**What is a string?**

Bắt đầu với những thứ cơ bản.

Trong go, một string là một read-only byte slice. điều quan trọng là một string sẽ chứa một số byte bất kì, Không yêu cầu là unicode text, utf-8 text hay bất kì một định dạng đã định nghĩa trước. theo như nội dung của string là có liên quan, nó chính xác tương đương với một slice of byte.

Đây là một string literal ta sử dụng ký hiệu `\xNN` để định nghĩa một hằng string chứa vài giá trị byte riêng biệt. 

	const sample = "\xbd\xb2\x3d\xbc\x20\xè\x8c\x98"

**Printing strings**

Vì có vài byte strong hằng string `sample` ở trên không hợp lệ ASCII và UTF-8, việc in trực tiếp string sẽ tạo ra output xấu xí.

	fmt.Println(sample) // ��=� ⌘

Để tìm ra thứ thực sự mà string đang lưu trữ, chúng ta cần lấy từng phần và kiểm tra từng phần một. Có vài cách để làm việc này, cách rõ ràng nhất là lặp thông qua nội dung của nó và lấy ra từng byte riêng lẻ, ví dụ.

	for i:= 0; i < len(sample); i++ {
		fmt.Printf("%x ", sample[i]) // bd b2 3d bc 20 e2 8c 98
	}

Chú ý cách mỗi byte riêng biệt khớp với các các số hexa được định nghĩa trong `sample` (\xNN là cú pháp biểu thị số hexa)

Một cách ngắn gọn để in ra những byte trong string là sử dụng %x trong lệnh in fmt.Printf, lệnh này sẽ dump ra chuỗi các bytes liên tiếp của string dưới dạng số hexa, 2 số là 1 byte.

	fmt.Printf("%x\n", sample)

hoặc sử dụng một trick nhỏ với string format ở trên

	fmt.Printf("% x\n", sample)

Chúng ta có thể sử dụng %q với string format để escape bất ký non-printable byte sequences trong string.
	
	fmt.Printf("%q\n", sample) // "\xbd\xb2=\xbc ⌘"

Nếu chúng ta nhìn liếc qua ta có thể thấy trong output có kí hiệu asciii(kí hiệu =) tiếp theo là một kí tự space thông thường và cuối cùng là biểu tượng "place of interest". Biểu tượng này có unicode value là u+2318, utf-8 encoded bởi các byte sau dấu space (hex value 20): e2 8c 98.

Nếu bạn không quen hoặc thấy mơ hồ bởi những giá trị lạ trong string, chúng ta có thể sử dụng thêm flag %q. Flag này gây ra ouput to escapte không chỉ non-printable sequences, mà còn với bất kỳ non-ascii bytes, tất cả trong khi diễn dịch utf-8, kết quả là nó cho thấy nhiều giá trị unicode được định dạng theo utf-8 đại diện cho các dữ liệu non-ascii trong string.

	fmt.Printf("%+q\n", sample) # "\xbd\xb2=\xbc \u2318"

Những kĩ thuật in này tốt cho việc debug nội dung của string, và sẽ thuận tiện trong việc thảo luận dưới đây. Nó chỉ ra những điểm giá trị mà các phương thức vận hành chính xác như byte slice.

**Utf-8 and string literal**

Chúng ta thấy rằng indexing một string sẽ sinh ra các byte của nó, không phải là các character, một string chỉ là một nhóm các byte. Điều đó có nghĩa là khi chúng ta lưu trữ một character value trong một string là chúng ta lưu trữ các byte của nó.

Cùng xem xét một ví dụ in một string constant theo ba cách khác nhau.

	func main() {
		const placeOfInterest = `⌘`

		fmt.Printf("Plain string: %s\n", placeOfInterest)
		fmt.Printf("Quoted string: %+q\n", placeOfInterest)
		fmt.Print("Hex bytes: ")
		for i := 0; i < len(placeOfInterest); i++ {
			fmt.Printf("%x", placeOfInterest[i])
		}
		fmt.Println()
	}

Kết quả

	Plain string: ⌘
	Quoted string: "\u2318"
	Hex bytes: e2 8c 98

Điều này nhắc nhở ta rằng unicode value U+2318, the "place of interest" symbol, được miêu tả bởi các byte `e2 8c 98`, và những byte này là utf-8 encoding của hexadecimal value `2318`.

Nó có thể rõ ràng hoặc mơ hồ phụ thuộc vào độ quen thuộc của bạn với utf-8, nhưng giá trị giải thích cách utf-8 mô tả một string. Sự thật đơn giản là nó được tạo ra khi mã nguồn được viết.

Ngắn gọn, mã nguồn go là utf-8, mã nguồn cho string literal là utf-8 text. Nếu string literal đó không chưa escape sequences, raw string không thể, kiến tạo string sẽ chứa chính xác nguồn text giữa quotes. Bởi vậy việc định nghĩa và kiến tạo raw string sẽ luôn chứa mỗt miêu tả utf-8 hợp lệ của nội dung của nó. tương tự trữ phi nó chứa utf-8 breaking escape giống như phần trước, một string literal thông thường sẽ luôn chứa utf-8 hợp lệ.

Vài người nghĩ go string luôn là utf-8, nhưng chúng không phải vậy, chỉ string literal là utf-8 text. Như chúng ta đã xem trước đây, string value có thể chửa những byte tùy ý, và trong bài viết này đã nói string literal luuôn chứa utf-8 text miễn là chúng không có byte-level escape

**Code points, characters, and runes**

Chúng ta đã từng rất cẩn thận cho tới nay trong cách sử dụng từ byte và character. Một phần vì string chứa các bytes, một phần vì ý tưởng của character có một chút khó định nghĩa. Chuẩn unicode sử dụng khái niệm code point thể tham chiếu tới item được miêu tả bởi một single value. Code point u+2318  với hexadecimal value 2318 miêu tả biểu tượng ⌘. 

Vậy những kí tự thường có dấu như à là gì? đó là một character, và nó cũng có một code point (u+00E0), nhưng nó có sự miêu tả khác. Ví dụ chúng ta có thể sử dụng kết hợp dấu và code point để tạo ra kí tự tương tự, U+0300 đính kèm vào kí tự a thường U+0061 để tạo ra character à.