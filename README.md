# Clique - The Modern Dating App 💖

> **Live Demo (Frontend):** [https://clique-frontend-one.vercel.app](https://clique-frontend-one.vercel.app)
> **Live API (Backend):** [https://clique-backend-kappa.vercel.app](https://clique-backend-kappa.vercel.app)
> **GitHub Repository:** [https://github.com/minhphuong4203-bot/clique](https://github.com/minhphuong4203-bot/clique)

---

### 🏗️ 1. Cấu trúc tổ chức hệ thống (System Architecture)
Hệ thống được chia làm hai phần tách biệt (Client-Server Architecture) giúp tối ưu hiệu suất và dễ dàng bảo trì, mở rộng:
- **Frontend (Client-side):** Được xây dựng bằng **Next.js 15 (App Router)** kết hợp với **Tailwind CSS v4** và **TypeScript**. Sử dụng các Server Actions và Interceptors để quản lý request/response API. Đóng vai trò hiển thị giao diện người dùng, xử lý các thao tác tương tác và theo dõi thay đổi dữ liệu theo thời gian thực (Polling mechanism).
- **Backend (Server-side):** Xây dựng bằng kiến trúc module của **NestJS** kết hợp **TypeScript**. Cung cấp các RESTful APIs an toàn với cơ chế bảo mật (xác thực bằng JWT - Access/Refresh Tokens). Logic nghiệp vụ được chia nhỏ thành các Module (Auth, User, Matching) độc lập giúp code base sạch sẽ và tuân thủ nguyên lý thiết kế hệ thống.

### 💾 2. Phương thức lưu trữ dữ liệu (Data Storage)
- **Database (Cơ sở dữ liệu chính):** Toàn bộ dữ liệu (Thông tin tài khoản, Hồ sơ Profile, Lượt thích, Danh sách Match, Lịch rảnh rỗi) được lưu trữ tập trung tại cơ sở dữ liệu quan hệ **PostgreSQL** thông qua dịch vụ lưu trữ đám mây **NeonDB**.
- **ORM:** Khai thác **Prisma ORM** để thiết kế và định nghĩa các Schema (bảng) cũng như tương tác đọc/ghi vào Database một cách an toàn (Type-safe) từ Backend.
- **Client Cache/Storage:** Trên Frontend, hệ thống sử dụng **HTTP Only Cookies** để lưu trữ Token JWT bảo mật tuyệt đối. Trạng thái giao diện tạm thời được giữ trong Local State của React.

### 💘 3. Logic Match (Tương hợp) hoạt động như thế nào?
1. Khi **User A** lướt xem hồ sơ và ấn nút `Like` **User B**, hệ thống sẽ tạo một bản ghi (record) 1 chiều từ A -> B vào cơ sở dữ liệu.
2. Backend đồng thời truy vấn chéo để kiểm tra xem trước đó **User B đã Like User A chưa**.
3. **Trường hợp 1:** Nếu B chưa mảy may quan tâm A -> Hệ thống ghi nhận lượt Like của A trong im lặng. B sẽ nhận được thông báo/gợi ý hiển thị hồ sơ của A ở những lượt lướt sau.
4. **Trường hợp 2:** Nếu B đã Like A từ trước (Mutual Like) -> Thuật toán xác nhận cả 2 đều có tình cảm. Hệ thống tự động khởi tạo một **Match (Tương hợp)** chính thức, liên kết A và B, và tính năng "Lên lịch Date" giữa hai người được bắt đầu.

### 📅 4. Logic tìm Slot trùng (Common Time Slot) hoạt động ra sao?
Tính năng Lên lịch cho phép 2 phía đối tác chọn các khoảng thời gian rảnh báo trước (Từ khung giờ đến khung giờ) trong 1 ngày cụ thể. Thuật toán xử lý:
1. Khi có sự kiện một người thêm hoặc sửa khoảng thời gian trống, Backend gộp **toàn bộ các khung giờ** rảnh mà A và B đã nộp.
2. Khởi tạo vòng lặp **quét chéo (Cross-check)**: So sánh đối chiếu chéo từng khung giờ rảnh của A với từng khung rảnh của B.
3. Nếu phát hiện A và B có chọn ở cùng **1 định dạng ngày**, hệ thống tìm khoảng giao thoa (**Overlap**) giữa 2 đoạn thẳng thời gian này bằng công thức:
   - `Overlap_Start = Max(StartA, StartB)`
   - `Overlap_End = Min(EndA, EndB)`
4. Nếu `Overlap_Start < Overlap_End` (ví dụ A rảnh 14h-18h, B rảnh 16h-20h => Trùng đoạn biên 16h-18h), thuật toán xác nhận giao điểm thành công. Hệ thống tự động gán `Overlap_Start` thành thời điểm hẹn hò chính thức (`dateScheduledAt`), lưu lại DB và thông báo ngay cho cả 2 tài khoản.

### 🚀 5. Những cải thiện nếu có thêm thời gian phát triển
- **Real-time WebSockets:** Thay thế hoàn toàn cơ chế HTTP Polling (Frontend liên tục dùng hàm chờ ngầm mỗi 10-15 giây để làm mới API) bằng **Socket.io** hai chiều. Việc sử dụng WebSocket đẩy sự kiện (Push Event) tới màn hình sẽ tiết kiệm tài nguyên Server, không gây quá tải và tối ưu độ phản hồi xuống cực thấp <1s.
- **Hệ thống Nhắn tin Mini (In-app Messaging):** Xây dựng phòng Chat cá nhân nhỏ cho phép 2 cá nhân bàn luận trực tiếp với nhau về buổi hẹn ngầm sau khi đã lên lịch thành công thay vì chỉ dừng lại ở màn hình chúc mừng tĩnh.
- **Giao diện Swipe (Tinder-like):** Nâng cao hiệu ứng Micro-animations, cho phép người dùng lướt Profile như thẻ bài vuốt trái-phải ở màn điện thoại thay vì phải ấn nút tĩnh.
- **Hệ thống Gợi ý (Recommendation Engine):** Tái cơ cấu hàm lấy ngẫu nhiên danh sách ứng viên (Get Profiles) sang thuật toán lọc theo "Điểm tương đồng" bằng Tag sở thích (Bio keyword matching) và Location.

### ✨ 6. Đề xuất thêm 3 Tính năng sáng giá cho sản phẩm
1. **"Ice-breaker AI" (AI phá băng):**
   - **Lý do:** Khá nhiều người dùng gặp khó khăn trong việc biết phải bắt chuyện như thế nào khi vừa mới Match. Tích hợp Gen AI (Gemini/ChatGPT) đọc hiểu tiểu sử (Bio) của 2 người và gợi ý sẵn 3 dòng câu chào có đính kèm điểm chung (Ví dụ: *"Cậu cũng thích nghe nhạc Lofi lúc đêm khuya giống mình à!"*) sẽ giúp tăng tỉ lệ tương tác thành công đáng kể.
2. **Gợi ý điểm hẹn hò quanh đây (Venue Recommendations):**
   - **Lý do:** Sau khi app chốt xong ngày/giờ, nếu Clique có thể sử dụng Google Maps API tìm quán Cafe lãng mạn, Rạp chiếu phim ở **vị trí trung tâm (Midpoint)** giữa nhà của A và B để làm điểm hẹn, app sẽ giải quyết toàn diện chuyến hành trình trải nghiệm người dùng từ A-Z mà họ không cần chuyển app khác để search địa điểm.
3. **Tính năng Verify (Xác thực danh tính Blind Video Chat chống mạo danh):**
   - **Lý do:** Tình trạng giả mạo danh tính/tài khoản rác trên các app hẹn hò rất cao gây mất niềm tin người dùng. Cho phép thiết lập 1 tính năng Video Chat "blur" (giấu mặt làm mờ nhòe khuôn mặt trong 3 phút đầu match nhau). Chỉ khi cả 2 cùng bấm nút thả tim giữa cuốc gọi 3 phút đấy thì màn hình mới được làm nét khuôn mặt thật. Giúp hạn chế lừa đảo và kiểm tra độ cuốn hút (Chemistry) tự nhiên nhất.
