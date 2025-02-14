
# Bảo Trì và Mở Rộng Code Dễ Dàng Hơn Với Kiến Trúc Clean Architecture

## 1. Những Vấn Đề Thường Gặp Khi Lập Trình Ứng Dụng

Khi phát triển phần mềm, đặc biệt là các dự án lớn, bạn có thể đối mặt với những vấn đề như:

- **Khó khăn trong bảo trì**: Khi ứng dụng có nhiều tính năng và lỗi xảy ra, việc sửa chữa có thể mất nhiều thời gian.
- **Phát triển tính năng mới**: Yêu cầu khách hàng có thể đòi hỏi thêm những tính năng đặc biệt đột xuất mà khi thêm mới cần không ảnh hưởng đến các tính năng hiện tại.
- **Mở rộng quy mô dự án**: Dự án lớn nhưng nguồn nhân lực hạn chế đòi hỏi cách tổ chức code tốt để làm việc hiệu quả.
- **Hợp tác dễ dàng hơn**: Code rõ ràng và có cấu trúc giúp các thành viên dễ dàng bàn giao hoặc phối hợp.

---

## 2. Kiến Trúc Clean Architecture

### Lợi Ích Chính

Kiến trúc **Clean Architecture** là giải pháp hữu hiệu giúp tổ chức code khoa học hơn, với những ưu điểm nổi bật như:

- **Tăng tính linh hoạt**: Các thành phần dễ thay đổi nhất được đặt ở lớp ngoài cùng, giúp việc thay đổi, thêm mới API hoặc truy xuất dữ liệu đơn giản hơn trong quá trình bảo trì.
- **Quản lý module hiệu quả**: Mỗi module được thiết kế riêng biệt, dễ bảo trì và mở rộng.

![Sơ đồ Clean Architecture](img.png)

### Cấu Trúc Kiến Trúc

- **Domain (Cốt lõi)**: Tầng sâu nhất, chứa logic nghiệp vụ chính, ít bị thay đổi.
- **Presentation (Giao diện)**: Tầng trung gian, xử lý dữ liệu từ domain và hiển thị lên UI.
- **Data (Dữ liệu)**: Tầng ngoài cùng, chịu trách nhiệm về API hoặc lưu trữ dữ liệu.

Nguyên tắc chính:
1. **Tầng trong không phụ thuộc tầng ngoài**: Domain không được lấy dữ liệu từ Presentation hoặc Data.
2. **Tầng ngoài có thể truy xuất tầng trong**: Data có thể gọi đến Domain hoặc Presentation khi cần.

---

## 3. Triển Khai Clean Architecture Trong Flutter

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

### 3.2. Công Cụ Tự Động Tạo Cấu Trúc

Việc tạo thủ công các thư mục và file có thể mất thời gian, đặc biệt khi khởi tạo module mới. Bạn có thể dùng **Mason** để tự động hóa:

- **Mason** giúp tự động tạo cấu trúc Clean Architecture nhanh chóng.
- Tham khảo công cụ: [Boost your Flutter development efficiency with Mason](https://wongcoupon.com/en/doc/help/flutter/boost-your-flutter-development-efficiency-with-mason).
- Bạn có thể tham khảo template: [dr_clean_arch trên Brickhub](https://brickhub.dev/bricks/dr_clean_arch).

---

### 3.3. Chi Tiết Các Tầng

#### Tầng Domain
Đây là tầng sâu nhất, chứa logic nghiệp vụ và ít bị thay đổi. Tầng này thường gồm:
- **Entities**: Các đối tượng thực thể. Tôi cũng có các mẫu code cho các thực thể này, hãy [tham khảo](https://brickhub.dev/bricks/dr_entity) sủ dụng plugin json_serializable, và [tham khảo](https://brickhub.dev/bricks/dr_freezed) sử dụng plugin freezed
- **Use Cases**: Các logic xử lý nghiệp vụ. Một số lập trình viên sẽ bỏ thư mục này vì cảm giác nó khá thừa thãi, theo quan điểm cá nhân tôi thì nó cần thiết khi có thể dễ dàng nhìn thấy các case mà người dùng sử dụng khi muốn truy xuất vào tầng data. Nó sẽ rất hữu ích khi kết hợp với các biểu đồ Diagram trong lập trình.
- **Enums**: Các giá trị liệt kê. Tôi sử dụng rất nhiều enums trong dự án vì sụ thuận tiện của nó. Bạn có thể tham khảo mẫu enum tôi tạo trên mason tại [dr_enum](https://brickhub.dev/bricks/dr_enum).

#### Tầng Presentation
Tầng này xử lý hiển thị giao diện và tương tác người dùng. Thường gồm:
- **Views**: Các màn hình chính và các phần con.
- **View Models**: Quản lý trạng thái của màn hình (state management) như [getx](https://pub.dev/packages/get), [bloc](https://bloclibrary.dev/), [riverpod](https://riverpod.dev/).
- **Widgets**: Các widget tái sử dụng. Nếu bạn muốn xây dựng widget để tái sử dụng trong các project khác thì hãy đưa nó vào package, tôi sẽ nói rõ hơn trong bài viết sắp tới.

#### Tầng Data
Tầng này dễ thay đổi nhất, chứa các repository để truy xuất dữ liệu. Bao gồm:
- **Constants**: Các hằng số dùng trong module.
- **Repositories**: Quản lý truy xuất dữ liệu từ API hoặc local.

---

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
    - **Views**: Hiển thị giao diện thanh toán.
    - **View Models**: Sử dụng BLoC để quản lý trạng thái.
    - **Widgets**: Các nút và biểu mẫu liên quan đến thanh toán.

3. **Data**:
    - **Repositories**: Gọi API thanh toán hoặc lưu dữ liệu offline.
    - **Constants**: Cấu hình endpoint API.

---

## 5. Tài Liệu Tham Khảo

- [Learn more about Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Flutter Clean Architecture Usage](https://pub.dev/packages/flutter_clean_architecture#usage)
- [Clean Architecture with BLoC](https://medium.com/@yamen.abd98/clean-architecture-in-flutter-mvvm-bloc-dio-79b1615530e1)
