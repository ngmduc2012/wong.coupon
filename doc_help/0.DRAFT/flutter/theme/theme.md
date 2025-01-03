Thiết lập giao diện sáng tối và font chữ cho 1 ứng dụng Flutter 

Việc phát triển UX của ứng dụng là 1 viện quan trọng, nó khiến người dùng thoải mái khi sử dụng và ở lại ứng dụng lâu hơn. 

1. Thiết lập giao diện sáng tối 
Giao diện sáng tối sẽ được chia ra làm 3 chế độ: 
Giao diện sáng, giao diện tối, và giao diện theo hệ thống, nhưng việc hiển thị trên giao diện lại chỉ có 2 phần chính là sáng và tối 
```dart
extension MyThemeModeContext on BuildContext{
  bool get isDarkMode => switch(myThemeMode) {
    ThemeMode.system => MediaQuery.of(this).platformBrightness == Brightness.dark,
    ThemeMode.light => false,
    ThemeMode.dark => true,
  };
}
```

Chúng ta sẽ tạo 1 biến toàn cục quản lý giao diện 
```dart
ThemeMode myThemeMode = ThemeMode.system;

```
Sẽ sảy ra 1 vấn đề là biến này sẽ không được lưu lại khi tắt ứng dụng hoàn toàn. có nhiều giải pháp cho vấn đề này như sử dụng shared_preferences https://pub.dev/packages/shared_preferences
để lưu biến đó lại và lấy nó nên khi khởi tạo ứng dụng. 
Còn cách của tôi sẽ lưu nó vào trong bloc như sau 
```dart
    myThemeMode = context.select((SettingBloc bloc) => bloc.state.settingWeb.theme);
```
Hãy tìm hiểu bài viết về bloc của tôi để biết cách lưu các giá trị vào trong bộ nhớ máy tại *LINK*

2. Sử dụng giao diện sáng tối vào giao diện
Trước tiên là về màu sắc, mỗi giao diện sáng và tối đều có 1 cặp màu sắc riêng. chúng ta có thể check nó trước khi sử dụng. 
```dart
extension WebColorContext on BuildContext{
  Color get white => isDarkMode ? WebColor.blackForDark : WebColor.white;
}

```

Cách sử dụng
```dart
 context.white
```
Có thể sẽ có 1 vài bất cập khi gọi màu kiểu như này vì chúng ta luôn cần phải có biến context, tôi có 1 cách đơn giản để gọi context ở bất kỳ đâu trong project 
hãy tham khảo bài viết sau của tôi *LINK*
3. Chuyển đổi giao diện sáng tối 
```dart
   myThemeMode = ThemeMode.light;
   await WidgetsFlutterBinding.ensureInitialized().performReassemble();
```
Câu lệnh thứ 2 sẽ giúp toàn bộ ứng dụng vẽ lại màn hình. 

4. Giao diện sáng tối với Font chữ 
Font chữ là 1 phần không thể thiếu trong giao diện
Có hiện tại tôi đang sử dụng 2 cách cho việc thêm font chữ cho ứng dụng của mình 
Cách 1 thêm từ file font chữ được lưu trong thư mục assets 
```dart
static const _neoTextStyle = TextStyle(
  fontFamily: WebFontFamily.nanumSquareNeo,
  fontFamilyFallback: [
    WebFontFamily.pretendard,
  ],
  package: 'dsg_ui_core', // Thêm thư viện front chữ từ package: https://api.flutter.dev/flutter/painting/TextStyle-class.html#:~:text=To%20use%20a%20font%20family%20defined%20in%20a%20package%2C%20the%20package%20argument%20must%20be%20provided.%20For%20instance%2C%20suppose%20the%20font%20declaration%20above%20is%20in%20the%20pubspec.yaml%20of%20a%20package%20named%20my_package%20which%20the%20app%20depends%20on.%20Then%20creating%20the%20TextStyle%20is%20done%20as%20follows%3A
  height: 1.5,
  leadingDistribution:  TextLeadingDistribution.even,
);
```
Cách 2 sử dụng thư viện google_fonts https://pub.dev/packages/google_fonts nếu google font có chứa font chữ của bạn sử dụng 
```dart
  static final _webTextStyle = GoogleFonts.inter().copyWith(
    fontWeight: FontWeight.w600,
    height: 1.5,
    leadingDistribution: TextLeadingDistribution.even,
  );
```
Một vấn đề sẽ sảy ra khi thay đổi kích thước màn hình, thì font chữ cũng cần thay đổi tỷ lệ để hài hoà cùng với thiết kế trong design, 
như ở đây tôi có tỉ lệ ratio được lấy theo tỷ lệ chiều ngang, lấy chiều dài của khung hình chia cho chiều dài được thiết kế trong design sẽ ra được tỷ lệ cần hiển thị. 
```dart
  double get webRatio {
    final width = MediaQuery.of(this).size.width;
    final ratio = width / 1440;
    return ratio < 1 ? ratio < 0.75 ? 0.75 : ratio : 1;
  }
```
Sau đó sẽ tiền hành tạo các style font chữ theo từng cỡ chữ trong giao diện figma 
```dart
  TextStyle get h46B =>
      _webTextStyle.copyWith(
        fontSize: 46 * webRatio,
        fontWeight: FontWeight.w700,
        color: neutral80,
      );
```
Tổng quan đoạn code thiết lập tại đây 
```dart
extension WebTextStyle on BuildContext{
/*static const _neoTextStyle = TextStyle(
fontFamily: WebFontFamily.nanumSquareNeo,
fontFamilyFallback: [
WebFontFamily.pretendard,
],
package: 'dsg_ui_core', // Thêm thư viện front chữ từ package: https://api.flutter.dev/flutter/painting/TextStyle-class.html#:~:text=To%20use%20a%20font%20family%20defined%20in%20a%20package%2C%20the%20package%20argument%20must%20be%20provided.%20For%20instance%2C%20suppose%20the%20font%20declaration%20above%20is%20in%20the%20pubspec.yaml%20of%20a%20package%20named%20my_package%20which%20the%20app%20depends%20on.%20Then%20creating%20the%20TextStyle%20is%20done%20as%20follows%3A
height: 1.5,
leadingDistribution:  TextLeadingDistribution.even,

); */
  static final _webTextStyle = GoogleFonts.inter().copyWith(
    fontWeight: FontWeight.w600,
    height: 1.5,
    leadingDistribution: TextLeadingDistribution.even,

  );

  /// Edit ratio of fontSize for responsive screen
  double get webRatio {
    final width = MediaQuery.of(this).size.width;
    final ratio = width / 1440;
    return ratio < 1 ? ratio < 0.75 ? 0.75 : ratio : 1;
  }

  /// Heading

  TextStyle get h46B =>
      _webTextStyle.copyWith(
        fontSize: 46 * webRatio,
        fontWeight: FontWeight.w700,
        color: white,
      );
}
```
Cách sử dụng
```dart
 Text("Hello", style: context.h46B)
```
Chúng ta hoàn toàn có thể tuỳ chỉnh style theo 1 kiểu design riêng biệt, sau đây là 1 cách tuỳ chỉnh với màu.
```dart
 Text("Hello", style: context.b10B.copyWith(
    color: context.white,
    ),
```

Khi thiết lập như này, khi giao diện sáng tối thay đổi, màu chữ cũng sẽ thay đổi theo màu của giao diện sáng tối. 

Trên đây là bài trình bày của tôi về cách thiết lập giao diện sáng tối, bạn có thể tham khảo thêm tại cách tạo package ui tại *LINK*