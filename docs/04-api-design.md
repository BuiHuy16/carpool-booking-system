# 4. Thiết kế API

## 4.1. API Conventions

### 4.1.1. Base URL

```text
/api
```

Các endpoint được tổ chức theo resource:

```text
/api/auth
/api/users
/api/trips
/api/bookings
/api/admin
```

### 4.1.2. Request/Response Format

API sử dụng **JSON** cho request và response.

Request:

```http
Content-Type: application/json
```

Response thành công:

```http
Content-Type: application/json
```


### 4.1.3. Authentication Header

Các API yêu cầu đăng nhập sử dụng:

```http
Authorization: Bearer <JWT_TOKEN>
```

Các API công khai như đăng ký, đăng nhập và tìm kiếm chuyến xe không bắt buộc token.

---

## 4.2. Authentication APIs

### 4.2.1. Register

```http
POST /api/auth/register
```

Đăng ký tài khoản người dùng.

Request:

```json
{
  "fullName": "Nguyen Van A",
  "email": "a@example.com",
  "password": "123456",
  "phone": "0901234567"
}
```

Response `201 Created`:

```json
{
  "id": 1,
  "fullName": "Nguyen Van A",
  "email": "a@example.com",
  "phone": "0901234567",
  "role": "USER"
}
```

### 4.2.2. Login

```http
POST /api/auth/login
```

Đăng nhập và nhận JWT.

Request:

```json
{
  "email": "a@example.com",
  "password": "123456"
}
```

Response `200 OK`:

```json
{
  "token": "eyJhbGciOiJIUzI1NiJ9...",
  "tokenType": "Bearer",
  "expiresIn": 86400,
  "user": {
    "id": 1,
    "fullName": "Nguyen Van A",
    "email": "a@example.com",
    "role": "USER"
  }
}
```

### 4.2.3. Get Current User

```http
GET /api/auth/me
```

**Authentication:** Required

Lấy thông tin tài khoản đang đăng nhập.

Response:

```json
{
  "id": 1,
  "fullName": "Nguyen Van A",
  "email": "a@example.com",
  "phone": "0901234567",
  "role": "USER"
}
```

---

## 4.3. User APIs

### 4.3.1. Get User Profile

```http
GET /api/users/me
```

**Authentication:** Required

Lấy thông tin profile của người dùng hiện tại.

### 4.3.2. Update User Profile

```http
PUT /api/users/me
```

**Authentication:** Required

Request:

```json
{
  "fullName": "Nguyen Van B",
  "phone": "0912345678"
}
```

Response:

```json
{
  "id": 1,
  "fullName": "Nguyen Van B",
  "email": "a@example.com",
  "phone": "0912345678",
  "role": "USER"
}
```

### 4.3.3. Get Booking History

```http
GET /api/users/me/bookings
```

**Authentication:** Required

Lấy danh sách các booking của người dùng hiện tại.

Response:

```json
[
  {
    "id": 10,
    "bookingCode": "BK20261009001",
    "tripId": 5,
    "passengerName": "Nguyen Van A",
    "passengerPhone": "0901234567",
    "quantity": 2,
    "totalAmount": 300000,
    "status": "CONFIRMED",
    "bookedAt": "2026-10-09T10:30:00"
  }
]
```

---

## 4.4. Trip APIs

### 4.4.1. Search Trips

```http
GET /api/trips
```

**Authentication:** Not required

Tìm kiếm các chuyến xe theo điểm đi, điểm đến và ngày khởi hành.

Query parameters:

```text
originId
destinationId
departureDate
```

Example:

```http
GET /api/trips?originId=1&destinationId=2&departureDate=2026-10-15
```

Response:

```json
[
  {
    "id": 5,
    "origin": "Ha Noi",
    "destination": "Da Nang",
    "departureTime": "2026-10-15T08:00:00",
    "arrivalTime": "2026-10-15T20:00:00",
    "price": 250000,
    "availableSeats": 20,
    "vehicleType": "BUS",
    "operatorName": "ABC Bus",
    "status": "SCHEDULED"
  }
]
```

### 4.4.2. Get Trip Detail

```http
GET /api/trips/{tripId}
```

**Authentication:** Not required

Lấy thông tin chi tiết của một chuyến xe.

Example:

```http
GET /api/trips/5
```

Response:

```json
{
  "id": 5,
  "route": {
    "origin": "Ha Noi",
    "destination": "Da Nang",
    "estimatedDurationMinutes": 720
  },
  "vehicle": {
    "vehicleType": "BUS",
    "seatCapacity": 40
  },
  "departureTime": "2026-10-15T08:00:00",
  "arrivalTime": "2026-10-15T20:00:00",
  "price": 250000,
  "availableSeats": 20,
  "status": "SCHEDULED"
}
```

---

## 4.5. Booking APIs

### 4.5.1. Create Booking

```http
POST /api/bookings
```

**Authentication:** Required

Tạo booking cho chuyến xe.

Request:

```json
{
  "tripId": 5,
  "passengerName": "Nguyen Van A",
  "passengerPhone": "0901234567",
  "quantity": 2
}
```

Response `201 Created`:

```json
{
  "id": 10,
  "bookingCode": "BK20261009001",
  "tripId": 5,
  "passengerName": "Nguyen Van A",
  "passengerPhone": "0901234567",
  "quantity": 2,
  "unitPrice": 250000,
  "totalAmount": 500000,
  "status": "CONFIRMED",
  "bookedAt": "2026-10-09T10:30:00"
}
```

### 4.5.2. Get Booking Detail

```http
GET /api/bookings/{bookingId}
```

**Authentication:** Required

Người dùng chỉ được xem booking của chính mình. Admin có thể xem booking của mọi người dùng.

### 4.5.3. Cancel Booking

```http
DELETE /api/bookings/{bookingId}
```

**Authentication:** Required

Hủy booking.

Khi booking được hủy, số ghế khả dụng của chuyến xe được cập nhật lại theo business rule.

Response:

```json
{
  "id": 10,
  "bookingCode": "BK20261009001",
  "status": "CANCELLED"
}
```

---

## 4.6. Admin APIs

Các API trong phần này yêu cầu:

```text
Role: ADMIN
```

### 4.6.1. User Management

Lấy danh sách người dùng:

```http
GET /api/admin/users
```

Cập nhật trạng thái người dùng:

```http
PUT /api/admin/users/{userId}/status
```

Request:

```json
{
  "status": "INACTIVE"
}
```

### 4.6.2. Bus Operator Management

Lấy danh sách nhà xe:

```http
GET /api/admin/operators
```

Tạo nhà xe:

```http
POST /api/admin/operators
```

Cập nhật nhà xe:

```http
PUT /api/admin/operators/{operatorId}
```

Xóa nhà xe:

```http
DELETE /api/admin/operators/{operatorId}
```

### 4.6.3. Vehicle Management

Lấy danh sách xe:

```http
GET /api/admin/vehicles
```

Tạo xe:

```http
POST /api/admin/vehicles
```

Cập nhật xe:

```http
PUT /api/admin/vehicles/{vehicleId}
```

Xóa xe:

```http
DELETE /api/admin/vehicles/{vehicleId}
```

### 4.6.4. Location Management

Lấy danh sách địa điểm:

```http
GET /api/admin/locations
```

Tạo địa điểm:

```http
POST /api/admin/locations
```

Cập nhật địa điểm:

```http
PUT /api/admin/locations/{locationId}
```

Xóa địa điểm:

```http
DELETE /api/admin/locations/{locationId}
```

### 4.6.5. Route Management

Lấy danh sách tuyến:

```http
GET /api/admin/routes
```

Tạo tuyến:

```http
POST /api/admin/routes
```

Cập nhật tuyến:

```http
PUT /api/admin/routes/{routeId}
```

Xóa tuyến:

```http
DELETE /api/admin/routes/{routeId}
```

### 4.6.6. Trip Management

Lấy danh sách chuyến:

```http
GET /api/admin/trips
```

Tạo chuyến:

```http
POST /api/admin/trips
```

Cập nhật chuyến:

```http
PUT /api/admin/trips/{tripId}
```

Xóa hoặc hủy chuyến:

```http
DELETE /api/admin/trips/{tripId}
```

### 4.6.7. Booking Management

Lấy danh sách booking:

```http
GET /api/admin/bookings
```

Xem chi tiết booking:

```http
GET /api/admin/bookings/{bookingId}
```

Cập nhật trạng thái booking:

```http
PUT /api/admin/bookings/{bookingId}/status
```

Request:

```json
{
  "status": "CANCELLED"
}
```

---

## 4.7. Error Response

API sử dụng một format thống nhất cho các lỗi.

### 4.7.1. Error Format

```json
{
  "timestamp": "2026-10-09T10:30:00",
  "status": 400,
  "error": "Bad Request",
  "message": "Quantity must be greater than 0",
  "path": "/api/bookings"
}
```

### 4.7.2. Validation Error

Khi request chứa dữ liệu không hợp lệ:

```json
{
  "timestamp": "2026-10-09T10:30:00",
  "status": 400,
  "error": "Validation Error",
  "message": "Request validation failed",
  "path": "/api/bookings",
  "details": {
    "quantity": "must be greater than 0"
  }
}
```

### 4.7.3. Authentication Error

Khi chưa đăng nhập hoặc JWT không hợp lệ:

```json
{
  "timestamp": "2026-10-09T10:30:00",
  "status": 401,
  "error": "Unauthorized",
  "message": "Authentication is required",
  "path": "/api/bookings"
}
```

### 4.7.4. Authorization Error

Khi người dùng đã đăng nhập nhưng không có quyền:

```json
{
  "timestamp": "2026-10-09T10:30:00",
  "status": 403,
  "error": "Forbidden",
  "message": "You do not have permission to access this resource",
  "path": "/api/admin/users"
}
```

### 4.7.5. Resource Not Found

```json
{
  "timestamp": "2026-10-09T10:30:00",
  "status": 404,
  "error": "Not Found",
  "message": "Trip not found",
  "path": "/api/trips/999"
}
```

### 4.7.6. Business Conflict

Ví dụ khi số ghế không đủ:

```json
{
  "timestamp": "2026-10-09T10:30:00",
  "status": 409,
  "error": "Conflict",
  "message": "Not enough available seats",
  "path": "/api/bookings"
}
```

---

## 4.8. Authentication & Authorization

### 4.8.1. Authentication Flow

```text
Client
  │
  │ POST /api/auth/login
  ▼
AuthController
  │
  ▼
AuthService
  │
  │ Verify email/password
  ▼
UserRepository
  │
  ▼
PostgreSQL
  │
  │ User information
  ▼
AuthService
  │
  │ Generate JWT
  ▼
Client
```

Sau khi đăng nhập thành công, client nhận JWT và gửi token trong các request cần xác thực:

```http
Authorization: Bearer <JWT_TOKEN>
```

### 4.8.2. Request Authentication Flow

```text
Client
  │
  │ HTTP Request + JWT
  ▼
Security Filter
  │
  ├── Invalid/Missing Token ──► 401 Unauthorized
  │
  ▼
Authentication Context
  │
  ├── Insufficient Role ───────► 403 Forbidden
  │
  ▼
Controller
  │
  ▼
Business Service
  │
  ▼
Response
```

### 4.8.3. Role-Based Authorization

Hệ thống có hai role chính:

| Role  | Quyền                                                                                 |
| ----- | ------------------------------------------------------------------------------------- |
| USER  | Tìm chuyến xe, xem chuyến xe, tạo booking, xem booking của mình, hủy booking của mình |
| ADMIN | Quản lý user, nhà xe, xe, địa điểm, tuyến, chuyến xe và booking                       |

Các endpoint yêu cầu ADMIN được bảo vệ ở tầng Security.

Ví dụ:

```text
GET /api/admin/users
POST /api/admin/operators
POST /api/admin/vehicles
POST /api/admin/routes
POST /api/admin/trips
```

đều yêu cầu:

```text
ROLE_ADMIN
```

### 4.8.4. Resource Ownership

Role-based authorization không thay thế kiểm tra quyền sở hữu tài nguyên.

Ví dụ:

```text
USER A
  │
  ├── GET /api/bookings/10
  │
  └── Booking 10 thuộc USER A
       └── Allow

USER B
  │
  ├── GET /api/bookings/10
  │
  └── Booking 10 thuộc USER A
       └── Deny
```

Việc kiểm tra ownership được thực hiện trong **Business Layer**, vì đây là business rule của hệ thống.

### 4.8.5. Public and Protected APIs

| API                          | Authentication | Role       |
| ---------------------------- | -------------- | ---------- |
| `POST /api/auth/register`    | Không          | Public     |
| `POST /api/auth/login`       | Không          | Public     |
| `GET /api/trips`             | Không          | Public     |
| `GET /api/trips/{id}`        | Không          | Public     |
| `GET /api/auth/me`           | Có             | USER/ADMIN |
| `GET /api/users/me`          | Có             | USER/ADMIN |
| `GET /api/users/me/bookings` | Có             | USER       |
| `POST /api/bookings`         | Có             | USER       |
| `GET /api/bookings/{id}`     | Có             | USER/ADMIN |
| `DELETE /api/bookings/{id}`  | Có             | USER/ADMIN |
| `/api/admin/**`              | Có             | ADMIN      |

### 4.8.6. Security Principle

Authentication và authorization được xử lý tập trung tại
