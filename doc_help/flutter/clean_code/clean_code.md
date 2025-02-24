
# Các Biện Pháp Giúp Code Sạch và Tường Minh Cho Đội Nhóm Phát Triển Flutter
![image1.jpeg](image1.jpeg)

Trong thế giới phát triển phần mềm, việc duy trì code sạch và rõ ràng không chỉ giúp tăng hiệu quả làm việc mà còn đảm bảo chất lượng của sản phẩm cuối cùng. Đặc biệt, khi làm việc trong một đội nhóm, sự thống nhất về phong cách code và kiến trúc dự án là yếu tố then chốt để mọi thành viên có thể hiểu và tiếp tục phát triển dự án một cách suôn sẻ. Dưới đây là những biện pháp giúp đội nhóm của bạn giữ cho code luôn sạch và dễ hiểu khi phát triển ứng dụng Flutter.

## 1. Sử Dụng Chung Một Quy Chuẩn Code Cho Tất Cả Thành Viên Bằng Mason

Mỗi lập trình viên thường có phong cách code riêng, từ cách đặt tên biến, hàm, đến cách sắp xếp các khối code. Điều này dẫn đến khó khăn khi đọc và hiểu code của người khác, gây mất thời gian và giảm hiệu suất làm việc.

- **Thiết Lập Quy Chuẩn Code Chung:**
  - Xây dựng một tài liệu quy định về quy tắc viết code cho dự án.
  - Bao gồm cách đặt tên biến, hàm, class, cấu trúc thư mục, và cách viết comment.

- **Sử Dụng Công Cụ Sinh Code Tự Động:**
  - Cách trên sẽ rất phức tạp và gây ra gánh nặng lên hệ thần kinh. Tôi đã áp dụng một giải pháp sử dụng Mason để tạo form mẫu code cho tất cả các cấu trúc có trong dự án, từ class đến enum, giao diện. Như vậy, các đồng nghiệp sẽ có cơ hội đóng góp ý tưởng, xây dựng nó và dễ dàng tiếp cận code của nhau vì chúng đã chung một khuôn mẫu. Bạn có thể tham khảo bài viết của tôi về Mason tại *LINK*.

**Lợi ích:**

- **Tăng Tính Nhất Quán:**
  - Code trở nên thống nhất về phong cách và cấu trúc, giúp dễ dàng đọc và hiểu.

- **Giảm Xung Đột Khi Merge Code:**
  - Khi mọi người tuân thủ cùng một quy chuẩn, các xung đột khi gộp code sẽ giảm đi đáng kể.

## 2. Sử Dụng Linter và Tuân Thủ "Effective Dart"

- **Tuân Thủ "Effective Dart":**
  - Đây là bộ hướng dẫn chính thức từ Dart team, giúp lập trình viên viết code hiệu quả và theo chuẩn.
  - Bao gồm các hướng dẫn về phong cách, tài liệu hóa, và cách sử dụng ngôn ngữ một cách tối ưu.
  - Việc luôn phải ghi nhớ và tuân thủ các chính sách này có thể khiến chúng ta cảm thấy khó chịu. Vì vậy, tôi đã sử dụng một thư viện có tên lint để luôn giám sát cách chúng ta viết code và đưa ra cảnh báo. Hãy đọc thêm về nó tại đây: [https://pub.dev/packages/lint](https://pub.dev/packages/lint).
  - Linter giúp phát hiện các vi phạm quy tắc hoặc lỗi coding ngay trong quá trình viết code.
  - Đảm bảo code tuân thủ best practices, dễ đọc và dễ bảo trì.

**Cách Tôi Áp Dụng lint Vào Project Của Mình**

- **Thêm Gói Lint Vào Dự Án:**

  ```yaml
  dev_dependencies:
    lint: ^2.3.0
  ```

  - **Cấu Hình `analysis_options.yaml`:**

    ```yaml
    # This file configures the analyzer to use the lint rule set from `package:lint`

    include: package:lint/strict.yaml # For production apps
    # include: package:lint/casual.yaml # For code samples, hackathons and other non-production code
    # include: package:lint/package.yaml # Use this for packages with public API
  
  
    # You might want to exclude auto-generated files from dart analysis
    analyzer:
    errors:
      invalid_annotation_target: ignore
      lines_longer_than_80_chars: ignore
    plugins:
    - custom_lint
    exclude:
      - packages/mason_core/**
      - '**.freezed.dart'
      - '**.g.dart'
  
    # You can customize the lint rules set to your own liking. A list of all rules
    # can be found at https://dart-lang.github.io/linter/lints/options/options.html
    linter:
      rules:
      - unawaited_futures
    rules:
      # Util classes are awesome!
      # avoid_classes_with_only_static_members: false
    
      # Make constructors the first thing in every class
      # sort_constructors_first: true
    
      # Choose wisely, but you don't have to
      prefer_double_quotes: true
      prefer_single_quotes: true
      avoid_dynamic_calls: true
      lines_longer_than_80_chars: false
      avoid_classes_with_only_static_members: true
      use_named_constants: true
      annotate_redeclares: true
    ```
- **Sau Đó Chạy Lại Lệnh:**

    ```bash
    dart analyze
    ```

## 3. Áp Dụng Kiến Trúc Clean Architecture

- **Sử Dụng Clean Architecture:**
  - Tách biệt ứng dụng thành các layer: Presentation, Domain, Data.
  - Đảm bảo sự phụ thuộc giữa các layer là một chiều, từ ngoài vào trong.
  - Tôi có một bài viết về Clean Architecture tại *LINK*, bạn có thể tham khảo thêm.
- **Sử Dụng Dependency Injection:**
  - Quản lý các phụ thuộc giữa các lớp thông qua DI (Dependency Injection) để dễ dàng thay thế và mô phỏng trong khi test.

## 4. Tách Biệt Các Plugin Bên Thứ Ba Bằng Các Class Riêng Biệt

Phụ thuộc quá nhiều vào các plugin bên thứ ba trực tiếp trong code chính có thể gây khó khăn khi plugin không còn được cập nhật hoặc gây lỗi.

**Giải pháp:**

- **Tạo Các Adapter Hoặc Service Layer:**
  - Đóng gói việc sử dụng plugin trong các class riêng biệt.
  - Sử dụng interface hoặc abstract class để định nghĩa các hành vi.

**Ví Dụ:**

```dart
abstract class AuthenticationService {
  Future<User> login(String username, String password);
}

class FirebaseAuthService implements AuthenticationService {
  final FirebaseAuth _firebaseAuth = FirebaseAuth.instance;

  @override
  Future<User> login(String username, String password) async {
    // Sử dụng plugin Firebase Auth
    final userCredential = await _firebaseAuth.signInWithEmailAndPassword(
      email: username,
      password: password,
    );
    return userCredential.user;
  }
}
```

## 5. Tách Biệt Phần Code Liên Quan Đến Native Thành Các Plugin Riêng

Mã native tích hợp trực tiếp trong dự án Flutter có thể gây khó khăn khi cần chỉnh sửa hoặc tái sử dụng ở dự án khác. Hoặc chỉ đơn giản là nâng cấp lên phiên bản Flutter mới có thể khiến cho phần code Android bị lỗi thời.

**Giải pháp:**

- **Tạo Các Flutter Plugin Riêng Cho Mã Native:**
  - Đặt mã Android và iOS trong các plugin riêng biệt.
  - Quản lý chúng như các gói độc lập.
  - Bạn có thể tham khảo một bài viết về plugin của tôi tại *LINK*.

## 6. Sử Dụng Các Lệnh Dart Để Làm Sạch Code

**Vấn đề:**

Code có thể trở nên lộn xộn theo thời gian với các import thừa, code không sử dụng, hoặc format không nhất quán. Nhưng đôi khi việc format code lại càng dẫn đến việc khó nhìn hơn, tôi đã tìm ra giải pháp khi format code nhiều hơn 80 ký tự một dòng của Flutter.

**Giải pháp:**

```bash
      dart fix --apply
      dart format --line-length=1000 .
      dart analyze
   ```

## **Kết Luận**

Việc duy trì một codebase sạch và rõ ràng là nền tảng cho một dự án thành công, đặc biệt khi làm việc trong một đội nhóm. Bằng cách:

- Thống nhất quy chuẩn code.
- Sử dụng công cụ linting và tuân thủ best practices.
- Áp dụng kiến trúc sạch.
- Tách biệt phụ thuộc vào plugin bên thứ ba.
- Tách mã native thành các plugin riêng.
- Sử dụng các lệnh Dart để làm sạch code.

Bạn không chỉ cải thiện chất lượng của dự án mà còn tăng hiệu suất làm việc nhóm, giảm thiểu rủi ro và tạo điều kiện cho việc mở rộng và bảo trì trong tương lai.
