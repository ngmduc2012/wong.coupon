
# **Tạo Base Project trong Flutter**
Bài viết này sẽ hoàn chỉnh khi nó kết hợp với bài viết [Clean Architecture](https://wong-coupon.gitbook.io/flutter/easy-code/flutter-clean-architecture)và [Package](*LINK*).

## **1. Tại Sao Cần Một Base Project?**

Bất kỳ lập trình viên Flutter nào sau khi đã trải qua một vài dự án đều muốn tạo riêng cho mình một mẫu project để khởi tạo nhanh cho các dự án sau này. Tuy nhiên, việc bóc tách những phần có thể tái sử dụng và thiết lập cấu trúc dự án từ đầu có thể mất rất nhiều thời gian và công sức.

Vì vậy, tôi đã tìm hiểu nhiều cách để tự động tạo các file và thư mục cần thiết, trong đó có thể sử dụng lệnh tạo file của Windows. Tuy nhiên, một công cụ mạnh mẽ hơn có thể giúp chúng ta và đồng nghiệp tiết kiệm thời gian là **Mason**. Nếu bạn chưa biết về công cụ này, hãy tham khảo bài viết của tôi tại:  
[Hướng Dẫn Mason trong Flutter](https://wongcoupon.com/en/doc/help/flutter/boost-your-flutter-development-efficiency-with-mason)


## **2. Các Thư Viện Cần Có Trong Base Project**

Dưới đây là danh sách các thư viện quan trọng mà tôi sử dụng để đảm bảo code luôn sạch (clean), dễ mở rộng và bảo trì.

### **2.1. Quản lý dependency với `get_it`**
[get_it trên pub.dev](https://pub.dev/packages/get_it)

- Thư viện giúp truy xuất dữ liệu và quản lý dependency injection dễ dàng.
- Cho phép truy cập vào các repository ở bất kỳ đâu trong project mà không cần truyền dữ liệu qua nhiều class.

### **2.2. Quản lý định tuyến với `go_router`**
[go_router trên pub.dev](https://pub.dev/packages/go_router)

- Do chính Flutter phát triển, hỗ trợ tốt trên cả mobile và web.
- Cung cấp cách quản lý route rõ ràng, dễ mở rộng.

### **2.3. Quản lý trạng thái với `flutter_bloc`**
[flutter_bloc trên pub.dev](https://pub.dev/packages/flutter_bloc)

- Một trong những thư viện quản lý trạng thái phổ biến và hiệu quả nhất cho Flutter.
- Nếu bạn chưa quen với `bloc`, hãy tham khảo bài viết của tôi tại: //TODO: Thêm link bài viết về BLoC.

### **2.4. Sinh code tự động với `json_serializable` & `freezed`**
[json_serializable trên pub.dev](https://pub.dev/packages/json_serializable)  
[freezed trên pub.dev](https://pub.dev/packages/freezed)

- Giúp giảm thời gian viết code lặp lại.
- Tự động sinh các hàm hỗ trợ như `copyWith`, `fromJson`, `toJson` để xử lý dữ liệu dễ dàng hơn.

### **2.5. Cải thiện chất lượng code với `lint`**
[lint trên pub.dev](https://pub.dev/packages/lint)

- Định nghĩa các quy tắc coding style chuẩn theo **Effective Dart**.
- Giúp code dễ đọc, nhất quán giữa các thành viên trong nhóm.
- Nếu bạn chưa đọc **Effective Dart**, có thể tham khảo tại: [Effective Dart](https://dart.dev/effective-dart).

### **2.6. Thiết lập icon ứng dụng với `flutter_launcher_icons`**
[flutter_launcher_icons trên pub.dev](https://pub.dev/packages/flutter_launcher_icons)

- Hỗ trợ tự động tạo icon cho ứng dụng trên cả Android và iOS.
- Giúp tiết kiệm thời gian setup.


## **3. Cách Tạo Base Project Với Mason**

Dưới đây là cách tôi thiết lập cấu trúc thư mục trong thư mục `lib/`:

```
lib/  
│── main.dart (chạy ứng dụng, chia thành 3 môi trường: dev, staging, production)  
│── router/ (quản lý route với `go_router`)  
│── helper/ (chứa các hàm dùng chung, ví dụ: localization, utilities)  
│── features/ (chứa các tính năng chính của ứng dụng, tách biệt rõ ràng)  
│── config/ (chứa các thiết lập môi trường, API, v.v.)  
```

Bên ngoài thư mục `lib/`, tôi cũng thiết lập:

```
assets/ (chứa hình ảnh và file localization)  
HELP.md (hướng dẫn setup project)  
CHANGELOG.md (ghi lại các thay đổi giữa các phiên bản)  
analysis_options.yaml (quy tắc lint)  
pubspec.yaml (khai báo dependency)  
build.yaml (cấu hình build)  
```

Bạn có thể tham khảo base project của tôi tại đây, nó được xây dựng dựa trên mason:  
[dr_base_project trên BrickHub](https://brickhub.dev/bricks/dr_base_project)


## **4. Thiết Lập Môi Trường Phát Triển**

Môi tường phát triển giúp đảm bảo bạn tránh được các lỗi trước khi ứng dụng đến tay người dùng.
Để dễ dàng thay đổi môi trường (dev, staging, production) mà không cần chỉnh sửa thủ công, tôi sử dụng một `enum` để lưu các thông tin môi trường.

### **4.1. Khai báo Enum Môi Trường**

```dart
late EnviEnum ourEnvi;

enum EnviEnum {
  develop(
    code: 0,
    title: "develop",
    config: _BaseUrlConfig(baseUrl: "http://192.168.20.82:5173/"),
  ),
  staging(
    code: 1,
    title: "staging",
    config: _BaseUrlConfig(baseUrl: "https://staging-partner.kdmp.net/"),
  ),
  product(
    code: 2,
    title: "product",
    config: _BaseUrlConfig(baseUrl: "https://partners.kdmp.net/"),
  );

  const EnviEnum({required this.code, required this.title, required this.config});
  final int code;
  final String title;
  final _BaseUrlConfig config;

  String get baseUrl => config.baseUrl;
}

class _BaseUrlConfig {
  final String baseUrl;
  const _BaseUrlConfig({required this.baseUrl});
}
```

Bạn có thể thêm các config khác như API key, logging, v.v.

### **4.2. Khởi tạo ứng dụng theo từng môi trường**

#### **Môi trường `develop` (dành cho server local)**

```dart
void main() {
  ourEnvi = EnviEnum.develop;
  myMain(() {
    setup();
    runApp(const App());
  });
}
```

#### **Môi trường `staging` (test nội bộ trước khi release)**

```dart
void main() {
  ourEnvi = EnviEnum.staging;
  myMain(() {
    setup();
    runApp(const App());
  });
}
```

#### **Môi trường `product` (sẵn sàng cho người dùng cuối)**

```dart
void main() {
  ourEnvi = EnviEnum.product;
  myMain(() {
    setup();
    runApp(const App());
  });
}
```


## **5. Kết Luận**

Thiết lập **base project** trong Flutter giúp tiết kiệm rất nhiều thời gian khi khởi tạo dự án mới, dễ bảo trì và mở rộng. 

- **Sử dụng Mason** để tự động tạo các file và thư mục cần thiết.
- **Tận dụng các thư viện phổ biến** như `go_router`, `flutter_bloc`, `json_serializable`, `freezed` để tăng hiệu quả phát triển.
- **Cấu hình môi trường dev, staging, product** giúp dễ dàng kiểm soát môi trường ứng dụng.
