+++
date = "2016-11-20T17:42:39+07:00"
title = "Array, slice, string - mechanic of append"
draft = true

+++

**Introduction**

Một trong những tính năng phổ biến của các ngôn ngữ lập trình thủ tục là khái niệm array. Array có vẻ như là điều đơn giản nhưng có khá nhiều câu hỏi cần được trả lời khi thêm chúng vào một ngôn ngữ như: 

	- fixed size or variable size?
	- is the size part of the type?
	- what do multidimensional arrays look like?
	- does the empty array have meaning?

Những câu trả lời cho những câu hỏi trên ảnh hưởng tới việc array chỉ là một tính năng của ngôn ngữ hay nó là một phần cốt lõi của thiết kế.

Trong bản phát triển của go, mất khoảng một năm để trả lời những câu hỏi trên trước khi đưa ra thiết kế chuẩn. Bước đi chính chính là việc giới thiệu slice, slice được built on fixed size array, một cấu trúc dữ liệu linh hoạt và có thể mở rộng. cho tới hôm nay nhiều lập trình viên golang mới thường hiểu sai cách slice hoạt động.

Bài viết này chúng ta sẽ cố gằng làm sáng tỏ sự mơ hồ này. Chúng ta sẽ cùng xây dựng những đoạn code để giải thích cách built-in function append làm việc.

**Array**

Array là một phần khá quan trọng trong go. Giống như nền tảng của một ngôi nhà chúng thường ẩn dưới những thành phần khác. chúng ta phải nói về array trước khi tìm hiểu về slice. 

Array không được sử dụng thường xuyên trong go vì size của array is a part of type, điều này làm hạn chế khả năng diễn đạt của nó. Ta khai báo một biến mảng có tên buffer chứa 256 byte như sau.

	var buffer [256]byte

Một array với [512]byte sẽ khác với type [256]byte type.

Dữ liệu liên kết với một array là một array of elements nó được mô tả như sau trong bộ nhớ.

	buffer: byte byte byte ... 256 times

Đó là một biến chứa 256 byte dữ liệu, chúng ta có thể truy cập các phần tử của mảng thông qua index như `buffer[0]` `buffer[5]`

**Slice - slice headers**

Slice được sử dụng khá thường xuyên trong go, vì vậy để sử dụng chúng tốt ta cần hiểu nó là gì và nó hoạt động ra sao. 

Một slice là một cấu trúc dữ liệu mô tả một đoạn liên tiếp của một array được lưu trữ riêng biệt với chính slice đó. Slice không phải là một array, slice mô tả một đoạn dữ liệu của một array.

Ví dụ chúng ta có thể tạo một slice từ array buffer ta đã tạo trước đây từ phần tử thứ 100 đến phần tử thư 149. 

	var slice []byte = buffer[100:150]

Chúng ta cũng có thể viết theo cách idomatic go như nhau

	var slice = buffer[100:150] // or
	slice := buffer[100:150]

Vậy chính xác thì slice variable là gì? Nó không phải là toàn bộ câu chuyện, bây giờ chúng ta hãy nghĩ slice như một cấu trúc dữ liệu nhỏ với hai phần tử, một là length của slice, một là con trỏ trỏ tới các phần tử của một array, ta có thể hình dung như sau.

	type sliceHeader struct {
		Length int
		ZerothElement *byte
	}

	slice := sliceHeader {
		Length: 50,
		ZerothElement: &buffer[100],
	}

Trước đây ta mới chỉ slice một array, ta hoàn toàn có thể slice một slice, ví dụ

	slice2 := slice[5:10]

Cũng giống như trước đây, lệnh này sẽ tạo ra một slice mới mô tả các phần tử từ vị trí thứ 5 tới vị trí thứ 10 trên underlying array của slice hiện tại (là array `buffer`), cụ thể là từ `buffer[105]` đến `buffer[109]`, `slice2` lúc này có dạng.

	slice2 := sliceHeader {
		Length: 5,
		ZerothElement: &buffer[105]
	}

Ta cũng có thể reslice lại chính slice đó như việc tạo mới `slice2` ở trên. 

	slice := slice[5:10]

Nếu bạn đã từng nghe những lập trình viên go có kinh nghiệm nói về slice header, thì nó chính là điều chúng ta vừa tìm hiểu ở trên, và thực tế một biến slice chỉ lưu trữ slice header mà thôi. ví dụ khi ta gọi một function nhận vào tham số là một slice thì chính slice header đó được truyền vào function. 

**Passing slices to functions**

Điều quan trọng là phải hiểu rằng mặc dù một slice chứa một pointer nhưng chính bản thân nó là một giá trị (value). Theo đó nó là một struct value chứa một pointer và một length, không phải là pointer to a struct. 

Phần trước ta có nói về việc truyền slice vào mỗt function, thì một bản copy của slice header của sẽ được truyền vào function, ví dụ.

	func AddOneToEachElement(slice []byte) {
		for i := range slice {
			slice[i]++
		}
	}

Mặc dù slice header được truyền bởi value, nhưng slice header chứa một pointer tới các phần tử của một array. Vì vậy cả origin slice header và copy of slice header được truyền vào function đều mô tả trên cùng một array. Tuy nhiên argument thực sự là bản copy ta cùng xem ví dụ.

	func SubtractOneFromLength(slice []byte) []byte {
		slice = slice[0:len(slice)-1]
		return slice
	}

	func main() {
		var slice []byte
		fmt.Println("Before: len(slice) = ", len(slice)) // 50
		newSlice := SubtractOneFromLength(slice)
		fmt.Println("After: len(slice) = ", len(slice)) // 50
		fmt.Println("After: len(newSlice) = ", len(newSlice)) // 49
	}

Như ta thấy nội dung của slice argument có thể bị chỉnh sửa bởi một function nhưng header của slice đó thì không, length trong slice header không bị chỉnh sửa vì bản copy của slice header được truyền vào chứ không phải origin. bởi vậy nếu ta muốn viết một function chỉnh sửa slice header ta phải trả về kết quả của function đó như ta vừa làm ở trên là newSlice. 

**Pointer to slice - Method receiver**

Một cách khác để viết một function chỉnh sửa slice header là truyền vào function một pointer trỏ tới slice header.

	func PtrSubtractOneFromLength(slicePtr *[]byte) {
		slice := *slicePtr
		*slicePtr = slice[0:len(slice)-1]
	}

	func main() {
		var slice []byte
		fmt.Println("Before: len(slice) = ", len(slice))
		PtrSubtractOneFromLength(&slice)
		fmt.Println("After: len(slice) = ", len(slice))
	}

Cách trên trông có vẻ không idiomatic go lắm, ta cùng viết lại theo cách idomatic golang như sau.

	type path []byte

	func (p *path) TruncateAtFinalSlash() {
		i := bytes.LastIndex(*p, []byte("/"))
		if i >= 0 {
			*p = (*p)[0:i]
		}
	}

	func main() {
		pathName := path("/usr/bin/tso") // conversion from string to path
		pathName.TruncateAtFinalSlash()
		fmt.Printf("%s\n", pathName)
	}

Mặt khác nếu ta muốn viết một method cho `path` làm công việc đổi các kí tự ascii thành kí tự hoa trong `path`, thì method nên là value receiver vì value receiver sẽ vẫn trỏ tới cùng underlying array.

**Capacity**

Cùng xem xét function mở rộng thêm một phần tử cho slice dưới đây

	func Extend(slice []int, element int) []int {
		n := len(slice)
		slice := slice[0:n+1]
		slice[n] = element
		return slice
	}

	func main() {
		var iBuffer [10]int
		slice := iBuffer[0:0]
		for i := 0; i < 20; i++ {
			slice = Extend(slice, i)
			fmt.Println(slice)
		}
	}

Ta chạy code trên đến khi slice đạt 10 phần tử sẽ sinh ra lỗi, lý do vì sao vậy nhỉ? Nguyên nhân là do một thành phần thứ ba trong slice header gọi là capacity. Lúc này slice header có dạng.

	type sliceHeader struct {
		Length int
		Capacity int
		ZerothElement *byte
	}

`Capacity` cho ta biết số giới hạn số phần tử mà underlying array có thể lưu trữ. ở trên khi ta gán `slice := iBuffer[0:0]` thì slice header sẽ như sau.

	slice := sliceHeader {
		Length: 0,
		Capacity: 10,
		ZerothElement: &iBuffer[0],
	}

`Capacity` nói theo khác chính là length của underlying array

**Make**

Ta lại có vấn đề mới, nếu chúng ta muốn mở rộng slice vượt quá capacity thì làm thế nào, theo định nghĩa, capacity là giới hạn của sự gia tăng phần tử của slice. Tuy nhiên ta có thể đạt được kết quả tương tự bằng việc cấp phát một array mới, rồi copy dữ liệu sang array mới và chỉnh sửa mô tả của slice sang array mới.

Chúng ta bắt đầu với việc cấp phát. Chúng ta có thể sử dụng builtin function `new` để cấp phát một array lớn hơn và tạo một slice header mô tả trên array này. `make` function nhận vào ba tham số là: kiểu của slice, length và capacity, ví dụ.

	slice := make([]int, 10, 15)
	newSlice := make([]int, len(slice), 2*cap(slice))
	for i := range slice {
		newSlice[i] = slice[i]
	}
	slice = newSlice

**Copy**

Khi chúng ta nhân đôi capacity của slice ở ví dụ trên ta sử dụng vòng lặp để copy dữ liệu từ slice cũ sang slice mới. Go hỗ trợ một builtin function là `copy` giúp công việc chuyển đổi dữ liệu rễ ràng hơn, function này nhận vào hai tham số là hai slice rồi copy dữ liệu từ slice bên phải sang slice bên trái, ví dụ

	newSlice := make([]int, len(slice), 2*cap(slice))
	copy(newSlice, slice)

`copy` function khá thông minh, nó chỉ copy những cái có thể và chú ý tới length của cả 2 slice, Theo cách khác số lượng phần tử nó copy là tối thiểu của length của 2 slice. Điều này giúp tiết kiệm nhiều thứ, Hơn nữa `copy` trả về một integer value, là số phần tử đã được copy.

`copy` cũng có thể được dùng đề dịch chuyển một phần tử như sau.

	func Insert(slice []int, index, value int) []int {
		// grow slice by one element
		slice := slice[0:len(slice)+1]
		copy(slice[index+1:], slice[index:])
		slice[index] := value
		return slice
	}

**Append**

Trước đây ta đã viết `Extend` function để mở rộng slice thêm một phần tử. Function đó có bug, function `Insert` cũng vậy, nếu capacity của slice quá nhỏ chương trình của ta sẽ bị crash ngay. Chúng ta cũng fix bug nào. 

	func Extend(slice []int, element int) []int {
		n := len(slice)
		if n == cap(slice) {
			// slice is full, must grow
			// we double its size and add 1, so if the size is zero we still grow
			newSlice := make([]int, len(slice), 2*cap(slice)+1)
			copy(newSlice, slice)
			slice = newSlice
		}
		slice = slice[0:n+1]
		slice[n] = element
		return slice
	}


Với function `Extend` mới ta vừa viết, ta có thể cải thiện để mở rộng slice với nhiều phần tử cùng lúc. Để làm vậy ta sẽ sử dụng khả năng chuyển cái danh sách tham số của hàm thành một slice khi function được gọi, tính năng này gọi là variadic function trong go.

Ta gọi function mới này là `Append`, phiên bản đầu tiên ta có thể gọi function `Extend` ở trên nhiều lần bên trong `Append`, mã lệnh của Append function trông như sau.

	func Append(slice []int, items ...int) []int {
		for _, item := range items {
			slice = Extend(slice, item)
		}
		return slice
	}

Chúng ta có thể làm cho `Append` hiệu quả ho8n bằng cách chỉ cấp phát một lần.

	// Append appends the elements to the slice
	// Efficient version
	func Append(slice []int, elements ...int) []int {
		n := len(slice)
		total := len(slice) + len(elements)
		if total > cap(slice) {
			// Reallocate, grow to 1.5 times the new size, so we can still grow
			newSize := total*3/2 + 1
			newSlice := make([]int, total, newSize)
			copy(newSlice, slice)
		}
		slice = slice[:total]
		copy(slice[n:], elements)
		return slice
	}

**Append - builtin function**

Trên đây chúng ta đã xây dựng Append function khá hiệu quả, tuy nhiên đó chỉ là tiền đề cho việc ra đời của Append builtin làm việc với mọi kiểu của slice. 

Một điểm yếu của go là bất kì generic-type operation phải được cung cấp bởi run-time. Một ngày nào đó nó có thể thay đổi nhưng bây giờ để làm việc với slice dễ dàng hơn, go cung cấp một builtin function `append`. 

Cần nhớ rằng slice header luôn được cập nhật bởi lời gọi append function, bạn cần lưu lại slice được trả về khi gọi append, Trong thực tế trình biên dịch không cho phép bạn gọi append mà không lưu lại kết quả trả về. ví dụ

	// create couple of starter slices
	slice := []int{1, 2, 3}
	slice2 := []int{55, 66, 77}

	// Add item to a slice
	slice = append(slice, 4) 

	// Add slice to slice
	slice = append(slice, slice2...)

	// make a copy of a slice
	slice3 := append([]int(nil), slice...)

	// copy a slice to the end of itself
	slice = append(slice, slice...)

**Nil**

Một nil slice có slice header như sau

	sliceHeader {
		Length: 0,
		Capacity: 0,
		ZerothElement: nil,
	}

hay đơn giản là `sliceHeader{}`. Chi tiết chính là element pointer là `nil`. Để hiểu nil slice ta cùng xem xét một slice khác được tạo bởi `array[0:0]` slice này có leng và cap = 0 nhưng pointer của nó không nil vì thế nó không phải là nil slice. Rõ ràng là một empty slice có thể grow vì nó có underlying array (như trên là `array[0:0]`) còn một nil slice thì không thể grow vì nó không có underlying array để lưu giá trị.

Điều này nói lên rằng một nil slice là có chức năng tương tự một zero-length slice, mặc dù nó không trỏ đến đâu cả. Nó có length và có thể append được với việc cấp phát.

**Strings**

String thực sự rất đơn giản, nó chỉ là một read only byte slice với vài cú pháp mở rộng hỗ trợ bởi ngôn ngữ lập trình. 

Bởi vì chúng là read only, vì thế không cập một capacity (vì ta không thể grow chúng).

	// yield the byte valuye '/'
	slash := "/usr/ken"[0]

Chúng ta có thể slice một string thành một sub string như sau.

	// yield the string "/usr"
	usr := "/usr/ken"[0:4]

Chuyển đổi byte slice thành string và ngược lại như sau.

	str := string(usr)
	slice := []byte(usr)

Underlying array của một string bị ẩn và không thể truy cập nội dung của nó ngoại trừ việc thông qua string, điều đó có nghĩa là khi chúng ta thực hiện những thao tác chuyển đổi (conversion) thì một bản copy của array được tạo ra. Go sẽ lo việc này, ta  không cần bận tâm. Sau khi thực hiện những thao tác chuyển đổi, chỉnh sửa underlying array thì byte slice không ảnh hưởng tới string tương ứng.

Một hệ quả quan trọng của slice-like design cho string là việc tạo một substring rất hiệu quả. Tất cả những gì cần làm là tạo ra một string header. Vì string là read only, nên original string và result string từ việc thao tác slice có thể chia sẻ cùng một array một cach an toàn.

**Conclusion**

Hiểu cách slice hoạt động sẽ giúp cho việc hiểu cách chúng được cài đặt. Có một cấu trúc dữ liệu nhỏ, slice header liên kết với slice variable, và header này mô tả một phẩn của một array riêng biệt đã được cấp phát. Khi chúng ta truyền slice value, bản sao của header được truyền vào nhưng nó vẫn trỏ tới cùng một underlying array
