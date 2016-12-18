+++
title = "chapter 4 array slice map"
draft = true
date = "2016-10-21T16:45:45+07:00"
tags = [ "Go", "Go In Action", "Book" ]
categories = [ "Go", "Book" ]

+++

Khá khó khăn để viết các chương trình không cần lưu trữ và đọc các nhóm dữ liệu. Nếu bạn dùng databases hoặc files hoặc truy cập web, bạn cần một cách thức để xử lý dữ liệu nhận được và gửi đi. Go có ba cấu trúc dữ liệu (data structure) khác nhau cho phép bạn làm việc với các nhóm dữ liệu là: arrays, slices, maps. Những cấu trúc dữ liệu này đã được tích hợp sẵn vào ngôn ngữ và sử dụng xuyên suốt các thư viện chuẩn (standard library). Khi bạn học về cách làm việc của những cấu trúc dữ liệu này, việc lập trình trong go trở nên fun, fast và flexible.

1. Array internals and fundamentals.

	[It makes sense to start with arrays] vì nó là cấu trúc dữ liệu nền tảng cho slice và map. Việc hiểu cách array làm việc sẽ giúp bạn đánh giá đúng sự lịch lãm và sức mạnh mà slice và map cung cấp.

	1.1 Internals.

	Một array trong go là một kiểu dữ liệu fixed length chứa một khối các phần tử cùng kiểu kề nhau. Điều này có thể như một built-in type như integer, string hoặc là kiểu cấu trúc. Trong hình 1.1 dưới đây mô tả một array. Các phần tử của array được tô màu xám và được kết nối xâu chuỗi với các phần tử khác. Mỗi phần tử đều cùng kiểu dữ liệu, trong trường hợp này là integer và có thể được truy cập thông qua một chỉ số duy nhất.
	[Hình 1.1 thêm sau]

	Array là cấu trúc dữ liệu rất có ích bởi vì bộ nhớ được phân bổ tuần tự. Có bộ nhớ ở dạng liền kề có thể giúp lưu trữ bộ nhớ bạn sử dụng ở trạng thái loaded trong phạm vị CPU cache lâu dài. Việc sử dụng chỉ số toán học bạn có thể lặp qua tất cả các phẩn tử của array nhanh chóng. Thông tin kiểu cho array cung cấp khoảng cách trong bộ nhớ bạn phải di chuyển để tìm mỗi phần tử. Vì mỗi phần tử có cùng kiểu và theo sau phần tử khác tuần tự, việc di chuyển xuyên suốt array là phù hợp và nhanh. 

	1.2 Declaring and initializing.

	Một array được khai báo bằng cách xách định kiểu dữ liệu được lưu trữ và tổng số phẩn tử của array hay còn được gọi là array's length.

		// declare an integer array of five element
		var array [5]int

	Một khi array được khai báo kiểu dữ liệu của các phần tử hoặc length không thể thay đổi. Nếu bạn cần thêm phần tử bạn phải tạo một array mới với length cần thiết sau đó copy các giá trị từ array cũ sang. 

	Khi các biến trong go được khai báo, chúng luôn được khởi tạo zero value tương ứng với kiểu dữ liệu của chúng, và array cũng không ngoại lệ. Khi một array được khởi tạo mỗi phần tử riêng biệt của array sẽ được khởi tạo zero value, trong trường hợp array ta vừa khai báo ở trên sẽ tạo ra array kiểu int có length là 5 và các phần tử của array được khởi tạo zero value theo int là 0.

	Một cách nhanh và dễ dàng tạo và khởi tạo array là sử dụng array literal. Array literal cho phép bạn khai báo số lượng phẩn tử cẩn thiết và xác định giá trị cho các phần tử đó.

		// Declare an interge array of five element use array literal
		array := [5]int{1, 2, 3, 4, 5}

	Nếu length của array là ...(dấu 3 chấm) go sẽ tự nhận ra length của array dựa trên số lượng phần tử được khởi tạo.

		// Declare an integer array, initialize each element with a specific value
		// Capacity is determined based on the number of values initialized
		array := [...]int{1, 2, 3, 4}

	Nếu bạn biết length của array bạn cần, nhưng bạn chỉ cần khởi tạo vài phần tử cụ thể bạn có thể sử dụng cú pháp sau.

		// Declare an integer array of five elements.
		// Initialize index 1 and 2 with specific values.
		// The rest of the elements contain their zero value
		array := [5]{1: 10, 2: 20}

	1.3 Working with arrays.

		
	