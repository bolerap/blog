+++
title = "Object oriented programming in Python"
draft = false
date = "2016-11-09T03:02:06+07:00"
tags = [ "oop", "python" ]
categories = [ "python" ]
image = "oop.jpg"

+++

**OOP concepts**

OOP có các concept như sau.

1. Class: Là prototype/blueprint của một object. Nó gồm một tập các attributes để mô tả cho object, và attributes = data members + methods = (class vars + instance vars) + methods.

2. Instance: Là một thực thể đơn được tạo từ một class, đã được cấp phát memory. Kiểu như 1 bản copy của class với attributes riêng biệt với từng instance riêng.

3. Object: Là chỉ một thực thể có attributes. Và hoàn toàn có thể nói một object là một instance của một class.

4. Class variables: Là các biến được định nghĩa bên trong một class và bên ngoài các method của class đó, *nó được chia sẻ cho tất cả các instance của class đó dùng*. Class variables còn được biết đến với tên là static member variables như trong C++, Java, C#. Khi giá trị của class variables thay đổi thì ảnh hưởng tới tất cả các instance của class đó.

5. Instance variables: Là các biến chỉ có thể sử dụng bởi chính instance đó.

6. 