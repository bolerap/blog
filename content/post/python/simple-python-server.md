+++
title = "Simple Python server"
draft = false
date = "2016-10-22T09:07:38+07:00"
tags = [ "http server", "simple server", "Sharing files in LAN" ]
categories = [ "python", "http server", "python tricks" ]
image = "simple-python-server.gif"

+++

Server là từ ngữ khá thần thánh và phổ biến trong giới IT, định nghĩa về server theo wikipedia như sau.

> a server is a computer program or a device that provides functionality for other programs or devices

Hiểu biết sâu về server sẽ rất có ích cho các ITer. Bài viết này sẽ xem xét về ứng dụng http server built-in của python để chia sẻ file trong mạng LAN.

cd tới thư mục muốn chia sẻ rồi chạy lệnh
	
	# python 3
	python -m http.server
	#
	# python 2
	python2 -m SimpleHTTPServer