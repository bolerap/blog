+++
title = "createClass vs Component in React"
draft = false
date = "2016-10-20T15:55:47+07:00"
tags = [ "React", "Reactjs", "React Component" ]
categories = [ "Ecmascript" ]

+++

Để tạo một react component ta có thể dùng cú pháp 

	// cách 1
	const = react.createClass({
		getInitialState() {
			return {name: 'David'}
		},

		propTypes: {},

		getDefaultProps() {
			return {}
		},

		handleClick() {
			console.log(this);
		},

		render() {
			return (
				<div>
					<h1>Hello, {this.state.name}</h1>
				</div>
			);
		}
	});

hoặc 
	
	// cách 2
	class Comment extends React.Component {
		constructor(props) {
			super(props);
			this.state = {
				name: 'David'
			};
			this.handleClick = this.handleClick.bind(this);
		}

		handleClick() {
			console.log(this);
		}

		render() {
			return (
				<div>
					<h1>Hello, {this.state.name}</h1>
				</div>
			);
		}
	}

	Comment.propTypes = {};
	Comment.defaultProps = {};

Về bản chất hai cú pháp đều thực hiện công việc giống nhau, cách 1 sử dụng cú pháp ES5 cách 2 sử dụng cú pháp ES6. Vài điểm khác nhau giữa 2 cách như sau: 

- initState: cách 1 dùng function getInitialState(), cách 2 set object trong constructor.
- defaultProps: cách 1 là sử dụng function getDefaultProps(), cách 2 set object defaultProps.
- handler Event: cách 1 tự động bind vào component khi gọi, cách 2 phải bind manual hoặc bind trong constructor.
- mixin: cách 1 hỗ trợ, cách 2 không hỗ trợ