# Bus Booking System

## 1. Định nghĩa bài toán

### 1.1. Bối cảnh

Hiện nay, nhu cầu di chuyển bằng xe khách giữa các tỉnh, thành phố ngày càng phổ biến. Người dùng thường phải tìm kiếm thông tin chuyến xe, thời gian khởi hành, giá vé và số ghế còn trống thông qua nhiều nguồn khác nhau. Việc đặt vé theo phương thức thủ công có thể gây mất thời gian và khó quản lý thông tin đặt vé.

Bên cạnh đó, các nhà xe cần quản lý nhiều thông tin liên quan đến phương tiện, tuyến đường, lịch trình và vé đã được đặt. Nếu quản lý bằng phương thức thủ công, việc cập nhật số lượng ghế còn trống và theo dõi các lượt đặt vé có thể dễ xảy ra sai sót.

Vì vậy, hệ thống quản lý đặt vé xe khách trực tuyến được xây dựng nhằm cung cấp một nền tảng tập trung để người dùng tìm kiếm chuyến xe và thực hiện đặt vé, đồng thời hỗ trợ quản trị viên quản lý các dữ liệu cơ bản của hệ thống.

### 1.2. Vấn đề

Hệ thống tập trung giải quyết các vấn đề chính sau:

* Người dùng khó tìm kiếm và so sánh các chuyến xe phù hợp với nhu cầu di chuyển.
* Thông tin về tuyến đường, thời gian khởi hành, giá vé và số ghế còn trống cần được quản lý tập trung.
* Việc đặt vé thủ công gây khó khăn trong việc quản lý thông tin hành khách và số lượng vé.
* Số lượng ghế còn trống cần được cập nhật sau khi có booking.
* Người dùng cần có khả năng theo dõi các booking đã thực hiện.
* Quản trị viên cần có khả năng quản lý dữ liệu của hệ thống.

## 2. Phân chia công việc

| Thành viên           | Công việc                                                           |
| -------------------- | ------------------------------------------------------------------- |
| Nguyễn Văn Huy Hoàng | Auth, Role, User, Operator, Vehicle, Location, Route, Security |
| Bùi Công Huy         | Trip, Booking, Exception Handling, Test, Swagger/OpenAPI, Docker, Test Kaggle CPU  |

## 3. Công nghệ sử dụng

* Java 21, Spring Boot
* Spring Security, JWT
* PostgreSQL, Spring Data JPA
* Swagger/OpenAPI
* JUnit, Mockito
* Docker, Docker Compose

## 4. Kiến trúc hệ thống

Áp dụng kiến trúc phân lớp:

`API → Business → Data Access → PostgreSQL`

Các lớp được phân chia trách nhiệm rõ ràng, hỗ trợ bảo trì và kiểm thử độc lập.

## 5. Tài liệu project

Tài liệu đặc tả chi tiết được lưu tại thư mục [`docs/`](docs/).

- [`01-overview-and-requirements.md`](docs/01-overview-and-requirements.md)
- [`02-database-design.md`](docs/02-database-design.md)
- [`03-system-architecture.md`](docs/03-system-architecture.md)
- [`04-api-design.md`](docs/04-api-design.md)
- [`05-source-code-design.md`](docs/05-source-code-design.md)
- [`06-quality-attributes.md`](docs/06-quality-attributes.md)

## 6. Hướng dẫn chạy dự án (tạm thời)

### 6.1. Yêu cầu môi trường

- JDK 21
- PostgreSQL
- Maven Wrapper (đã có trong dự án)

### 6.2. Cấu hình cơ sở dữ liệu

Tạo database PostgreSQL tên `busbooking`, sau đó cấu hình trong file `src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5433/busbooking
spring.datasource.username=busbooking_user
spring.datasource.password=${DB_PASSWORD}
```

Thiết lập biến môi trường `DB_PASSWORD` bằng mật khẩu PostgreSQL của bạn. Điều chỉnh **cổng** và **thông tin đăng nhập** nếu môi trường của bạn khác.

### 6.3. Chạy dự án

Mở Terminal tại thư mục gốc của dự án và chạy lệnh trên Windows:

```powershell
.\mvnw.cmd spring-boot:run
```

Hoặc chạy trực tiếp file `BusBookingApplication.java` bằng IntelliJ IDEA.

Flyway sẽ tự động thực thi các migration để tạo bảng khi ứng dụng khởi động.

### 6.4. Truy cập tài liệu API (hiện tại chưa hỗ trợ tài liệu api swagger)

Sau khi ứng dụng khởi động thành công:

- **Swagger UI:** http://localhost:8085/swagger-ui/index.html
- **OpenAPI JSON:** http://localhost:8085/v3/api-docs

**Lưu ý:** Đây là hướng dẫn chạy tạm thời trong môi trường phát triển cục bộ. Không đưa mật khẩu hoặc thông tin bí mật vào GitHub.
