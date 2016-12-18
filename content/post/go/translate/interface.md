+++
title = "Go data structure - interface"
draft = true
date = "2016-11-02T13:28:46+07:00"

+++

Interface trong go lang là static, checked lúc compile, interface là phần thú vị nhất của golang.

**Usage**

Interface trong golang cho phép bạn sử dụng *duck typing* giống như bạn muốn trong các purely dynamic language như Python nhưng compiler vẫn phải bắt các lỗi hiển nhiên như truyền một value int cho phương thức `Read` của một object như nó mong muốn hoặc như việc gọi *Read* method với các arguments sai. Để sử dụng interface, trước tiên phải định nghĩa interface type.

	type ReadCloser interface {
		Read(b []byte) (n int, err os.Error)
		Close()
	}

Và sau đó định nghĩa function để implement `ReadCloser` vừa định nghĩa. Ví dụ function này sẽ gọi `Read` lặp lại để lấy ra tất cả data đã được request và sau đó gọi `Close`.

	func ReadAndClose(r ReadCloser, buf []byte) (n int, err os.Error) {
		for len(buf) > 0 && err == nil {
			var nr int
			nr, err = r.Read(buf)
			n += nr
			buf = buf[nr:]
		}
		r.Close()
		return
	}

Đoạn code trên `ReadAndCloser` có thể truyền một value of any type miễn là nó có các phương thức `Read` và `Close` với right signature. Không giống nhưng các ngôn ngữ động như Python, nếu bạn truyền một giá trị có type sai bạn sẽ nhận một lỗi lúc biên dịch (compile time) không phải lỗi lúc chạy (run time).

Interface không bị giới hạn bởi static checking. Bạn có thể check dynamically có chăng một interface đặc biệt có thêm một method. ví dụ

	type Stringer interface {
		String() string
	}

	func toString(any interface{}) string {
		if v, ok := any.(Stringer); ok {
			return v.String()
		}
		switch v := any.(type) {
		case int:
			return strconv.Itoa(v)
		case float:
			return strconv.Ftoa(v, 'g', -1)
		}
		return "???"
	}

Giá trị any có static type là `interface{}` có nghĩa là không có sự đảm bảo của bất kì method nào, nó có thể chứa bất kì type nào. Câu lệnh if v, ok := any.(Stringer)` kiểm tra có thể convert giá trị any thành interface value Stringer, nếu được thì gọi method String() không thì dùng switch để kiểm tra giá trị any với các basic type. Ví dụ hãy xem xét một kiểu integer 64 bit với method String sẽ print ra giá trị binary.

	type Binary uint64	

	func (i Binary) String() string {
		return strconv.Uitob64(i.Get, 2)
	}

	func (i Binay) Get() uint64 {
		return uint64(i)
	}

	http://research.swtch.com/interfaces