+++
title = "Haskell Fundamentals"
draft = false
date = "2016-11-20T08:37:59+07:00"
tags = [ "haskell", "haskell learn" ]
categories = [ "haskell" ]
image = "haskell.jpg"

+++

Các function chúng ta định nghĩa cho tới nay bị giới hạn trong ở những thao tác cơ bản như cách gia tăng một số, Trong chương này chúng ta sẽ thảo luận về một số chức năng nâng cao và xem xét về các thao tác trên list. 

**Program are composed from modules**

Thông thường một chương trình bao gồm nhiều function và định nghĩa kiểu (type definition). Rõ ràng đặt chúng trong một file là 1 ý tưởng tồi tệ. Bởi vậy các ngôn ngữ lập trình hiện đại cung cấp vài công cụ để cấu trúc chương trình bằng cách cho phép nhóm các related definition vào một logic unit được lưu trữ trong 1 file riêng. Trong haskell những unit này gọi là module.

Ví dụ về định nghĩa một module trong haskell.
{{< gist thanhngvpt 75c8695b03afea2a2961a431b2e602cd >}}
Module bắt đầu với một header là một comment chứa các thông tin, tác giả, ngày tạo, mô tả ngắn gọn về chức năng của module. Dòng đầu tiên bắt đầu bởi keyword module theo sau là tên của module, dòng tiếp là keyword where và các definition thuộc về module. Lưu ý là tên module trái ngược với tên function và tên biến là phải bắt đầu bằng kí tự hoa. Trong haskell có một module đặc biệt tên là Prelude mà nội dung luôn sẵn có (available). `Prelude` chứa tất cả các function được định nghĩa trước trong haskell như `+` `*` ...

**Branches in the control flow**

Cho tới nay các ví dụ của chúng ta đều thực hiện mà không bị ràng buộc bởi điều kiện (conditional), phần nay chúng ta sẽ tìm hiểu về điều kiện trong haskell. Cùng xem xét ví dụ sau về sử dụng conditional trong haskell. 
{{< gist thanhngvpt 70c93748bd09994625e59ab264485eff >}}

**Binder**

Một binder sẽ bind value tới một name, và sau đó ta có thể truy cập value thông qua name. 
{{< gist thanhngvpt 7f93e3621a2866445c5b9133fe32e339 >}}

**Tuple combinding different data items**

Cho tới nay chúng ta mới chỉ biết cách truyền nhiều tham số vào function nhưng chưa biết cách tạo function trả về nhiều value. Chúng ta có thể thực hiện điều này bằng cách sử dụng tuple.
Một tuple sẽ kết hợp nhiều thành phần thành một compound value, tuy nhiên việc kiến tạo một compound value mới chỉ là một nửa câu chuyện, nửa còn lại ta cần decompound, để làm làm vậy haskell hỗ trợ sẵn ta 2 function là fst và snd. Với fst và snd ta không sử dụng một variable để tham chiếu đến toàn bộ compound value thay vào đó ta  phải phân tách cặp thành các thành phần `x` `y` quá trình này được gọi là decomposition pattern matching