Sức mạnh của enum trong việc đơn giản hoá code 

```dart
enum PlanetType { terrestrial, gas, ice }
```

1. Những thứ enum có thể giúp đơn giản hoá code. 
Đây là 1 enum đơn thuần mà ai cũng biết, nhưng sẽ ra sao nếu enum của bạn có thể: 
- tự định nghĩa trong json khi nhận được tử server 
- tự có thể dịch sang các ngôn ngữ khác  
- Có thể tự liệt kê danh sách 
- Tự tìm kiếm các giá trị trong hàm của mình  
- có thể rút nắng các đoạn code có điều kiện 

Trong bài viết này tôi sẽ giúp bạn biến 1 enum đơn thuần trở thành 1 các code cực kỳ tiện lợi và nhanh chóng. 

Flutter là 1 ngôn ngữ lập trình hướng đối tượng, Tính đóng gói là nguyên tắc ẩn giấu chi tiết triển khai của một đối tượng và chỉ cho phép truy cập hoặc thao tác với nó thông qua các phương thức công khai (public methods). Điều này giúp bảo vệ dữ liệu bên trong đối tượng khỏi sự thay đổi trực tiếp từ bên ngoài, giảm thiểu rủi ro gây lỗi và tăng tính bảo trì của mã nguồn.
Vì vậy chúng ta sẽ đưa tất cả các phần hữu ích trên vào trong 1 enum vào sau đó chỉ việc sử dụng và không cần phải quan tâm đến no nữa. 

2. Tự định nghĩa trường trong json 

Việc tự định nghĩa 1 trường trong json được giới thiếu trong tài liệu của json_serializable https://pub.dev/packages/json_serializable#enums 

Dứoi đây là 1 ví dụ thiết lập 1 enum cho json_serializable, enum sẽ dựa vào giá trị 'code' để lấy dữ liệu từ json và chuyển nó thành enum, VD:
```json
{ "code" : 1}
```

Giá trị của trường StateEnum code lúc này sẽ là StateEnum.loading khi nhận được dữ liệu trên.
Chúng ta có thể thay đổi 'code' bằng các trường khác tuỳ ý như 'title'

```dart
@JsonEnum(valueField: 'code')
enum StateEnum {

    notWorkingReady( 0, "notWorkingReady"),
    loading( 1, "loading"),
    notWorkingSuccess( 3, "notWorkingSuccess"),
    notWorkingFailure( 2, "notWorkingFailure");
    
    // @override
    // int compareTo(PaddingWhiteLineEditEnum other) => code - other.code;
    
    const StateEnum( this.code, this.title);
    
    final int code;
    final String title;
}
```

2. Rút gọn các đoạn code điều kiện 

Lợi ích thứ 2 mà enum có thể làm được là rút gọn các đoạn code điều kiện khiến chúng trở nên dễ hiểu, hay xem xét ví dụ sau 

```dart
@JsonEnum(valueField: 'code')
enum StateEnum {

    notWorkingReady( 0, "notWorkingReady"),
    loading( 1, "loading"),
    notWorkingSuccess( 3, "notWorkingSuccess"),
    notWorkingFailure( 2, "notWorkingFailure");
    
    // @override
    // int compareTo(PaddingWhiteLineEditEnum other) => code - other.code;
    
    const StateEnum( this.code, this.title);
    
    final int code;
    final String title;

    bool get isLoading => this == StateEnum.loading;

}
```
Việc viết ngắn gọn các điều kiện khiến code tường minh và dễ dọc hơn như sau 
Cách mới 
```dart
if(state.isLoading){
  
}
```
Cách cũ 
```dart
if(state == StateEnum.loading){
  
}
```

3. Chuyển đổi ngôn ngữ 

```dart
  String get interpret => switch(this) {
      StateEnum.notWorkingReady => "Translate notWorkingReady",
      StateEnum.loading => "Translate loading",
      _ => "Translate other",
  };
```
Tôi sẽ có 1 bài viết về chuyển đổi đa ngôn ngữ, bạn hãy tham khảo tại - *LINK*

4. Truy xuất dữ liệu từ tường dữ liệu của enum

Giả sử tôi có dữ liệu của tường code và tôi muốn tìm kiếm nó xem đó là enum nào, tôi có thể tạo 1 hàm như sau 
```dart
static ({StateEnum? getEnum , String? string})? getByCode(int? code){
    try {
        final e = StateEnum.values.firstWhere((element) => element.code == code,);
        return (getEnum: e, string: e.interpret);
    } catch (e) {
      return null;
    }
}
```

5. Ứng dụng mason rút ngắm thời gian tạo enum 
Với những sự hữu dụng như trên, việc dùng đầy đủ các hàm enum sẽ giúp chúng ta code nhanh hơn và clean code hơn. 
Để tạo 1 form mẫu code sẵn cho các enum của bản thân, hãy tham khảo mason. (nếu bạn chưa biết gì về mason hãy tham khảo https://wongcoupon.com/en/doc/help/flutter/boost-your-flutter-development-efficiency-with-mason)
Bạn có thể tham khảo mẫu enum của tôi để tạo cho mình 1 mẫu enum phù hợp với bạn và đồng nghiệp của bạn dr_enum https://brickhub.dev/bricks/dr_enum

