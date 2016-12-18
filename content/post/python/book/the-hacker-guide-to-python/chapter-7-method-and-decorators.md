+++
title = "Chapter 7 method and decorators"
draft = true
date = "2016-11-12T11:03:31+07:00"
tags = [ "python", "python book", "the hacker guide to python new edition" ]
categories = [ "python" ]
image = "the-hacker-guide-to-python.jpg"

+++

Python cung cấp một cách tiện dụng để modify các function là decorator.

1. **Creating decorators**

Một decorator đơn giản là một function nhận vào một function khác làm argument và thay thế function đó bằng một function đã modified. 
{{< gist thanhngvpt e87181a5901bf0621a6d7f39cb941bf8 >}}
Ví dụ này thực chất là decorator chẳng làm gì cả nó chỉ đơn giản là trả về function vừa nhận vào. Cùng xem xét một ví dụ phức tạp hơn.
{{< gist thanhngvpt d10d9df3b4b08327c9cae4584710d3df >}}
