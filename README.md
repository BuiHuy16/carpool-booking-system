### 1. Bảng USERS (Quản lý tài khoản & Xác thực)
Bảng này lưu thông tin định danh chung cho mọi đối tượng đăng nhập vào hệ thống, phục vụ trực tiếp cho yêu cầu Bảo mật.

| Thuộc tính | Kiểu dữ liệu & Ràng buộc | Ý nghĩa nghiệp vụ & Lý do thiết kế |
| :--- | :--- | :--- |
| **id** | `INT / BIGINT (PK, Auto Increment)` | Khóa chính định danh duy nhất mỗi người dùng. Dùng số nguyên tự tăng giúp Index nhẹ và JOIN nhanh hơn UUID. |
| **name** | `VARCHAR(100) NOT NULL` | Họ và tên hiển thị của người dùng (hành khách hoặc tài xế). |
| **email** | `VARCHAR(150) UNIQUE NOT NULL` | Dùng làm tài khoản đăng nhập (`POST /api/auth/login`). Ràng buộc UNIQUE ngăn việc đăng ký trùng email. |
| **password** | `VARCHAR(255) NOT NULL` | Lưu chuỗi mật khẩu đã băm (hash bằng bcrypt/argon2), tuyệt đối không lưu mật khẩu thuần (plain text). |
| **phone** | `VARCHAR(20) NOT NULL` | Số điện thoại liên hệ để tài xế gọi cho khách khi đón hoặc ngược lại. |
| **role** | `VARCHAR(20) NOT NULL` | Phân quyền người dùng (`PASSENGER`, `DRIVER`, `ADMIN`). Middleware xác thực sẽ đọc trường này từ Token để kiểm tra quyền gọi API. |
| **created_at** | `TIMESTAMP DEFAULT CURRENT_TIMESTAMP` | Lưu mốc thời gian tạo tài khoản, tự động ghi nhận bởi hệ quản trị CSDL. |

---

### 2. Bảng DRIVERS (Hồ sơ nghiệp vụ Tài xế)
Tách riêng thông tin hành nghề lái xe ra khỏi bảng USERS để tránh làm bảng USERS bị dư thừa các cột NULL đối với hành khách thông thường (đảm bảo dạng chuẩn 3NF).

| Thuộc tính | Kiểu dữ liệu & Ràng buộc | Ý nghĩa nghiệp vụ & Lý do thiết kế |
| :--- | :--- | :--- |
| **id** | `INT / BIGINT (PK, Auto Increment)` | Khóa chính định danh hồ sơ tài xế. |
| **user_id** | `INT UNIQUE (FK → USERS.id)` | Khóa ngoại trỏ về bảng USERS. Ràng buộc UNIQUE ép quan hệ 1–1: mỗi tài khoản USERS chỉ được tạo tối đa 1 hồ sơ tài xế. |
| **license_number** | `VARCHAR(50) UNIQUE NOT NULL` | Số giấy phép lái xe (bằng lái). UNIQUE để đảm bảo 1 bằng lái không bị dùng đăng ký cho 2 tài xế. |
| **license_expiry** | `DATE NOT NULL` | Ngày hết hạn bằng lái. Tầng Nghiệp vụ (Business Layer) có thể kiểm tra hạn bằng lái trước khi cho phép tài xế mở chuyến. |
| **status** | `VARCHAR(20) NOT NULL` | Trạng thái hoạt động của tài xế (`ACTIVE`: được phép mở chuyến, `INACTIVE`: đang tạm khóa/ngừng chạy). |
| **created_at** | `TIMESTAMP DEFAULT CURRENT_TIMESTAMP` | Thời điểm tài khoản được kích hoạt hồ sơ tài xế. |

---

### 3. Bảng VEHICLES (Phương tiện vận chuyển)
Quản lý danh sách xe của tài xế. Một tài xế có thể đăng ký nhiều xe (quan hệ 1–N từ DRIVERS sang VEHICLES).

| Thuộc tính | Kiểu dữ liệu & Ràng buộc | Ý nghĩa nghiệp vụ & Lý do thiết kế |
| :--- | :--- | :--- |
| **id** | `INT / BIGINT (PK, Auto Increment)` | Khóa chính định danh từng chiếc xe. |
| **driver_id** | `INT (FK → DRIVERS.id)` | Xác định chiếc xe này thuộc quyền quản lý của tài xế nào. |
| **license_plate** | `VARCHAR(20) UNIQUE NOT NULL` | Biển số xe (VD: 30K-123.45). UNIQUE để chống trùng lặp phương tiện trong hệ thống. |
| **brand** | `VARCHAR(50) NOT NULL` | Hãng sản xuất xe (VD: Toyota, Hyundai, VinFast). |
| **model** | `VARCHAR(50) NOT NULL` | Tên dòng xe (VD: Vios, Accent, VF8) giúp khách nhận diện xe khi đón. |
| **seat_capacity** | `INT NOT NULL` | Số ghế chở khách tối đa của xe (VD: 4 hoặc 7). Tầng Nghiệp vụ dùng cột này để kiểm tra: khi tạo chuyến mới, số ghế mở bán (`total_seats`) không được vượt quá `seat_capacity`. |
| **created_at** | `TIMESTAMP DEFAULT CURRENT_TIMESTAMP` | Thời điểm phương tiện được thêm vào hệ thống. |

---

### 4. Bảng TRIPS (Chuyến xe ghép)
Lưu thông tin các chuyến xe được tài xế mở bán ghế. Đây là bảng bị truy vấn đọc (GET) và cập nhật (UPDATE) nhiều nhất.

| Thuộc tính | Kiểu dữ liệu & Ràng buộc | Ý nghĩa nghiệp vụ & Lý do thiết kế |
| :--- | :--- | :--- |
| **id** | `INT / BIGINT (PK, Auto Increment)` | Khóa chính định danh chuyến đi. |
| **driver_id** | `INT (FK → DRIVERS.id)` | Tài xế chịu trách nhiệm chạy chuyến này. |
| **vehicle_id** | `INT (FK → VEHICLES.id)` | Chiếc xe được sử dụng trong chuyến này. Lưu cả `driver_id` và `vehicle_id` giúp giữ nguyên lịch sử chính xác ngay cả khi sau này tài xế đổi sang chiếc xe khác. |
| **origin** | `VARCHAR(100) NOT NULL` | Điểm xuất phát (VD: 'Hà Nội'). Dùng làm tham số lọc khi khách tìm xe. |
| **destination** | `VARCHAR(100) NOT NULL` | Điểm đến (VD: 'Nam Định'). Kết hợp với `origin` để đánh Index tối ưu tìm kiếm ở Pha 2. |
| **departure_time** | `TIMESTAMP NOT NULL` | Ngày giờ khởi hành của chuyến xe. |
| **total_seats** | `INT NOT NULL` | Tổng số ghế mở bán ban đầu của chuyến (nhỏ hơn hoặc bằng `seat_capacity` của xe). |
| **available_seats** | `INT NOT NULL` | Thuộc tính quan trọng nhất cho Pha 2: Số ghế trống còn lại hiện tại. Giảm xuống khi có khách đặt (`POST /bookings`) và tăng lên khi khách hủy (`DELETE /bookings/{id}`). |
| **price** | `DECIMAL(10,2) NOT NULL` | Giá tiền niêm yết cho 1 ghế trên chuyến xe này. Dùng DECIMAL thay vì FLOAT để không bị sai số làm tròn tiền tệ. |
| **status** | `VARCHAR(20) NOT NULL` | Trạng thái chuyến (`OPEN`: Đang nhận khách, `FULL`: Hết chỗ, `COMPLETED`: Đã chạy xong, `CANCELLED`: Hủy chuyến). |
| **created_at** | `TIMESTAMP DEFAULT CURRENT_TIMESTAMP` | Thời điểm chuyến xe được đăng lên hệ thống. |

---

### 5. Bảng BOOKINGS (Đơn đặt chỗ của hành khách)
Ghi nhận giao dịch đặt ghế của hành khách trên một chuyến xe cụ thể.

| Thuộc tính | Kiểu dữ liệu & Ràng buộc | Ý nghĩa nghiệp vụ & Lý do thiết kế |
| :--- | :--- | :--- |
| **id** | `INT / BIGINT (PK, Auto Increment)` | Khóa chính định danh đơn đặt chỗ. |
| **trip_id** | `INT (FK → TRIPS.id)` | Khóa ngoại xác định đơn này đặt vào chuyến xe nào. |
| **passenger_id** | `INT (FK → USERS.id)` | Khóa ngoại xác định người đặt (lấy từ thông tin User trong JWT Token khi qua Middleware xác thực). |
| **pickup_address** | `VARCHAR(255) NOT NULL` | Địa chỉ đón khách tận nơi (đặc trưng của xe ghép liên tỉnh). |
| **dropoff_address** | `VARCHAR(255) NOT NULL` | Địa chỉ trả khách tận nơi. |
| **seats** | `INT NOT NULL` | Số lượng ghế khách đặt trong đơn này (VD: đặt 2 ghế). |
| **total_price** | `DECIMAL(10,2) NOT NULL` | Tổng tiền thanh toán của đơn (`= seats * TRIPS.price`). Lý do phải lưu cột này: Để chốt cứng giá tại thời điểm đặt; nếu sau đó tài xế đổi giá `TRIPS.price` thì lịch sử đơn hàng của khách cũ không bị nhảy giá theo. |
| **status** | `VARCHAR(20) NOT NULL` | Trạng thái đơn (`CONFIRMED`: Đặt thành công, `CANCELLED`: Khách đã hủy đơn). Khi gọi `DELETE /api/bookings/{id}`, thực chất ta đổi status thành `'CANCELLED'` (Soft Delete) và cộng trả lại số `seats` vào `TRIPS.available_seats`. |
| **created_at** | `TIMESTAMP DEFAULT CURRENT_TIMESTAMP` | Thời điểm đơn đặt chỗ được tạo. |

## Cấu trúc Project

```text
carpoolbooking/
│
│
├── .mvn/
│   └── wrapper/
│       └── maven-wrapper.properties       # Cấu hình Maven Wrapper
│
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/carpoolbooking/
│   │   │       │
│   │   │       ├── CarpoolbookingApplication.java
│   │   │       │   # Entry point của Spring Boot application
│   │   │       │
│   │   │       ├── config/
│   │   │       │   ├── OpenApiConfig.java
│   │   │       │   │   # Cấu hình Swagger/OpenAPI
│   │   │       │   ├── PasswordEncoderConfig.java
│   │   │       │   │   # Cấu hình mã hóa mật khẩu
│   │   │       │   └── SecurityConfig.java
│   │   │       │       # Cấu hình Spring Security
│   │   │       │
│   │   │       ├── controller/
│   │   │       │   ├── AuthController.java
│   │   │       │   │   # API đăng ký và đăng nhập
│   │   │       │   ├── BookingController.java
│   │   │       │   │   # API quản lý booking
│   │   │       │   ├── DriverController.java
│   │   │       │   │   # API quản lý tài xế
│   │   │       │   ├── TripController.java
│   │   │       │   │   # API quản lý chuyến xe
│   │   │       │   ├── UserController.java
│   │   │       │   │   # API quản lý người dùng
│   │   │       │   └── VehicleController.java
│   │   │       │       # API quản lý phương tiện
│   │   │       │
│   │   │       ├── dto/
│   │   │       │   ├── auth/
│   │   │       │   │   ├── LoginRequest.java
│   │   │       │   │   │   # Dữ liệu request đăng nhập
│   │   │       │   │   ├── LoginResponse.java
│   │   │       │   │   │   # Dữ liệu response đăng nhập
│   │   │       │   │   └── RegisterRequest.java
│   │   │       │   │       # Dữ liệu request đăng ký
│   │   │       │   │
│   │   │       │   ├── booking/
│   │   │       │   │   ├── BookingResponse.java
│   │   │       │   │   │   # Dữ liệu response booking
│   │   │       │   │   └── CreateBookingRequest.java
│   │   │       │   │       # Dữ liệu request tạo booking
│   │   │       │   │
│   │   │       │   ├── driver/
│   │   │       │   │   ├── CreateDriverRequest.java
│   │   │       │   │   │   # Dữ liệu request tạo tài xế
│   │   │       │   │   ├── DriverResponse.java
│   │   │       │   │   │   # Dữ liệu response tài xế
│   │   │       │   │   └── UpdateDriverRequest.java
│   │   │       │   │       # Dữ liệu request cập nhật tài xế
│   │   │       │   │
│   │   │       │   ├── trip/
│   │   │       │   │   ├── CreateTripRequest.java
│   │   │       │   │   │   # Dữ liệu request tạo chuyến
│   │   │       │   │   ├── TripResponse.java
│   │   │       │   │   │   # Dữ liệu response chuyến xe
│   │   │       │   │   ├── TripSearchRequest.java
│   │   │       │   │   │   # Dữ liệu request tìm kiếm chuyến
│   │   │       │   │   └── UpdateTripRequest.java
│   │   │       │   │       # Dữ liệu request cập nhật chuyến
│   │   │       │   │
│   │   │       │   ├── user/
│   │   │       │   │   ├── UpdateUserRequest.java
│   │   │       │   │   │   # Dữ liệu request cập nhật người dùng
│   │   │       │   │   └── UserResponse.java
│   │   │       │   │       # Dữ liệu response người dùng
│   │   │       │   │
│   │   │       │   └── vehicle/
│   │   │       │       ├── CreateVehicleRequest.java
│   │   │       │       │   # Dữ liệu request tạo phương tiện
│   │   │       │       ├── UpdateVehicleRequest.java
│   │   │       │       │   # Dữ liệu request cập nhật phương tiện
│   │   │       │       └── VehicleResponse.java
│   │   │           # Dữ liệu response phương tiện
│   │   │
│   │   │       ├── entity/
│   │   │       │   ├── Booking.java
│   │   │       │   │   # Entity ánh xạ bảng BOOKINGS
│   │   │       │   ├── Driver.java
│   │   │       │   │   # Entity ánh xạ bảng DRIVERS
│   │   │       │   ├── Trip.java
│   │   │       │   │   # Entity ánh xạ bảng TRIPS
│   │   │       │   ├── User.java
│   │   │       │   │   # Entity ánh xạ bảng USERS
│   │   │       │   └── Vehicle.java
│   │   │       │       # Entity ánh xạ bảng VEHICLES
│   │   │       │
│   │   │       ├── enums/
│   │   │       │   ├── BookingStatus.java
│   │   │       │   │   # Trạng thái booking
│   │   │       │   ├── DriverStatus.java
│   │   │       │   │   # Trạng thái tài xế
│   │   │       │   ├── TripStatus.java
│   │   │       │   │   # Trạng thái chuyến xe
│   │   │       │   └── UserRole.java
│   │   │       │       # Vai trò người dùng
│   │   │       │
│   │   │       ├── exception/
│   │   │       │   ├── BadRequestException.java
│   │   │       │   │   # Lỗi request không hợp lệ
│   │   │       │   ├── ErrorResponse.java
│   │   │       │   │   # Cấu trúc response khi xảy ra lỗi
│   │   │       │   ├── GlobalExceptionHandler.java
│   │   │       │   │   # Xử lý exception tập trung
│   │   │       │   ├── ResourceNotFoundException.java
│   │   │       │   │   # Lỗi không tìm thấy resource
│   │   │       │   └── UnauthorizedException.java
│   │   │       │       # Lỗi không có quyền truy cập
│   │   │       │
│   │   │       ├── mapper/
│   │   │       │   ├── BookingMapper.java
│   │   │       │   │   # Chuyển đổi Booking Entity ↔ DTO
│   │   │       │   ├── DriverMapper.java
│   │   │       │   │   # Chuyển đổi Driver Entity ↔ DTO
│   │   │       │   ├── TripMapper.java
│   │   │       │   │   # Chuyển đổi Trip Entity ↔ DTO
│   │   │       │   ├── UserMapper.java
│   │   │       │   │   # Chuyển đổi User Entity ↔ DTO
│   │   │       │   └── VehicleMapper.java
│   │   │       │       # Chuyển đổi Vehicle Entity ↔ DTO
│   │   │       │
│   │   │       ├── repository/
│   │   │       │   ├── BookingRepository.java
│   │   │       │   │   # Truy cập dữ liệu BOOKINGS
│   │   │       │   ├── DriverRepository.java
│   │   │       │   │   # Truy cập dữ liệu DRIVERS
│   │   │       │   ├── TripRepository.java
│   │   │       │   │   # Truy cập dữ liệu TRIPS
│   │   │       │   ├── UserRepository.java
│   │   │       │   │   # Truy cập dữ liệu USERS
│   │   │       │   └── VehicleRepository.java
│   │   │       │       # Truy cập dữ liệu VEHICLES
│   │   │       │
│   │   │       ├── security/
│   │   │       │   ├── CustomUserDetailsService.java
│   │   │       │   │   # Load thông tin user cho Spring Security
│   │   │       │   ├── JwtAuthenticationFilter.java
│   │   │       │   │   # Filter xác thực JWT
│   │   │       │   └── JwtService.java
│   │   │       │       # Tạo và kiểm tra JWT
│   │   │       │
│   │   │       └── service/
│   │   │           ├── AuthService.java
│   │   │           │   # Xử lý nghiệp vụ đăng ký/đăng nhập
│   │   │           ├── BookingService.java
│   │   │           │   # Xử lý nghiệp vụ đặt và hủy booking
│   │   │           ├── DriverService.java
│   │   │           │   # Xử lý nghiệp vụ tài xế
│   │   │           ├── TripService.java
│   │   │           │   # Xử lý nghiệp vụ chuyến xe
│   │   │           ├── UserService.java
│   │   │           │   # Xử lý nghiệp vụ người dùng
│   │   │           └── VehicleService.java
│   │   │               # Xử lý nghiệp vụ phương tiện
│   │   │
│   │   └── resources/
│   │       ├── application.properties
│   │       │   # Cấu hình ứng dụng và database
│   │       │
│   │       └── db/
│   │           └── migration/
│   │               ├── V1__create_users.sql
│   │               │   # Tạo bảng USERS
│   │               ├── V2__create_drivers.sql
│   │               │   # Tạo bảng DRIVERS
│   │               ├── V3__create_vehicles.sql
│   │               │   # Tạo bảng VEHICLES
│   │               ├── V4__create_trips.sql
│   │               │   # Tạo bảng TRIPS
│   │               └── V5__create_bookings.sql
│   │                   # Tạo bảng BOOKINGS
│   │
│   └── test/
│       └── java/
│           └── com/example/carpoolbooking/
│               └── CarpoolbookingApplicationTests.java
│                  # Test cơ bản của Spring Boot
├── .gitattributes                         
├── .gitignore                             
├── mvnw                                   
├── mvnw.cmd                               
├── pom.xml                                # Cấu hình Maven và dependencies