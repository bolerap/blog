+++
title = "Python 3 edges"
draft = true
date = "2016-10-28T17:00:59+07:00"
tags = [ "python 3", "async/await python", "coroutines python", "generator python" ]
categories = [ "python" ]
image = "python3-edge.png"

+++

Python 3.5 came with new features such as coroutines, async/await syntax. Those become natively with python. Before explore them, need to understand something below.

**Generator**

In general a function execution will return a value then will be destroyed. If we call that function more, it'll rerun as the firstly. Generator is a function use to produce data, it can yield a value and stop function excution, The control is returned to where scope that function called. Then we can recall that function and receive new value returned. Notice generator doesn't return a value that return a generator object like when we use iterable, Thus we can use next() on generator object to loop over values or simple use for loop.

Now we can see generator is better in some case as we can use generator instead of range() to reduce memory used, it help avoid run out of memory.

	# Usage generator
	def gen_nums(n):
		num = 0
		while num < n:
			yield num
			num = num + 1

	nums = gen_nums(100000):
	for n in nums():
		print(x)

	# Usage range
	for x in range(1000000):
		print(x)


**Coroutines**

