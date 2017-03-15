+++
date = "2016-11-17T19:29:06+07:00"
title = "Haskell first step"
draft = false
tags = ["haskell", "learn haskell", "haskell tutorial"]
categories = ["haskell", "translate"]
image = "haskell.jpg"

+++

Values, functions, types là những thành phần cơ bản của một chương trình haskell. Trước khi đi vào viết chương trình haskell chúng ta cùng xem xét các thành phần này.

**Values**

Values là các giá trị như `5` là một số nguyên, `hello` là một chuỗi kí tự ... values được xử lý bởi các funtions ví dụ function `+` nhận vào hai số và tạo ra một số mới, nói cách khác function là mapping các input values thành output values.

**Types**

Một nhóm các values có cùng thuộc tính gọi là một type. Trong haskell có các primitive type như sau: Int, Bool, Float, Double, String, Char.

**Functions**

Một function bao gồm hai thành phần: head (gồm tên hàm và danh sách các tham số) + body (code thực thi của hàm).
{{< gist thanhngvpt f89a64aa15161f02a7b277e8e334fae9>}}

**Type signatures**

Type signatures là một phần tùy chọn nhưng được khuyến khích sử dụng. 
{{< gist thanhngvpt 2dde8d7b95af5e0ee8bef304ecb7aacd >}}


**Multiple arguments**

Function nhận vào nhiều tham số đều là curried function vì mặc định function trong haskell chỉ nhận vào một tham số, function có thể nhận vào một function và trả về một function.

**A first glance at overloading**

Tất cả những ngôn ngữ lập trình chính thống đều cung cấp những chức năng mà argument type không bị giới hạn bởi một single type thay vào đó là một family of types. Ví dụ 1 + 1 (argument là Int), 1.1 + 2.2 (argument là Float), Điều này có nghĩa function `+` đồng thời có các types: `(+) :: Int -> Int -> Int` và `(+) :: Float -> Float -> Float`. Type signature chuẩn của function `+` là `(+) :: (Num a) => a -> a -> a` có nghĩa là nó nhận vào 2 tham số biến kiểu (type variable) a và trả về một biến kiểu a thuộc lớp kiểu (type class) Num. Một vài type class quan trọng khác trong Haskell là `Eq`, `Show`, `Ord`, `Floating`, `Num`, `Integral`, `Fractional`