+++
title = "Interface in go"
draft = false
date = "2016-11-02T11:28:04+07:00"
tags = [ "go", "interface" ]
categories = [ "go" ]
image = "interface-in-go.png"

+++

**What is an interface?**

*Interface là một tập các methods và cũng là một type*. Nói theo cách của go thì interface là những actions mà type có thể execute thay vì những loại data mà type có thể hold như trong vài ngôn ngữ.

**Implement an interface**

Trong go không có từ khóa `implement` tường minh như một vài ngôn ngữ, và để implement một interface chỉ cần implement một trong các method có trong interface.

**Empty interface**

Empty interface là `interface{}`, empty interface không có method nào, vì trong go không có keyword implement nên mọi type đều implement ít nhất là zero method và như vậy là thỏa mãn điều kiện implement của empty interface. Đây chính là nguồn gốc của những phức tạp khi sử dụng interface. Ví dụ với function `doSomthing()` dưới đây. {{< gist thanhngvpt 1e6dc031374ea543a70c90a9ad0f7f53 >}}
argument v trong function `doSomething()` có type là `interface{}` chứ không phải any type như nhiều người lầm tưởng. Phân tích thêm chút phần này nào, flow sẽ là khi truyền 1 vào `doSomthing()` một argument go runtime sẽ thực hiện type conversion nếu cần thiết và convert value sang `interface{}` value. Mọi value đều chỉ có chính xác 1 type khi runtime và type của `v` sẽ là `interface{}`. Vậy dữ liệu thực tế được truyền vào `doSomthing()` là `interface{}` value à? Có gì đó sai sai nhỉ? Lại phân tích tiếp nào. 

Một interface value được kiến tạo vởi 2 phần data như sau:
	- Một con trỏ trỏ tới method table dành cho underlying type của interface value.
	- Một phần trỏ tới data thực sự được lưu trữ bởi interface value.

Cùng xem xét một ví dụ về interface như sau {{< gist thanhngvpt 7ab0a20f868e9ee511cff8c4f17bda5d 
>}}

Trong ví dụ trên ta khai báo biến slice `vehicles` là `Vehicle` value (interface value), mỗi phần tử của `vehicles` đều có underlying data khác nhau.

