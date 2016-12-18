+++
title = "Multiple monitor i3wm"
draft = false
date = "2016-10-27T15:42:47+07:00"
tags = [ "tilling window manager", "i3wm" ]
categories = [ "linux", "window manager" ]
image = "multiple-monitor-i3wm.png"

+++

**What is i3?**

>i3 is a tiling window manager, completely written from scratch. The target platforms are GNU/Linux and BSD operating systems, our code is Free and Open Source Software (FOSS) under the BSD license. i3 is primarily targeted at advanced users and developers. Based upon the experiences we made when wanting to hack/fix wmii, we agreed upon the following goals for i3

**Working with multiple monitor**

- Kiểm tra xem monitor đã được kết nối vào hệ thống chưa. Mở terminal và chạy lệnh.

	`xrandr`

được thông tin dạng như sau

	Screen 0: minimum 8 x 8, current 2732 x 768, maximum 32767 x 32767
	LVDS1 connected 1366x768+1366+0 (normal left inverted right x axis y axis) 310mm x 170mm
	   1366x768      60.10*+
	   1024x768      60.00  
	   1024x576      60.00  
	   960x540       60.00  
	   800x600       60.32    56.25  
	   864x486       60.00  
	   640x480       59.94  
	   720x405       60.00  
	   680x384       60.00  
	   640x360       60.00  
	DP1 disconnected (normal left inverted right x axis y axis)
	HDMI1 disconnected (normal left inverted right x axis y axis)
	VGA1 connected 1366x768+0+0 (normal left inverted right x axis y axis) 410mm x 230mm
	   1366x768      59.79*+
	   1280x1024     60.02  
	   1280x720      60.00  
	   1024x768      60.00  
	   800x600       60.32  
	   640x480       59.94  
	   720x400       70.08  
	VIRTUAL1 disconnected (normal left inverted right x axis y axis)

- Chạy tiếp lệnh.

		xrandr --ouput VGA1 --auto --left-of LVDS1

- Trong đó: 
	+ VGA1, LVDS, HDMI1 là tên của các monitor device đã connect vào trong hệ thông.
	+ Các tham số: --output, --auto, --left-of quyết định hiển thị của monitor (xem help để biết thêm).


Xem thêm tại [mulitple monitor in i3wm](https://i3wm.org/docs/userguide.html#multi_monitor)