Bảo trì và mở rộng code dễ dàng hơn với kiến trúc Clean Architecture

1 Các vấn đề gặp phải khi lập trình ứng dụng:
1.1 Cần bảo trì code khi có các vấn đề lỗi sảy ra trong 1 dự án lớn có nhiều tính năng
1.2 Cần phát triển thêm các tính năng mới theo yêu cầu đặc biệt của khác hàng
1.3 có thể mở rộng ra cho các dự án lớn với nguồn nhân lực hạn chế,
1.4 Dễ dàng bàn giao hay hợp tác cũng các đồng nghiệp khác.

2 Kiến trức clean architecture
Các ưu điểm khác phục các vấn đề nêu trên
2.1 Kiến trức này hướng các thành phần code dễ thay đổi nhất ra các lớp ngoài cùng, nên việc thay đổi API hay thay đổi cách truy xuất dữ liệu sẽ vô cùng dễ dàng.
2.2 Đảm nhiệm quản lý module chức năng lớn riêng biệt
![img.png](img.png)

3 Hãy xem một ví dụ được triển khai kiến trức này trong flutter
![image1.png](image1.png)
3.1 tất cả các tính năng chính của ứng dụng được triển khai trong thư mục features trong vùng 1 trong hình ảnh, mỗi module lớn sẽ được chiến trai theo kiến trúc clean architecture trong khu vực 2 trong hình ảnh
3.2 Vấn đề lớn sảy ra ở đây là sẽ có rất nhiều thư mực vào file cần được tạo trước khi triển trai 1 module tính năng mới, tôi đã sử dụng mason đẻ giúp tôi sinh ra các file và thư mực đó. Nếu bạn chưa biết gì về mason, hãy tham khảo bài viết https://wongcoupon.com/en/doc/help/flutter/boost-your-flutter-development-efficiency-with-mason.
3.3 Bạn có thể tham khảo dr_clean_arch của tôi xây dụng tại https://brickhub.dev/bricks/dr_clean_arch, các hướng dẫn sử dụng được viết sẵn trong HELP.md sau khi tạo code bằng mason.
Kiến trúc của tôi chia làm 3 tầng chính: tầng data, tầng domain, tầng presentation. Nếu dựa vào kiến trúc clean architecture lớp sâu nhất sẽ là domain tiếp theo là presentation cuối cùng là lớp data.
Theo nguyên tác của kiến trúc này thì lớp trong sẽ không trỏ hay lấy dữ liệu từ bất kỳ lớp ngoài nào. Vd lớp domain sẽ không truy xuất bầy kỳ dữ liệu nào từ tầng presentation, và càng không được phép với tầng data,
nhưng ngược lại thì tầng data và sẽ được phép truy xuất dữ liệu từ 2 tầng bên trên nó là domain và presentation.

![image2.png](image2.png)
3.4 Chi tiết tầng domain.
Đây là tầng sau nhất trong kiến trúc, là tầng ít bị thay đổi nhất của dự án. Nếu đối chiếu với sơ đồ thì nó chứa 2 lớp chính là Entities và Use Cases.
Trong mẫu code của tôi, tôi có 3 thưc mục chính là
thư mục entities là nơi chứa các đối tượng của các thực thể. Tôi cũng có các mẫu code cho các thực thể này, hãy tham khảo tại https://brickhub.dev/bricks/dr_entity sủ dụng plugin json_serializable, và https://brickhub.dev/bricks/dr_freezed sử dụng plugin freezed
thư mục enums là nơi chứa các enum của dự án. Tôi sử dụng rất nhiều enums trong dự án vì sụ thuận tiện của nó. Bạn có thể tham khảo mẫu enum tôi tạo trên mason tại https://brickhub.dev/bricks/dr_enum.
thư mục usecases là nơi chứa các usecase của module tính năng. Một số lập trình viên sẽ bỏ thư mục này vì cảm giác nó khá thừa thãi, theo quan điểm cá nhân tôi thì nó cần thiết khi có thể dễ dàng nhìn thấy các case mà người dùng sử dụng khi muốn truy xuất vào tầng data.
Nó sẽ rất hữu ích khi kết hợp với các biểu đồ Diagram trong lập trình.

3.5 Chi tiết tầng persentation.
Tầng này tương ứng với tầng controller, gateways, presenters trong kiến trúc. Ở tầng này chúng ta sẽ thường triển khai các plugin quản lý trạng thái của úng dụng như getx https://pub.dev/packages/get, bloc https://bloclibrary.dev/, riverpod https://riverpod.dev/.
Mỗi thư viện quản lý trạng thái lại có 1 điểm mạnh điểm yếu riêng. Kiến trức clean architecture thường áp dụng cho các dự án lớn, nên nó thường triển khia cùng bloc, riverpod. Theo đánh giá của tôi thì việc lựa chọn giữa bloc hay riverpod đều cho hiệu năng như nhau vì chúng đều xây dựng dựa trên Provider https://pub.dev/packages/provider
Hiện tại tôi đang sử dụng Bloc để triển khai quản lý trạng thái, tôi sẽ nói rõ hơn cách triển khai Bloc của tôi trong các phần sắp tới. Phần này sẽ được chia làm 3 thư mục con theo kiến trức MVVM:
Thư mục view_models sẽ chứa các trình quản lý trạng thái màn hình,
thư mực views chứa các màn hình và phần con của màn hình đó  
Thư mục widgets chưá các widget có thể tái sử dụng lại được không chỉ trong module này mà có thể sủ dụng trong các module khác. Nếu bạn muốn xây dựng widget để tái sử dụng trong các project khác thì hãy đưa nó vào package, tôi sẽ nói rõ hơn trong bài viết sắp tới.

3.6 Chi tiết tầng data.
Tầng này chứa các phần dễ thay đổi và thêm mới nhất của project, như thay đổi API, thay đổi cách lấy dữ liệu từ online sang local, thêm các tính năng mới... Nó sẽ được chia thành 2 thư mục chính
Thư mực repositories chứa các repository truy xuất vào các dữ liệu từ API hoặc local, đôi khi có thể chứa các các plugin mà bạn lấy từ trên pub.dev. Chúng ta thường phải tách riêng chúng vào 1 class phòng tường hợp thư viện đó không cón được hỗ trợ trong tương lai hoặc thay thế 1 thư viện khác nhằm tránh thay đổi nhiều phần của project.
Thư mục constant chứa các const dữ module. Vd như setting mặc định, các câu query GraphQL

Trên đây là trình bầy của tôi về cách tôi triển khai kiến trúc clean architecture, nó có thể hoặc không phù hợp với project của bạn đang làm, nhưng nó sẽ cho bạn 1 góc nhìn về kiến trúc này. Hãy dựa vào nó để tự xây dựng cho bản thân mình 1 mẫu code cho riêng mình sử dụng mason. Bạn có thể tham khảo bài viết tạo mẫu code của tôi bằng mason tại https://wongcoupon.com/en/doc/help/flutter/boost-your-flutter-development-efficiency-with-mason

Các tài liệu tôi đã tham khảo cho việc xây dựng mẫu kiến trức này là
* Learn more about clean architecture: https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html
* Follow architecture: https://pub.dev/packages/flutter_clean_architecture#usage
* Follow clean architecture with bloc: https://medium.com/@yamen.abd98/clean-architecture-in-flutter-mvvm-bloc-dio-79b1615530e1


4 Sau đây tôi sẽ trình bày về cách cá nhân tôi triển khai vào 1 dự án thực tế như nào.

![image3.png](image3.png)


Tôi có 1 module PAYMENT chịu trách nghiệm xử lý toàn bộ các chức năng liên quan đến thanh toán của ứng dụng.
module này sẽ được chia ra 3 folder chính là data, domain, presentation như đã trình bày ở trên.

4.1 Chi tiết domain
![image4.png](image4.png)

Đây là phần lõi, ít thay đổi nhất trong module PAYMENT, trong thư mục entities chứa các modle cần sử dụng để call API nhận dữ liệu từ server được chứa trong 2 folder con là requests và responses. Folder states chứa các đối tượng quản lý việc cài đặt của setting payment.
Thư mục enums chứa các enum trạng thái, lựa chọn, cài đặt của module PAYMENT,
Thư mục usecases chứa các usecase của module này gồm có lấy bill, update giá và xác thực thanh toán với server.

4.2 Chi tiết presentation
![image5.png](image5.png)

Đây là phần hiển thị giao diện và quản lý trạng thái cho người dùng.
Trong thư mục view_models tôi sử dụng bloc để quản lý trạng thái của ứng dụng. Kiến trúc sử dụng MVVM, bạn cũng có thể áp dụng kiến trúc MVC, MVP tuỳ thích.
Trong thư mục views tôi quản lý các màn hình view chính, mỗi màn hình view chính sẽ có các màn hình nhỏ và các phần màn hình con có thể được sử dụng lại nhiều lần.
Trong thư mục widgets chứa các widget được tuỳ chỉnh UI để sử dụng lại ở nhiều nơi, hoặc những làm listen củ bloc riêng biệt.

4.3 Chi tiết thư mục data
![image6.png](image6.png)

Thư mục chứ các const và repositories để lấy các dữ liệu qua API. Tầng presentation sẽ giao tiếp với repositories của tầng data qua usecase. Đây chính là tác dụng của usecase khi chúng ta có thể dễ dàng thay các repository khác một cách nhanh chóng mà không ảnh hưởng đến các phần khác trong project 