+++
title = "Django practices"
draft = false
date = "2016-10-26T11:20:47+07:00"
image = "django-practices.jpg"

+++

**What is django?**

- Lời mô tả súc tích từ https://djangoproject.com.

>The web framework for perfectionists with deadlines

- Django là một MVT framework

**Manage dependencies**

	pip freeze > requirements.txt
	pip install -r requirements.txt

**Project layout**

- Đặt tên app ngắn gọn, súc tích nhất có thể, nên cố gắng đặt tên bằng 1 chữ.
- Sử dụng kiến trúc nhiều app nhỏ thay vì một app lớn.
- Sử dụng triệt để các app built-in và 3rd-party app.

**Settings**

- Sử dụng nhiều file settings cho mỗi env: production, dev, staging, testing.
- Sử dụng file settings cho mỗi user.
- Tất cả các file settings đều kế thừa từ base.
	
	INSTALLED_APP += ('debug_toolbar', )

**Fat models, thin views**

- Cố gắng giữ cho views đơn giản nhất có thể.

**Deployment**

- Webserver: nginx + gunicorn + supervisord + docker.
- Static server: nginx, or amazone S3, CDN.

**Asynchronous**

- Sử dụng celery để thực thi các task async.
- Sử dụng redis làm message queue cho celery.

**Monitor error**

- Sử dụng triệt để python logger.
- Sử dụng sentry.

**Debugging**

- Debug sử dụng trực tiếp của pycharm.
- Sử dụng thêm django-debug-toolbar.


