+++
draft = false
title = "Machine learning Introduction"
date = "2016-11-23T14:18:30+07:00"
tags = [ "artificial intelligence", "machine learning" ]
categories = ["ai", "ml"]
image = "machinelearning.png"

+++

**What is machine learning?**

Machine learning là một nhánh của khoa học về các chương trình máy tính có thể tự học và tự cải thiện kinh nghiệm. Việc học ở đây có nghĩa là nhận biết và hiểu về dữ liệu đầu vào và đưa ra quyết định dựa trên những dữ liệu được cung cấp.

Rất khó khăn để cung cấp tất cả những quyết định dựa trên tất cả những đầu vào có thể. Để ngăn chặn vấn đề này, các giải thuật được phát triển. Những giải thuật này xây dựng tri thức từ dữ liệu cụ thể và kinh nghiệm trước đó theo nguyên tắc của lý thuyết xác suất thống kê, logic, tối ưu hóa tổ hợp, tìm kiếm, học tăng cường và lý thuyết điều khiển.

Các giải thuật đã được phát triển thành cở sở của các ứng dụng như:

- Vision processing (Xử lý thị giác).
- Language processing (Xử lý ngôn ngữ).
- Forecasting (Tiên đoán, dự đoán).
- Pattern recognition (Nhận dạng mẫu).
- Game.
- Data mining (Khai phá dữ liệu).
- Expert system.
- Robotics.

Machine learning là một lĩnh vực rộng lớn và nó vượt quá phạm vi của bài viết này. Có vài cách để thực hiện (implement) các kĩ thuật machine learning, tuy nhiên có hai cách phổ biến là học có giám sát (supervised learning) và học không có giám sát (unsupervised learning).

**Supervised learning**

Học có giám sát là việc học một chức năng từ dữ liệu huấn luyện cho sẵn. Một giải thuật học có giám sát phân tích dữ liệu huấn luyện và tạo ra một chức năng đã được suy luận (inferred function). Các ví dụ phổ biến của học có giám sát bao gồm:

- Classifying emails as spam (Phân loại email spam).
- Labeling webpages based on their content (Gán nhãn các trang web dựa trên nội dung của chúng).
- voice recogition (Nhận dạng giọng nói).

Có nhiều giải thuật học có giám sát như neural network, support vector machines (SVMs) and Naive Bayes classifiers.

**Unsupervised learning**

Học không giám sát tạo tiên đoán cho các dữ liệu chưa được gán nhãn mà không có bất kì dữ liệu định nghĩa trước nào để huấn luyện. Học không giám sát là một công cụ cực kì mạnh mẽ cho việc phân tích dữ liệu sẵn có, tìm kiếm các mẫu và xu hướng. Nó thường được sử dụng cho việc nhóm các đầu vào giống nhau thành vào một nhóm logic. Các giải pháp phổ biến cho học không giám sát bao gồm:

- K-means.
- Self-organizing maps.
- Hierarchical clustering.

**Recommendation**

Đề xuất (recommendation) là một kĩ thuật phổ biến cung cấp các đề xuất tương tự dựa trên thông tin người dùng như click, xếp hạng. Ví dụ amazon dùng kĩ thuật này để hiển thị danh sách sản phẩm "có thể bạn quan tâm", facebook sử dụng cho tính năng hiển thị danh sách "những người có thể bạn biết"\

**Classification**

Phân loại (classification) hay còn được biết đến với tên categorization là một kĩ thuật machine learning sử dụng dữ liệu đã biết để xác định cách dữ liệu mới được phân loại vào một tập của các danh mục (categories) đã có. Phân loại là một dạng của học có giám sát.

- Các nhà cung cấp dịch vụ thư điện tự như Yahoo, Gmail sử dụng kĩ thuật này để quyết định một email mới được phân loại là spam hay không? Giải thuật phân loại huấn luận chính nó bằng cách sử dụng các thói quen người dùng khi đánh dấu một email là spam. Theo đó trình phân loại quyềt định một email trong tương lai là spam hay không?
- Itunes sử dụng phân loại để chuẩn bị các danh sách phát.

**Clustering**

Xếp nhóm (clustering) được sử dụng để tạo các nhóm dữ liệu giống nhau dựa trên các đặc điểm chung. Xếp nhóm là một dạng của học không giám sát.

Clustering engine đi qua toàn bộ dữ liệu đầu vào và dựa trên các đặc điểm của dữ liệu nó sẽ quyết định cluster nào sẽ được nhóm lại.
