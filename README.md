# MinLish Project - Hướng dẫn Chức năng Register, Verify OTP & Resend OTP

Dự án này triển khai hệ thống đăng ký người dùng với cơ chế xác thực mã OTP qua email, đảm bảo tính bảo mật và trải nghiệm người dùng.

**Sinh viên thực hiện:** Văn Phú Hiền
**MSSV:** 23110213

---

## 1. Backend: Chức năng Resend OTP

Chức năng này cho phép người dùng yêu cầu gửi lại mã OTP mới trong trường hợp mã cũ hết hạn hoặc không nhận được email.

### Thông tin API
- **Endpoint:** `POST /api/auth/resend-otp`
- **Body:** `{ "email": "user@example.com" }`

### Luồng xử lý (Logic)
1. **Kiểm tra người dùng:** Tìm kiếm người dùng trong cơ sở dữ liệu qua Email. Nếu không tồn tại, trả về lỗi 404.
2. **Kiểm tra trạng thái:** Nếu tài khoản đã được kích hoạt (`ACTIVE`), hệ thống sẽ từ chối gửi lại OTP.
3. **Vô hiệu hóa OTP cũ:** Tìm các mã OTP thuộc loại `REGISTER` chưa được sử dụng của người dùng này và đánh dấu là đã dùng (`isUsed: true`) để tránh xung đột.
4. **Tạo OTP mới:**
   - Sinh mã ngẫu nhiên 6 chữ số.
   - Thiết lập thời gian hết hạn (mặc định 10 phút).
   - Lưu vào bảng `Otps` trong database.
5. **Gửi Email:** Sử dụng dịch vụ Mail (Nodemailer) để gửi mã OTP mới nhất đến địa chỉ email của người dùng.

---

## 2. Frontend: Chức năng Register (Đăng ký)

Giao diện đăng ký được thiết kế hiện đại, tập trung vào tính đúng đắn của dữ liệu đầu vào.

### Đặc điểm nổi bật
- **Công nghệ sử dụng:** React Hook Form kết hợp với Zod để validation dữ liệu ngay tại client.
- **Validation:**
  - Tên đăng nhập ít nhất 3 ký tự.
  - Email đúng định dạng.
  - Mật khẩu tối thiểu 6 ký tự.
  - Kiểm tra mật khẩu xác nhận phải khớp với mật khẩu đã nhập.
- **Xử lý trạng thái:** Hiển thị trạng thái "Đang xử lý..." trên nút bấm khi đang gửi request để tránh người dùng nhấn nhiều lần (spam).
- **Chuyển hướng:** Sau khi đăng ký thành công, hệ thống sẽ tự động chuyển sang giao diện nhập mã OTP mà không làm tải lại trang (Single Page Experience).

---

## 3. Frontend: Chức năng Verify OTP (Xác thực)

Giao diện xác thực mã OTP là bước cuối cùng để kích hoạt tài khoản.

### Đặc điểm nổi bật
- **Giao diện nhập mã chuyên dụng:** Sử dụng thành phần `OtpInput` với 6 ô nhập liệu riêng biệt, tự động chuyển focus khi người dùng nhập hoặc xóa.
- **Hiệu ứng Animation:** Nếu người dùng nhập sai mã OTP, các ô nhập liệu sẽ thực hiện hiệu fade-out trước khi xóa trắng mã cũ, tạo cảm giác sinh động.
- **Tích hợp Resend:** Nút "Gửi lại" được tích hợp ngay dưới form, cho phép gọi API Resend OTP của Backend và hiển thị thông báo thành công tạm thời trong 5 giây.
- **Hoàn tất:** Khi xác thực thành công, người dùng được chuyển hướng về trang Đăng nhập kèm theo thông báo kích hoạt thành công.

---

## Hướng dẫn cài đặt & Chạy project

### Backend
1. `cd backend`
2. `npm install`
3. Cấu hình file `.env` (Database, Mail Service, JWT secret).
4. `npm run dev`

### Frontend
1. `cd frontend`
2. `npm install`
3. Cấu hình `.env.local` trỏ đến API URL của Backend.
4. `npm run dev`
