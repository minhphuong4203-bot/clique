# Clique – Mini Dating App 💖

## 🔗 Live Demo
- **Frontend:** https://clique-frontend-one.vercel.app
- **Backend API:** https://clique-backend-kappa.vercel.app

## 💻 Source Code
- **Main Repository:** https://github.com/minhphuong4203-bot/clique
- **Frontend Repo:** https://github.com/minhphuong4203-bot/clique-frontend
- **Backend Repo:** https://github.com/minhphuong4203-bot/clique-backend

---

# Clique – Mini Dating App 💖

## 1. Cách tôi tổ chức hệ thống

### Tổng quan kiến trúc

Hệ thống được thiết kế theo mô hình Client–Server Architecture:

- Frontend: Next.js 15 (App Router), TypeScript, TailwindCSS
- Backend: NestJS (Modular Architecture)
- Database: PostgreSQL (NeonDB)
- ORM: Prisma

Frontend chịu trách nhiệm:
- Render UI
- Gửi API request
- Quản lý state phía client

Backend chịu trách nhiệm:
- Xử lý business logic
- Authentication (JWT)
- Match logic
- Common slot calculation
- Database interaction

---

### Cách tổ chức Backend

Backend sử dụng feature-based modular architecture:

src/
 ├── auth/
 ├── users/
 ├── likes/
 ├── matches/
 ├── availability/
 ├── common-slot/
 └── prisma/

Mỗi module gồm:
- controller (handle request)
- service (business logic)
- dto (validate input)
- prisma model

Lý do:
- Tách biệt rõ trách nhiệm
- Dễ maintain
- Dễ scale

---

### Cách tổ chức Frontend

app/
 ├── login/
 ├── register/
 ├── dashboard/
 ├── matches/
 ├── profile/

- Mỗi route là một page
- Components tái sử dụng đặt trong /components
- API call tách riêng thành service layer

---

## 2. Tôi lưu data bằng gì?

Dữ liệu được lưu bằng PostgreSQL (NeonDB).

Các bảng chính:

- User
- Profile
- Like
- Match
- Availability
- ScheduledDate

Authentication:
- JWT Access Token
- JWT Refresh Token
- Lưu trong HTTP-only cookies

Lý do không dùng localStorage:

- Không bảo mật
- Không đồng bộ giữa nhiều user
- Không scale
- Dễ bị chỉnh sửa phía client

Database đảm bảo:
- Data integrity
- Multi-user support
- Production-ready

---

## 3. Logic Match hoạt động thế nào?

Nguyên tắc:
Match xảy ra khi có mutual like.

Quy trình:

1. User A like User B
   → tạo record trong bảng Like

2. Backend kiểm tra:
   Có tồn tại record Like (userB → userA) không?

3. Nếu có:
   - Tạo record trong bảng Match
   - Xóa hai record Like để tránh duplicate

Pseudo code:

```ts
if (existsLike(userB, userA)) {
   createMatch(userA, userB)
   deleteLike(userA, userB)
   deleteLike(userB, userA)
}
```

Lý do xử lý ở backend:
- Tránh fake match từ client
- Đảm bảo atomic operation
- Đảm bảo consistency

---

## 4. Logic tìm slot trùng hoạt động thế nào?

Mỗi user có nhiều availability:

{
  date: "2026-02-20",
  startTime: "14:00",
  endTime: "18:00"
}

Mục tiêu:
Tìm khoảng thời gian giao nhau giữa hai user trong cùng một ngày.

Công thức:

Overlap_Start = max(StartA, StartB)
Overlap_End   = min(EndA, EndB)

Nếu:

Overlap_Start < Overlap_End

→ tồn tại khoảng giao nhau.

Quy trình:

1. Lấy availability của user A và B
2. Group theo date
3. So sánh từng slot cùng ngày
4. Tính overlap
5. Chọn slot hợp lệ đầu tiên
6. Lưu vào bảng ScheduledDate

Độ phức tạp:

Nếu:
- A có n slots
- B có m slots

Worst case: O(n × m)

Có thể tối ưu bằng:
- Sort theo thời gian
- Two-pointer technique

---

## 5. Nếu có thêm thời gian tôi sẽ cải thiện gì?

1. Sử dụng database transaction cho match + schedule
2. Thêm WebSocket để realtime notification
3. Thêm Redis để caching profile list
4. Tối ưu index database

---

## 6. 1–3 tính năng đề xuất thêm và lý do

### 1. In-app Messaging

Lý do:
- Tăng retention
- Hoàn thiện trải nghiệm dating

### 2. Swipe-based Interaction (Tinder-like)

Lý do:
- UX trực quan
- Tăng engagement

### 3. Smart Match Suggestion

Ví dụ:
- Ưu tiên user có cùng thời gian rảnh
- Ưu tiên mutual interest

Lý do:
- Tăng xác suất match
- Tăng conversion rate

---

# Tổng kết

Hệ thống được xây dựng theo hướng:

- Backend-driven business logic
- Clean modular architecture
- Production-ready mindset
- Dễ mở rộng trong tương lai