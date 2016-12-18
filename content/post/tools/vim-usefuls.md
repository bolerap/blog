+++
title = "Vim usefuls"
draft = false
date = "2016-10-23T00:13:54+07:00"
tags = [ "vim", "editor" ]
categories = [ "editor" ]
image = "vim-useful.png"

+++

Vim là 1 trình soạn thảo văn bản nhỏ gọn nhưng cực kì khỏe, nếu được cài đầy đủ plugin nó hoàn toàn có thể sánh ngang các IDE hầm hố. Vim được khá nhiều dev lão luyện dùng điều này cho thấy nó khá tốt và cần đưa vào diện tình nghi ngay nếu chưa biết gì về nó.

Vim mạnh mẽ nhờ hệ thống plugin khổng lồ đáp ứng hầu hết các yêu cầu của các dev, để cài đặt plugin cho vim ta có thể sử dụng nhiều cách, tuy nhiên cách được sử dụng phổ biến và được khuyến khích là sử dụng một plugin manager, plugin manager lại có vài cái tên để lựa chọn như vundle, neobundle, pathogen ... trong số này thì vundle là cái được khuyến khích. Để cài một plugin sử dụng vundle có thể thêm plugin đó vào file .vimrc hoặc khởi động vim và chạy lệnh :PluginInstall name. Dưới đây là danh sách các plugin nên có.

Các plugin hay nên cài trong vim: nerdtree, auto-pairs, neocomplete, syntastic, ack, gitv, fugitive. 

**Các câu lệnh và thao tác hữu ích trong vim**

1. windows

- Chia cửa sổ hiện tại thành 2 cửa sổ nằm ngang ctrl + w, ctrl + S (shift + s).
- Chia cửa sổ hiện tại thành 2 cửa sổ nằm dọc ctrl + w, ctrl + v
- Di chuyển qua lại giữa các cửa số nhấn ctrl+w 2 lần hoặc ấn ctrl+w rồi sử dụng phím di duyển (gồm: hjkl hoặc các phím mũi tên).
- Thay đổi kích thước cửa sổ: 
	+ Horizontal (ngang) gõ lệnh :<number_of_column>winc +/- (tăng/giảm).
	+ Vertical (dọc) gõ lệnh :<number_of_comlumn>winc >/< (tăng/giảm).


