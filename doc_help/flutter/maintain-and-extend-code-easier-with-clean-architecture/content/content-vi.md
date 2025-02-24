
# Bảo Trì và Mở Rộng Code Flutter Dễ Dàng Hơn Với Kiến Trúc Clean Architecture

## 1. Những Vấn Đề Thường Gặp Khi Lập Trình Ứng Dụng

Khi phát triển phần mềm, đặc biệt là các dự án lớn, bạn có thể đối mặt với những vấn đề như:

- **Khó khăn trong bảo trì**: Khi ứng dụng có nhiều tính năng và lỗi xảy ra, việc sửa chữa có thể mất nhiều thời gian truy tìm lỗi.
- **Phát triển tính năng mới**: Việc thêm tính năng mới có thể ảnh hướng để luồng chạy của ứng dụng, cần 1 biện pháp đảm bảo chắc chắn khi thêm tính năng mới, các luông code cũ không bị ảnh hưởng nhiều.
- **Mở rộng quy mô dự án**: Dự án lớn nhưng nguồn nhân lực hạn chế đòi hỏi cách tổ chức code tốt để 1 người có thể quản lý được nhiều phần trong dự án.
- **Hợp tác dễ dàng hơn**: Code rõ ràng và có cấu trúc giúp các thành viên dễ dàng bàn giao hoặc phối hợp.

## 2. Tại sao nên sử dụng Kiến Trúc Clean Architecture cho Flutter

### Lợi Ích Chính, chúng giải quyết được các vấn đề nếu trên.

Kiến trúc **Clean Architecture** là giải pháp hữu hiệu giúp tổ chức code khoa học hơn, với những ưu điểm nổi bật như:

- **Tăng tính linh hoạt**: Các thành phần dễ thay đổi nhất được đặt ở lớp ngoài cùng, giúp việc thay đổi, thêm mới API hoặc truy xuất dữ liệu đơn giản hơn trong quá trình bảo trì. Các tính năng được tách biệt nên đồng nghiệp có thể dễ dàng phát triển các tính năng của riêng mình mà không lo xung đột code với các tính năng mà các đồng nghiệp khác đang code.
- **Quản lý module hiệu quả**: Mỗi module được thiết kế riêng biệt, dễ bảo trì và mở rộng, các tính năng sẽ được để trong 1 thư mục riêng biệt và chúng không ảnh hưởng đến nhau. 

![maintain-and-extend-code-easier-with-clean-architecture-image-1.png](..%2Fimage%2Fmaintain-and-extend-code-easier-with-clean-architecture-image-1.png)

### Cấu Trúc Kiến Trúc và tôi đã áp dụng.
- **Domain (Cốt lõi)**: Tầng sâu nhất, chứa logic nghiệp vụ chính, ít bị thay đổi.
- **Presentation (Giao diện)**: Tầng trung gian, xử lý dữ liệu từ domain và hiển thị lên UI.
- **Data (Dữ liệu)**: Tầng ngoài cùng, chịu trách nhiệm về API hoặc lưu trữ dữ liệu.

Nguyên tắc chính:
1. **Tầng trong không phụ thuộc tầng ngoài**: Domain không được lấy dữ liệu từ Presentation hoặc Data.
2. **Tầng ngoài có thể truy xuất tầng trong**: Data có thể gọi đến Domain hoặc Presentation khi cần.


## 3. Triển Khai Clean Architecture Trong Flutter
![maintain-and-extend-code-easier-with-clean-architecture-image-2.png](..%2Fimage%2Fmaintain-and-extend-code-easier-with-clean-architecture-image-2.png)

### 3.1. Cấu Trúc Thư Mục

Dự án Flutter thường được tổ chức như sau:

```plaintext
lib/
  ├── features/          # Chứa các module chính
  │     ├── module_1/    # Mỗi module có ba tầng: Data, Domain, Presentation
  │     └── module_2/
  ├── ...
  └── main.dart          # Điểm bắt đầu của ứng dụng
```

### 3.2. Chi Tiết Các Tầng

#### Tầng Domain
![maintain-and-extend-code-easier-with-clean-architecture-image-3.png](..%2Fimage%2Fmaintain-and-extend-code-easier-with-clean-architecture-image-3.png)
Đây là tầng sâu nhất, chứa logic nghiệp vụ và ít bị thay đổi. Tầng này thường gồm:
- **Entities**: Các đối tượng thực thể. Tôi cũng có các mẫu code cho các thực thể này, hãy [tham khảo](https://brickhub.dev/bricks/dr_entity) sủ dụng plugin json_serializable, và [tham khảo](https://brickhub.dev/bricks/dr_freezed) sử dụng plugin freezed
- **Use Cases**: Các logic xử lý nghiệp vụ. Một số lập trình viên sẽ bỏ thư mục này vì cảm giác nó khá thừa thãi, theo quan điểm cá nhân tôi thì nó cần thiết khi có thể dễ dàng nhìn thấy các case mà người dùng sử dụng khi muốn truy xuất vào tầng data. Nó sẽ rất hữu ích khi kết hợp với các biểu đồ Diagram trong lập trình.
- **Enums**: Các giá trị liệt kê. Tôi sử dụng rất nhiều enums trong dự án vì sụ thuận tiện của nó. Bạn có thể tham khảo mẫu enum tôi tạo trên mason tại [dr_enum](https://brickhub.dev/bricks/dr_enum).

#### Tầng Presentation

Tầng này xử lý hiển thị giao diện và tương tác người dùng. Thường gồm:
- **Views**: Các màn hình chính và các mảnh màn hình phụ.
- **View Models**: Quản lý trạng thái của màn hình (state management) như [getx](https://pub.dev/packages/get), [bloc](https://bloclibrary.dev/), [riverpod](https://riverpod.dev/).
- **Widgets**: Các widget tái sử dụng. Nếu bạn muốn xây dựng widget để tái sử dụng trong các project khác thì hãy đưa nó vào package, tôi sẽ nói rõ hơn trong bài viết về package

#### Tầng Data
Tầng này dễ thay đổi nhất, chứa các repository để truy xuất dữ liệu. Bao gồm:
- **Constants**: Các hằng số dùng trong module.
- **Repositories**: Quản lý truy xuất dữ liệu từ API hoặc local. Trước đây tôi sử dụng truy xuấy dự liệu từ các thiết bị iot bằng bluetooth, chúng cũng sẽ thuộc tầng này. 

### 3.3. Công Cụ Tự Động Tạo Cấu Trúc

Việc tạo thủ công các thư mục và file có thể mất thời gian, đặc biệt khi khởi tạo module mới. Bạn có thể dùng **Mason** để tự động hóa:

- **Mason** giúp tự động tạo cấu trúc Clean Architecture nhanh chóng.
- Tham khảo công cụ: [Boost your Flutter development efficiency with Mason](https://wongcoupon.com/en/doc/help/flutter/boost-your-flutter-development-efficiency-with-mason).
- Bạn có thể tham khảo template: [dr_clean_arch trên Brickhub](https://brickhub.dev/bricks/dr_clean_arch).


## 4. Triển Khai Clean Architecture Trong Một Dự Án

### Ví Dụ: Module PAYMENT

#### Cấu Trúc Module PAYMENT

```plaintext
lib/features/payment/
  ├── data/
  │     ├── repositories/    # Repository xử lý dữ liệu API
  │     └── constants/       # Các hằng số liên quan đến payment
  ├── domain/
  │     ├── entities/        # Các thực thể dùng để xử lý dữ liệu
  │     ├── usecases/        # Các use case chính (lấy bill, thanh toán)
  │     └── enums/           # Các enum liên quan đến trạng thái thanh toán
  └── presentation/
        ├── views/           # Các màn hình giao diện
        ├── view_models/     # Quản lý trạng thái màn hình bằng BLoC/GetX
        └── widgets/         # Các widget tái sử dụng
```

#### Chi Tiết Các Thành Phần

1. **Domain**:
    - **Entities**: Đối tượng mô tả hóa đơn, khách hàng.
    - **Use Cases**: Các chức năng như xác thực thanh toán, lấy danh sách hóa đơn.
    - **Enums**: Trạng thái thanh toán (chờ xử lý, thành công, thất bại).

2. **Presentation**:
    - **Views**: Hiển thị giao diện thanh toán, và các phần màn hỉnh nhỏ tách riêng để dễ quản lý và code không quá phức tạp.
    - **View Models**: Sử dụng BLoC để quản lý trạng thái.
    - **Widgets**: Các nút và biểu mẫu liên quan đến thanh toán được tái sử dụng lại. 

3. **Data**:
    - **Repositories**: Gọi API thanh toán hoặc lưu dữ liệu offline.
    - **Constants**: Cấu hình endpoint API.


## 5. Tài Liệu Tham Khảo

Đưới đây là tài liệu tôi đã tham khảo:

- [Learn more about Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Flutter Clean Architecture Usage](https://pub.dev/packages/flutter_clean_architecture#usage)
- [Clean Architecture with BLoC](https://medium.com/@yamen.abd98/clean-architecture-in-flutter-mvvm-bloc-dio-79b1615530e1)

[Buy Me a Coffee](https://buymeacoffee.com/ducmng12g) | [Support Me on Ko-fi](https://ko-fi.com/I2I81AEJG8)

