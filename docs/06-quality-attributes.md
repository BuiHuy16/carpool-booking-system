# 6. Quality Attributes

## 6.1. Bảo mật

**Mục tiêu:** Bảo vệ tài khoản người dùng và các API của hệ thống.

**Giải pháp:**

* Sử dụng JWT để xác thực người dùng.
* Sử dụng Spring Security để phân quyền USER và ADMIN.
* Mã hóa mật khẩu bằng BCrypt.
* Kiểm tra quyền sở hữu khi xem hoặc hủy booking.
* Lưu thông tin nhạy cảm trong biến môi trường.

## 6.2. Hiệu năng

**Mục tiêu:** Đảm bảo API phản hồi nhanh khi tìm kiếm chuyến xe và tra cứu booking.

**Giải pháp:**

* Tối ưu truy vấn PostgreSQL.
* Tạo index cho các trường thường xuyên tìm kiếm.
* Sử dụng phân trang cho danh sách dữ liệu.
* Đo thời gian phản hồi bằng kiểm thử tải.

## 6.3. Tính toàn vẹn dữ liệu

**Mục tiêu:** Đảm bảo dữ liệu đặt vé và số ghế luôn chính xác.

**Giải pháp:**

* Sử dụng transaction khi đặt và hủy vé.
* Kiểm tra số ghế trước khi xác nhận booking.
* Ngăn đặt vượt số ghế còn trống bằng cơ chế xử lý request đồng thời.
* Sử dụng khóa ngoại và các ràng buộc database.
* Chỉ hoàn ghế một lần khi hủy booking.

## 6.4. Khả năng bảo trì

**Mục tiêu:** Giúp mã nguồn dễ đọc, sửa đổi và mở rộng.

**Giải pháp:**

* Tổ chức mã nguồn theo các layer API, Business, Domain và Data.
* Tách biệt controller, service, repository và entity.
* Sử dụng repository interface để giảm phụ thuộc vào công nghệ database.
* Viết tài liệu kiến trúc và quy ước commit rõ ràng.

## 6.5. Khả năng kiểm thử

**Mục tiêu:** Đảm bảo các chức năng hoạt động đúng và phát hiện lỗi sớm.

**Giải pháp:**

* Viết unit test cho `TripService` và `BookingService`.
* Kiểm thử API đăng nhập, tìm kiếm chuyến và đặt vé.
* Viết integration test cho các luồng nghiệp vụ chính.
* Kiểm thử các trường hợp thiếu ghế, sai quyền và hủy booking.

## 6.6. Khả năng triển khai

**Mục tiêu:** Đảm bảo hệ thống dễ cài đặt và chạy trên các môi trường khác nhau.

**Giải pháp:**

* Sử dụng Maven Wrapper để build ứng dụng.
* Đóng gói bằng Docker.
* Sử dụng Docker Compose để chạy ứng dụng cùng PostgreSQL.
* Quản lý cấu hình bằng biến môi trường.
* Sử dụng Flyway để quản lý database migration.

## 6.7. Kế hoạch cải tiến

* **Phase 1:** Hoàn thiện phân lớp, JWT, xử lý transaction, kiểm thử cơ bản, Swagger và Docker.
* **Phase 2:** Đo hiệu năng, kiểm thử tải, kiểm tra đặt vé đồng thời và tối ưu truy vấn database.

## 6.8. Kết luận

Hệ thống ưu tiên bảo mật, tính toàn vẹn dữ liệu và khả năng kiểm thử. Các thuộc tính này giúp đảm bảo quy trình đặt vé đáng tin cậy, đồng thời tạo nền tảng để bảo trì và cải tiến hệ thống trong giai đoạn tiếp theo.
