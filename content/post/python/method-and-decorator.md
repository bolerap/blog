+++
draft = false
title = "Method and decorator in Python"
date = "2016-11-12T13:03:32+07:00"
tags = [ "python decorator", "decorator", "python method", "python" ]
categories = [ "python" ]
image = "method-and-decorator.jpg"

+++

**What is decorators?**

Một decorator đơn giản là một function nhận vào một function khác làm argument và thay thế function đó bằng một function đã modified. 
{{< gist thanhngvpt e87181a5901bf0621a6d7f39cb941bf8 >}}
Ví dụ này thực chất là decorator chẳng làm gì cả nó chỉ đơn giản là trả về function vừa nhận vào. Cùng xem xét một ví dụ phức tạp hơn.

{{< gist thanhngvpt d10d9df3b4b08327c9cae4584710d3df >}}

**What is methods?**

Một method là một function được bound tới một instance của class, với first parameter được truyền implicit là một instance (self)
{{< gist thanhngvpt 1b7c3d84dfd4b0242a9051dcacfb8627 >}}

**Static method**

Static method là một plain function được gọi thông qua class hoặc class instance

**Class method**

Class method là một function được bound tới class, với first parameter được truyền implicit là class object đó (cls)

