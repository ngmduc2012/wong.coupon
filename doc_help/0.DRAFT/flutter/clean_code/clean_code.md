

Các biện pháp giúp code sạch và tường minh cho đội nhóm  

1. Dùng chung 1 form code cho tất cả các thành viên trong nhóm, môi ng đều có 1 phong cách code riêng, việc đọc code của ng khác cũng khiến khá mất thừoi gian, việc dùng chugn 1 fomr code cho cả nhóm là cần thiết
2. dùng lint, - follơư dart efect code. 
3. Sử dụng kiến trức clean architectủre 
4. Sử dụng cách tách biệt các plugin bên thứ 3 với dự án chính bằng các class riêng biêt, vì các vấn đề plugin đó không còn đc update. 
5. Các phần liên quan đến native cần tách biệt ra các plugin để đảm bảo tường minh và dẽ bảo trì, cũng dễ dàng sửa các lỗi native không xác định bằng cách tạo project mới 