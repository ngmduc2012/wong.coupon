

# Các vấn đề Flutter Web: Hiệu Suất, SEO,...

## **1. Những Vấn Đề Khi Xây Dựng Ứng Dụng Flutter Web**

### **1.1. Hiệu Suất và Thời Gian Tải Trang**

- Hãy truy cập trang web [wongcoupon.com](https://wongcoupon.com/) này của tôi, và bạn sẽ nhận thấy thời gian ban đầu khi tải trang web rất là lâu. 
Với máy tính thì mất tầm 3s nhưng với thiết bị di động thì sẽ lâu hơn. Tôi đã cố gắng cải thiện nhất có thể cho trang web của mình nhưng vẫn không hiệu quả. 
Trước đây thời gian tải trang khoảng 4 đến 5 giây. 


### **1.2. Khó Khăn Về SEO**

- Tôi đã thử sử dụng nhiều cách để tích hợp SEO vào trang web của mình, thực sự SEO có hoạt động nhưng mà nó hoạt đông không tốt như mong đợi. Tôi đã xây dựng 
1 trang web về mã giảm giá, việc người dùng truy cập vào trang web của tôi nhanh chóng là điều bắt buộc. Tôi sử dụng tên miền và SEO nó trên Google, sau 1 thời gian
nó không còn hoạt động tốt như mong đợi, các công cụ tìm kiếm đầu coi trang web của tôi có nội dung kém chất lương vì chúng không thẻ tìm thấy văn bản trong trang web của tôi. 
Như thế sẽ rất khó khăn để các công cụ phân phối trang web này trới ngừoi dùng. 

### **1.3. Quốc Tế Hóa và Dịch Thuật**

- Bản chất flutter web sử dụng CanvasKit renderer để tạo ra giao diện. Điều này có nghĩa là toàn bộ giao diện, bao gồm cả văn bản, được vẽ lên một canvas duy nhất thông qua WebGL và WebAssembly, nên text cũng không ngoại lệ, đó là lý do tại sao công cụ dịch thuật không thể tìm ra text để dịch thuật cho trang web. 
Nhưng điều này cũng có nghĩa là nó sẽ có giao diện tương tự như trên mobile. 

### **1.4. Gỡ Lỗi và Kiểm Tra Phần Tử**

- Điều này cũng đồng nghĩa chúng ta không thể tìm thấy element bằng Developer Tools.

## **2. Chiến Lược Khắc Phục Các Thách Thức Của Flutter Web**

### **2.1. Cải Thiện Thời Gian Tải Trang**

- **Tối ưu hóa mã:**
    - Loại bỏ các import không cần thiết, không giống như mobile, việc chỉ import các file cần dùng sẽ làm giảm thời gian xây dựng trang web.
- **Tải Trì Hoãn (Deferred Loading):**
    - Áp dụng lazy loading cho các widget không quan trọng. Tôi áp dụng nó với việc điều hướng trang web, trang web sẽ không phải tải các phần khác trước khi sử dụng. 
- **Triển vọng với WebAssembly:**
    - **Hiểu biết về WebAssembly:**
        - Flutter đang tiến tới hỗ trợ WebAssembly để giảm thời gian tải trang.
        - Tham khảo: [Tài liệu Flutter WebAssembly](https://docs.flutter.dev/platform-integration/web/wasm).
    - **Lợi ích tiềm năng:**
        - Hiệu suất gần như ứng dụng native.
        - Kích thước gói nhỏ hơn. 


### **2.2. Triển Khai Thiết Kế Responsive**

- Không giống ứng dụng di động, ứng dụng web cần đáp ứng trên nhiều kích thước màn hình. Tôi đã có 1 bài viết về
  responsive, bạn có thể tham khảo tại đây *LINK*
- Nếu bạn muốn trải nghiệm responsive trên trang web của tôi, hãy truy cập [wongcoupon.com](https://wongcoupon.com/)
  
## **3. Kết luận Web Flutter phù hợp với loại web nào hơn**

### **3.1. Phù hợp**

- Sau khi xây dựng trang web bằng flutter, tôi đã nhận ra rằng nó sẽ phù hợp với các trang web quản trị, khi chúng ta muốn tận dụng luôn ngôn ngữ lập trình Flutter để xây dựng trang web.

### **3.2. Không phù hợp**

- Như đã đề cập ở trên, Flutter web thực sự không phù hợp với các trang web SEO cần tốc độ tải trang nhanh chóng. Các trang web thương mại phục vụ người dùng cuối sẽ không phù hợp khi xây dựng
bằng Flutter. 

