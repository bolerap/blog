+++
title = "Python tricks"
draft = false
date = "2016-10-20T14:57:11+07:00"
tags = [ "python", "idomatic python" ]
categories = [ "Python" ]

+++

**Unpacking**

	#1
	a, b, c = 1, 2, 3
	d = a, b, c
	print(d, type(d)) # (1, 2, 3) - tuple
	#2
	a, b, c = (2 * i + 1 for i in range(3))
	d = a, b, c
	print(d) # (1, 3, 5)
	#3
	a, (b, c), d = [1, (2, 3), 5]
	print(a, b, c, d) # 1 2 3 5
	#4 - swapping variables
	a, b = 1, 2
	a, b = b, a
	print(a, b) # 2 1
	#5
