+++
draft = true
title = "Mode algorithm"
date = "2016-10-21T14:15:20+07:00"
tags = [ "algorithm", "mode algorithm" ]
categories = [ "Algorithm", "Go" ]

+++

Mode algorithm là thuật toán được sử dụng để tìm ra phần tử lặp lại nhiều nhất trong một array. Source dưới đây cài đặt cho thuật toán tìm mode của 1 array sử dụng ngôn ngữ Go.

	// sử dụng slice
	function sliceMode(input []int) int {
		count := make([]input, len(input))
		maxValue := 0
		maxKey := 0
		for i := range input {
			count[input[i]]++
			if count[input[i]] > maxValue {
				maxValue = count[input[i]]
				maxKey = input[i]
			}
		}

		return maxKey
	}

	// sử dụng map
	function mapMode([]input) int {
		count := make(map[int]int)
		maxValue, maxKey := 0, 0
		for i := range input {
			count[input[i]]++
			if count[input[i]] > maxValue {
				maxValue = count[input[i]]
				maxKey = input[i]
			}
		}

		return maxKey
	}