# Hướng Dẫn Tạo và Quản Lý Package

**Tạo và quản lý package giúp giảm thời gian code đáng kể, tái sử dụng mã nguồn, và giúp duy trì dự án một cách dễ dàng hơn. Dưới đây là một số lợi ích cụ thể của việc tạo package:**

1. **Tái sử dụng mã nguồn**: Khi bạn tạo ra các package cho các tính năng hoặc thành phần phổ biến, bạn có thể tái sử dụng chúng trong nhiều dự án khác nhau mà không cần phải viết lại mã. Điều này giúp tiết kiệm thời gian và công sức.

2. **Dễ dàng quản lý và bảo trì**: Các package được tổ chức gọn gàng, dễ dàng cập nhật và bảo trì. Khi cần thay đổi hoặc nâng cấp một tính năng, bạn chỉ cần cập nhật package mà không ảnh hưởng đến toàn bộ dự án.

3. **Tăng cường tính nhất quán**: Sử dụng các package giúp đảm bảo rằng các đoạn mã nguồn của bạn luôn nhất quán và tuân thủ cùng một quy tắc hoặc chuẩn mực.

4. **Phát triển nhanh chóng**: Với các package được chuẩn bị sẵn, bạn có thể tập trung vào việc phát triển các tính năng đặc thù của dự án mà không mất thời gian cho các phần cơ bản.

5. **Chia sẻ và cộng tác**: Các package có thể được chia sẻ với cộng đồng hoặc nhóm làm việc, giúp tăng cường khả năng cộng tác và tận dụng kiến thức của nhau.

6. **Giảm thiểu lỗi**: Khi sử dụng lại các package đã được kiểm thử và ổn định, bạn sẽ giảm thiểu nguy cơ gặp phải các lỗi mới khi phát triển ứng dụng.

Dưới đây là hướng dẫn chi tiết về cách tạo và quản lý package:

## 1 Cách tôi thiết lập các package cho đội nhóm.

![image1.png](image1.png)

Tôi đã tạo 1 thư mục có tên là packages để lưu tất cả các package tôi tạo ra.
Tôi chia chúng thành 2 loại, 1 loại thuộc vào ancestor_core, đây là những package nền tảng mà tôi đã viết gọn lại các hàm theo phong cách của lập trình hướng đối tượng. Khi sử dụng chỉ việc gọi nó ra ngoài và sử dụng, không cần bận tâm đến cấu trúc phức tạp bên trong các hàm xử lý và thiết lập. 
Một loại khác là các package ui, nơi chứa đựng tất cả các widget ui của giao diện, hay những thứ gì liên quán đến ui, nó giúp tối giản 1 khối lượng code lớn và có thể tái sử dụng cho các dự án sau này. 

Hãy cùng đi sâu vào 1 trong số đó, fỉre_core:
![image2.png](image2.png)

Nơi đây tôi lưu trữ tất cả các phần code tôi đã từng code liên quan đến firebase. Nếu như trong tương lai, có 1 dự án nào mới cần sử dụng đến firebase, tôi chỉ việc import package này của tôi vào và sử dụng lại code tôi đã từng code trước đây. Thậm trí bạn có thể đồng bộ tất cả các package của các project khác nhau bằng git để cập nhập những code mới nhất cho dự án. 


## 2 Thiết lâp 1 Package

### Bước 1: Chạy
```bash
cd pathTo/packages

flutter create --template=package hello_core
```

### Bước 2: Thiết lập lint trong tệp pubspec.yaml

```bash
cd pathTo/hello_core
flutter pub add lint
flutter pub add test
flutter pub add mockito
```
The lint package is a package that helps check errors for Dart. This will help keep Dart code neat and formatted.
[Learn more](https://pub.dev/packages/lint)

### Bước 3: Thay thế tệp analysis_options.yaml
Thay thế tệp analysis_options.yaml của dự án
Thêm cấu hình phân tích lỗi từ gói `lint`.

```yaml
# File này cấu hình phân tích lỗi sử dụng bộ quy tắc từ package:lint

include: package:lint/strict.yaml # Dành cho các ứng dụng sản xuất
# include: package:lint/casual.yaml # Dành cho mã mẫu, hackathons và các mã không phải sản xuất
# include: package:lint/package.yaml # Dành cho các gói với API công khai

# Bạn có thể muốn loại bỏ các tệp tự động tạo ra khỏi phân tích dart
analyzer:
  errors:
    invalid_annotation_target: ignore
  plugins:
    - custom_lint
  exclude:
    - packages/mason_core/**
    - '**.freezed.dart'
    - '**.g.dart'

# Bạn có thể tùy chỉnh các quy tắc lint theo ý thích của mình. Một danh sách tất cả các quy tắc
# có thể được tìm thấy tại https://dart-lang.github.io/linter/lints/options/options.html
linter:
  rules:
    - unawaited_futures
rules:
  # Các lớp tiện ích rất tuyệt!
  # avoid_classes_with_only_static_members: false

  # Đặt các constructor lên đầu tiên trong mỗi lớp
  # sort_constructors_first: true

  # Lựa chọn khôn ngoan, nhưng không cần thiết
  prefer_double_quotes: true
  prefer_single_quotes: true
  avoid_dynamic_calls: true
  lines_longer_than_80_chars: true
  avoid_classes_with_only_static_members: true
  use_named_constants: true
```

Thay thế tệp analysis_options.yaml của package 
```yaml
include: ../../analysis_options.yaml
```

[Learn more](https://docs.flutter.dev/packages-and-plugins/developing-packages)

Để tạo cấu trúc các file thử mục như trong hình ảnh, bạn có thể sử dụng mason để tạo nó 1 cách nhanh chóng, nếu bạn chưa biết về mason. Bạn có thể tham khảo brick của tôi tại đây [dr_folder_package](https://brickhub.dev/search?q=dr_folder_package) 

## 3 Đồng bộ folder package ở các dự án khác 
### Bước 1: Tạo 1 repository mới 
### Bước 2: Chạy
```bash
cd pathTo/packages
git add .
git commit -am "changed"
git push origin feature-snap
```
### Bước 3: (tuỳ chọn) đẩy lên
```bash
cd pathTo/packages
git fetch
git pull origin master --rebase
```

## 1.4 Branch
Create branch for package\
eg: 
- master
- feature-snap
- feature-sms
- feature-my
- feature-zeus

and then you can use the command below to update the package

### Bước 2: (tuỳ chọn) kéo về
```bash
git pull origin master
git push origin feature-snap
git push origin feature-sms
git push origin feature-my
git push origin feature-zeus
git gc
```
## 1.5 Package Structure
A project package can contain many sub-packages. For example my package structure
![package-structure.png](package-structure.png)
## Use package in your project
To easily maintain and fix your package and your project, you should put the package in your project. It allows you to easily edit your project without having to update to git constantly.
![project-structure.png](project-structure.png)

and then add package name to your pubspec.yaml
![yaml-instruct.png](yaml-instruct.png)
## Kết Luận
Việc xây dựng package giúp giảm thời gian code, tạo ra mã một lần và sử dụng lại nhiều lần trong nhiều dự án, giảm thời gian thiết lập lại. Chúc bạn thành công trong việc tạo và quản lý package của mình! 🎉
