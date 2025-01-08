Web Flutter và các vấn đề

1. Các vấn đề tồn đọng với web làm bằng Flutter 

Trang web này được xây dựng bằng flutter 100%. Bạn có thể trải nghiệm nó trực tiếp tại đây từ tốc độ tải, SEO đến các vến đề khác của web 
Vấn đề thấy đầu tiên là tốc độ tải trang. Flutter web gần như được sinh ra bằng javaScript trong file main.dart.js nên sẽ mất 1 khoảng thời gian sinh code. 
Điều này sẽ gây ra 1 số vấn đề 
- khi các công cụ SEO sẽ khó nhận diện hơn, 
- tốc độ tải trang lần đầu sẽ khá lâu, tuy thế những lần sau tốc độ sẽ cải thiện hơn nhiều. Vấn đề tốc độ tải trang có thể sẽ biến mất trong tương lai khi Flutter đang hướng đến việc phát triển WebAssembly https://docs.flutter.dev/platform-integration/web/wasm, sẽ khiến cho tốc độ tải trang như nhau với mọi ngôn ngữ lập trình.  
- Không thể sử dụng dịch ngôn ngữ trên trang. 
- Không thể tìm được các element bằng inspect vì giao diện không được xây dựng 100% là html 

2. Các thách thức khi xây dựng 1 trang web FLutter 

- Responsive giao diện, đây là 1 vấn đề mà các lập trình viên mobile sẽ ít phải đối mặt, khi lập trình web cần đảm bảo giao diện tối ưu cho tất cả các màn hình. 
Bạn có thể tham khảo bài viết về responsive của tôi tại  *LINK* 

- Tạo đường dẫn tuỳ chỉnh cho web, hiện tại tôi đang sử dụng go_router https://pub.dev/packages/go_router để tạo các đừng dẫn cho web. 
- SEO cũng là 1 vấn đề của flutter, hiện tại vẫn chưa có giải pháp hiệu quả cho SEO 

Tuyệt đối không được dùng src.dart file ko cần thiết thì ko đc import , tôi giảm từ 8s loading web xuống 5s, khi bỏ src.dart, nó chỉu hiệu quả với app mobile 