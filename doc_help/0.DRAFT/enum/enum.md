Sức mạnh của enum trong việc đơn giản hoá code 

```dart
enum PlanetType { terrestrial, gas, ice }
```

Đây là 1 enum đơn thuần mà ai cũng biết, nhưng sẽ ra sao nếu enum của bạn có thể: 
- tự định nghĩa trong json khi nhận được tử server 
- tự có thể dịch sang các ngôn ngữ khác  
- Có thể tự liệt kê danh sách 
- Tự tìm kiếm các giá trị trong hàm của mình  
- có thể rút nắng các đoạn code có điều kiện 

Trong bài viết này tôi sẽ giúp bạn biến 1 enum đơn thuần trở thành 1 các code cực kỳ tiện lợi và nhanh chóng. 

Flutter là 1 ngôn ngữ lập trình hướng đối tượng, Tính đóng gói là nguyên tắc ẩn giấu chi tiết triển khai của một đối tượng và chỉ cho phép truy cập hoặc thao tác với nó thông qua các phương thức công khai (public methods). Điều này giúp bảo vệ dữ liệu bên trong đối tượng khỏi sự thay đổi trực tiếp từ bên ngoài, giảm thiểu rủi ro gây lỗi và tăng tính bảo trì của mã nguồn.
Vì vậy chúng ta sẽ đưa tất cả các phần hữu ích trên vào trong 1 enum vào sau đó chỉ việc sử dụng và không cần phải quan tâm đến no nữa. 

