# **Hướng Dẫn Thiết Lập và Truy Cập BuildContext Ở Bất Kỳ Đâu Trong Dự Án Flutter**

![image1.jpeg](image1.jpeg)
Trong quá trình phát triển ứng dụng Flutter, có những lúc bạn cần truy cập `BuildContext` hoặc thực hiện điều hướng (navigation) từ bất kỳ nơi nào trong dự án, ngay cả khi bạn không ở trong một widget có `context`. Bài viết này sẽ hướng dẫn bạn cách thiết lập và sử dụng `GlobalKey<NavigatorState>` để truy cập `BuildContext` và thực hiện điều hướng từ bất kỳ đâu trong ứng dụng của bạn.

## **1. Tại Sao Cần Truy Cập BuildContext Toàn Cục?**

`BuildContext` là một phần quan trọng trong Flutter, cho phép bạn truy cập đến cây widget (widget tree) và thực hiện các tác vụ như:

- Điều hướng giữa các màn hình.
- Hiển thị SnackBar, Dialog.
- Lấy thông tin về theme, media query.
- Nếu bạn sử dụng Bloc, hay các State management cần đến context thì bạn hoàn toàn có thẻ gọi các event của các Bloc khác nhau trong project. 

Tuy nhiên, trong một số trường hợp, bạn cần truy cập `context` từ những nơi không phải là widget, chẳng hạn như:

- Trong các lớp dịch vụ (services), mô hình (models), hoặc lớp tiện ích (utilities).
- Khi xử lý logic không liên quan trực tiếp đến UI.
- Nếu sử dụng Bloc, phải đưa sự kiện xử lý giữa các Bloc khác nhau ra ngoài giao diện. 

---

## **2. Giải Pháp Sử Dụng GlobalKey<NavigatorState>**

`GlobalKey<NavigatorState>` cho phép bạn truy cập đến `NavigatorState` toàn cục, từ đó bạn có thể thực hiện các thao tác điều hướng và truy cập `BuildContext` thông qua `navigatorKey.currentContext`.

### **2.1. Thiết Lập navigatorKey**

**Bước 1:** Tạo một biến toàn cục `GlobalKey<NavigatorState>`:

```dart
final GlobalKey<NavigatorState> myNavigatorKey = GlobalKey<NavigatorState>();
```

**Bước 2:** Cung cấp `navigatorKey` cho `MaterialApp` hoặc `CupertinoApp` của bạn:

```dart
return MaterialApp(
  navigatorKey: myNavigatorKey,
  home: MyHomePage(),
);
```

Hoặc nếu bạn sử dụng gói `go_router`:

```dart
final GoRouter ourRouter = GoRouter(
  navigatorKey: myNavigatorKey,
  // ... các thiết lập khác
);
```

### **2.2. Sử Dụng navigatorKey Để Truy Cập BuildContext**

Bây giờ, bạn có thể truy cập `BuildContext` từ bất kỳ đâu trong mã của bạn bằng cách:

```dart
BuildContext? context = myNavigatorKey.currentContext;
```

**Ví dụ:** Điều hướng đến một màn hình khác:

```dart
myNavigatorKey.currentState?.pushNamed('/nextScreen');

```

Hoặc sử dụng với `go_router`:

```dart
myNavigatorKey.currentContext?.go('/nextScreen');
myNavigatorKey.currentContext?.read<CameraViewModelBloc>().add(ResetState());
```

## **6. Ưu Điểm và Nhược Điểm**

### **6.1. Ưu Điểm**

- **Tiện lợi:** Truy cập `context` và điều hướng từ bất kỳ đâu.
- **Tổ chức mã tốt hơn:** Giảm phụ thuộc vào widget để thực hiện các tác vụ không liên quan đến UI.

### **6.2. Nhược Điểm**

- **Quản lý trạng thái phức tạp hơn:** Có thể dẫn đến khó khăn trong việc quản lý trạng thái ứng dụng.
- **Khả năng mở rộng:** Trong các ứng dụng lớn, việc sử dụng quá nhiều biến toàn cục có thể gây khó khăn trong bảo trì.


