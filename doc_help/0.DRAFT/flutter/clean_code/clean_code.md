

Các biện pháp giúp code sạch và tường minh cho đội nhóm  

1. Dùng chung 1 form code cho tất cả các thành viên trong nhóm, môi ng đều có 1 phong cách code riêng, việc đọc code của ng khác cũng khiến khá mất thừoi gian, việc dùng chugn 1 fomr code cho cả nhóm là cần thiết
2. dùng lint, - follơư dart efect code. 
3. Sử dụng kiến trức clean architectủre 
4. Sử dụng cách tách biệt các plugin bên thứ 3 với dự án chính bằng các class riêng biêt, vì các vấn đề plugin đó không còn đc update. 
5. Các phần liên quan đến native cần tách biệt ra các plugin để đảm bảo tường minh và dẽ bảo trì, cũng dễ dàng sửa các lỗi native không xác định bằng cách tạo project mới 
6. Câu l;ệnh dart giúp làm sạch code, cho đồng nghiệp , và bản thân,  


# Các Biện Pháp Giúp Code Sạch và Tường Minh Cho Đội Nhóm Phát Triển Flutter
![image1.jpeg](image1.jpeg)
Trong thế giới phát triển phần mềm, việc duy trì code sạch và rõ ràng không chỉ giúp tăng hiệu quả làm việc mà còn đảm bảo chất lượng của sản phẩm cuối cùng. Đặc biệt, khi làm việc trong một đội nhóm, sự thống nhất về phong cách code và kiến trúc dự án là yếu tố then chốt để mọi thành viên có thể hiểu và tiếp tục phát triển dự án một cách suôn sẻ. Dưới đây là những biện pháp giúp đội nhóm của bạn giữ cho code luôn sạch và dễ hiểu khi phát triển ứng dụng Flutter.

## 1. Sử Dụng Chung Một Quy Chuẩn Code Cho Tất Cả Thành Viên

Mỗi lập trình viên thường có phong cách code riêng, từ cách đặt tên biến, hàm, đến cách sắp xếp các khối code. Điều này dẫn đến khó khăn khi đọc và hiểu code của người khác, gây mất thời gian và giảm hiệu suất làm việc.

**Giải pháp:**

- **Thiết Lập Quy Chuẩn Code Chung:**
    - Xây dựng một tài liệu quy định về quy tắc viết code cho dự án.
    - Bao gồm cách đặt tên biến, hàm, class, cấu trúc thư mục, và cách viết comment.
- **Sử Dụng Công Cụ Sinh Code Tự Động:**
    - Cách trên sẽ rất phức tạo và gây ra gánh nặng lên hệ thần kinh. Tôi đã áp dụng 1 giải pháp sử dụng mason để tạo form mẫu code 
    cho tất cả các cấu trúc có trong dự án, từ class đến enum, giao diện. Như vậy các đồng nghiệp sẽ có cơ hội đóng góp ý tưởng cây dựng nó 
    và dễ dang tiếp cận code của nhau vì chúng đã chúng 1 khuân mẫu. Bạn có thể tham khảo bài viết của tôi về Mason tại *LINK*

**Lợi ích:**

- **Tăng Tính Nhất Quán:**
    - Code trở nên thống nhất về phong cách và cấu trúc, giúp dễ dàng đọc và hiểu.
- **Giảm Xung Đột Khi Merge Code:**
    - Khi mọi người tuân thủ cùng một quy chuẩn, các xung đột khi gộp code sẽ giảm đi đáng kể.

---

## 2. Sử Dụng Linter và Tuân Thủ "Effective Dart"

**Vấn đề:**

Code có thể chứa các lỗi tiềm ẩn hoặc không tuân thủ best practices, dẫn đến khó khăn trong bảo trì và phát triển.

**Giải pháp:**

- **Áp Dụng Linter:**
    - Sử dụng các gói như `lint` để thêm quy tắc linting vào dự án.
    - Tích hợp công cụ linting vào quy trình CI/CD để tự động kiểm tra code khi commit hoặc đẩy lên repository.
- **Tuân Thủ "Effective Dart":**
    - Đây là bộ hướng dẫn chính thức từ Dart team, giúp lập trình viên viết code hiệu quả và theo chuẩn.
    - Bao gồm các hướng dẫn về phong cách, tài liệu hóa, và cách sử dụng ngôn ngữ một cách tối ưu.

**Cách Thực Hiện:**

- **Thêm Gói Lint Vào Dự Án:**

  ```yaml
  dev_dependencies:
    lint: ^1.5.3
  ```

- **Cấu Hình `analysis_options.yaml`:**

  ```yaml
  include: package:lint/analysis_options.yaml
  ```

**Lợi ích:**

- **Phát Hiện Lỗi Sớm:**
    - Linter giúp phát hiện các vi phạm quy tắc hoặc lỗi coding ngay trong quá trình viết code.
- **Nâng Cao Chất Lượng Code:**
    - Đảm bảo code tuân thủ best practices, dễ đọc và dễ bảo trì.

---

## 3. Áp Dụng Kiến Trúc Clean Architecture

**Vấn đề:**

Codebase phức tạp và khó bảo trì khi không có kiến trúc rõ ràng, dẫn đến khó khăn trong việc mở rộng và thử nghiệm.

**Giải pháp:**

- **Sử Dụng Clean Architecture:**
    - Tách biệt ứng dụng thành các layer: Presentation, Domain, Data.
    - Đảm bảo sự phụ thuộc giữa các layer là một chiều, từ ngoài vào trong.
- **Sử Dụng Dependency Injection:**
    - Quản lý các phụ thuộc giữa các lớp thông qua DI để dễ dàng thay thế và mô phỏng trong khi test.

**Cách Thực Hiện:**

- **Tổ Chức Thư Mục:**

  ```
  lib/
    presentation/
    domain/
    data/
  ```

- **Mô Hình Hóa Các Layer:**

    - **Presentation Layer:**
        - Chứa UI, Stateful Widgets, và logic hiển thị.
    - **Domain Layer:**
        - Chứa các entities, use cases, repositories (abstraction).
    - **Data Layer:**
        - Implement các repositories, xử lý API, database.

**Lợi ích:**

- **Dễ Bảo Trì và Mở Rộng:**
    - Code được tổ chức rõ ràng, dễ dàng thêm tính năng mới hoặc thay đổi logic.
- **Tăng Khả Năng Test:**
    - Từng layer có thể được kiểm thử độc lập, đảm bảo chất lượng code.

---

## 4. Tách Biệt Các Plugin Bên Thứ Ba Bằng Các Class Riêng Biệt

**Vấn đề:**

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

**Lợi ích:**

- **Dễ Dàng Thay Thế Hoặc Cập Nhật Plugin:**
    - Nếu cần thay đổi plugin, chỉ cần sửa trong lớp implement mà không ảnh hưởng đến code khác.
- **Giảm Sự Phụ Thuộc Trực Tiếp:**
    - Code của bạn ít bị ảnh hưởng bởi thay đổi từ phía plugin bên thứ ba.

---

## 5. Tách Biệt Phần Mã Liên Quan Đến Native Thành Các Plugin Riêng

**Vấn đề:**

Mã native tích hợp trực tiếp trong dự án Flutter có thể gây khó khăn khi cần chỉnh sửa hoặc tái sử dụng ở dự án khác.

**Giải pháp:**

- **Tạo Các Flutter Plugin Riêng Cho Mã Native:**
    - Đặt mã Android và iOS trong các plugin riêng biệt.
    - Quản lý chúng như các gói độc lập.

**Cách Thực Hiện:**

- **Tạo Plugin Mới:**

  ```bash
  flutter create --template=plugin --platforms=android,ios native_functions
  ```

- **Sử Dụng Plugin Trong Dự Án:**

    - **Thêm Vào `pubspec.yaml`:**

      ```yaml
      dependencies:
        native_functions:
          path: ../native_functions
      ```

**Lợi ích:**

- **Tường Minh và Dễ Bảo Trì:**
    - Mã native được tách biệt, dễ dàng quản lý và cập nhật.
- **Tái Sử Dụng:**
    - Plugin có thể được sử dụng trong nhiều dự án khác nhau mà không cần viết lại mã.

---

## 6. Sử Dụng Các Lệnh Dart Để Làm Sạch Code

**Vấn đề:**

Code có thể trở nên lộn xộn theo thời gian với các import thừa, code không sử dụng, hoặc format không nhất quán.

**Giải pháp:**

- **Sử Dụng Các Lệnh Hữu Ích Của Dart:**

    - **Định Dạng Code:**

      ```bash
      dart format .
      ```

    - **Phân Tích Code:**

      ```bash
      dart analyze
      ```

    - **Tự Động Sửa Code:**

      ```bash
      dart fix --apply
      ```

- **Tích Hợp Vào Quy Trình CI/CD:**
    - Thực hiện các lệnh này tự động khi code được đẩy lên repository.

**Lợi ích:**

- **Tiết Kiệm Thời Gian:**
    - Giảm thiểu công việc thủ công trong việc kiểm tra và làm sạch code.
- **Đảm Bảo Code Luôn Sạch:**
    - Giữ cho codebase luôn ở trạng thái tốt nhất.

---

## **Kết Luận**

Việc duy trì một codebase sạch và rõ ràng là nền tảng cho một dự án thành công, đặc biệt khi làm việc trong một đội nhóm. Bằng cách:

- Thống nhất quy chuẩn code.
- Sử dụng công cụ linting và tuân thủ best practices.
- Áp dụng kiến trúc sạch.
- Tách biệt phụ thuộc vào plugin bên thứ ba.
- Tách mã native thành các plugin riêng.
- Sử dụng các lệnh Dart để làm sạch code.

Bạn không chỉ cải thiện chất lượng của dự án mà còn tăng hiệu suất làm việc nhóm, giảm thiểu rủi ro và tạo điều kiện cho việc mở rộng và bảo trì trong tương lai.

**Hãy bắt đầu áp dụng những biện pháp này ngay hôm nay để nâng tầm dự án Flutter của bạn!**

---

**Chia Sẻ Kinh Nghiệm:**

Nếu bạn có những kinh nghiệm hoặc biện pháp khác giúp giữ cho code sạch và tường minh, hãy chia sẻ cùng cộng đồng để chúng ta cùng nhau học hỏi và phát triển.

---

**Tham Khảo Thêm:**

- **Effective Dart:** [https://dart.dev/guides/language/effective-dart](https://dart.dev/guides/language/effective-dart)
- **Flutter Lints Package:** [https://pub.dev/packages/flutter_lints](https://pub.dev/packages/flutter_lints)
- **Clean Architecture Bài Viết:** [https://medium.com/flutter-community/flutter-clean-architecture-498aa8a3b04f](https://medium.com/flutter-community/flutter-clean-architecture-498aa8a3b04f)
- **Creating Packages & Plugins:** [https://flutter.dev/docs/development/packages-and-plugins/developing-packages](https://flutter.dev/docs/development/packages-and-plugins/developing-packages)

---

**Chúc bạn và đội nhóm luôn thành công trên con đường phát triển Flutter!** 🚀
