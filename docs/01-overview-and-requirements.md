# 1. Tổng quan và yêu cầu hệ thống

## 1.1. Mục tiêu hệ thống

Hệ thống hướng tới các mục tiêu:

* Cung cấp chức năng đăng ký và đăng nhập cho người dùng.
* Cho phép người dùng tìm kiếm các chuyến xe theo điểm đi, điểm đến và thời gian khởi hành.
* Hiển thị thông tin cơ bản của chuyến xe như nhà xe, phương tiện, thời gian, giá vé và số ghế còn trống.
* Cho phép người dùng đặt vé cho chuyến xe.
* Quản lý thông tin các booking của người dùng.
* Cho phép quản trị viên quản lý các dữ liệu cơ bản như nhà xe, phương tiện và chuyến xe.
* Cung cấp REST API để các client có thể giao tiếp với hệ thống.
* Đảm bảo các chức năng yêu cầu người dùng đăng nhập được bảo vệ bằng cơ chế xác thực.

## 1.2. Phạm vi hệ thống

### Trong phạm vi

Hệ thống ở Pha 1 bao gồm:

* Quản lý tài khoản người dùng.
* Đăng ký và đăng nhập.
* Xác thực người dùng.
* Quản lý vai trò người dùng.
* Quản lý nhà xe.
* Quản lý phương tiện.
* Quản lý địa điểm.
* Quản lý tuyến đường.
* Quản lý chuyến xe.
* Tìm kiếm chuyến xe.
* Đặt vé.
* Xem lịch sử đặt vé.
* Hủy booking.
* Quản lý các dữ liệu nghiệp vụ bởi quản trị viên.

### Ngoài phạm vi

Do chỉ xét bài toán nghiệp vụ đơn giản nên các chức năng này có thể không được triển khai:

* Thanh toán trực tuyến qua cổng thanh toán thực tế.
* Có Actor nhân viên
* Ứng dụng mobile riêng.
* Hệ thống đánh giá và nhận xét nhà xe.
* Hệ thống khuyến mãi và mã giảm giá.
* Chat giữa người dùng và nhà xe.
* Thông báo thời gian thực.
* Hệ thống chọn vị trí ghế cụ thể.
* Các cơ chế tối ưu cho hệ thống có lượng truy cập rất lớn.


## 1.3. Actors

Hệ thống có hai actor chính:

### User

Người dùng có tài khoản trên hệ thống và sử dụng các chức năng liên quan đến việc tìm kiếm và đặt vé.

Các chức năng chính:

* Đăng ký tài khoản.
* Đăng nhập.
* Tìm kiếm chuyến xe.
* Xem thông tin chuyến xe.
* Đặt vé.
* Xem lịch sử booking.
* Xem thông tin booking.
* Hủy booking theo các điều kiện của hệ thống.

### Admin

Quản trị viên chịu trách nhiệm quản lý dữ liệu và hoạt động cơ bản của hệ thống.

Các chức năng chính:

* Đăng nhập.
* Quản lý người dùng.
* Quản lý nhà xe.
* Quản lý phương tiện.
* Quản lý địa điểm.
* Quản lý tuyến đường.
* Quản lý chuyến xe.
* Xem và quản lý các booking.

## 1.4. Yêu cầu chức năng

### 1.4.1. Authentication

#### FR-01: Đăng ký

Hệ thống cho phép người dùng tạo tài khoản bằng cách cung cấp các thông tin cần thiết như họ tên, email, số điện thoại và mật khẩu.

Email của mỗi tài khoản phải là duy nhất.

#### FR-02: Đăng nhập

Người dùng có thể đăng nhập bằng thông tin tài khoản đã đăng ký.

Sau khi đăng nhập thành công, hệ thống cấp thông tin xác thực để người dùng truy cập các chức năng yêu cầu đăng nhập.

#### FR-03: Xác thực

Các chức năng yêu cầu đăng nhập phải kiểm tra thông tin xác thực của người dùng trước khi xử lý yêu cầu.

#### FR-04: Phân quyền

Hệ thống phân biệt quyền truy cập giữa `USER` và `ADMIN`.

Người dùng thông thường không được phép thực hiện các chức năng chỉ dành cho quản trị viên.

---

### 1.4.2. User

#### FR-05: Xem thông tin tài khoản

Người dùng đã đăng nhập có thể xem thông tin tài khoản của mình.

#### FR-06: Tìm kiếm chuyến xe

Người dùng có thể tìm kiếm chuyến xe dựa trên các tiêu chí:

* Điểm đi.
* Điểm đến.
* Thời gian hoặc ngày khởi hành.

#### FR-07: Xem thông tin chuyến xe

Hệ thống hiển thị thông tin của chuyến xe, bao gồm:

* Nhà xe.
* Tuyến đường.
* Phương tiện.
* Thời gian khởi hành.
* Thời gian dự kiến đến.
* Giá vé.
* Số ghế còn trống.
* Trạng thái chuyến xe.

#### FR-08: Xem lịch sử đặt vé

Người dùng đã đăng nhập có thể xem danh sách các booking của mình.

#### FR-09: Xem chi tiết booking

Người dùng có thể xem thông tin chi tiết của một booking thuộc tài khoản của mình.

---

### 1.4.3. Trip

#### FR-10: Quản lý tuyến đường

Hệ thống cho phép quản lý các tuyến đường, bao gồm điểm đi và điểm đến.

#### FR-11: Quản lý chuyến xe

Hệ thống cho phép quản lý thông tin chuyến xe, bao gồm:

* Tuyến đường.
* Phương tiện.
* Thời gian khởi hành.
* Thời gian dự kiến đến.
* Giá vé.
* Số ghế.
* Trạng thái chuyến xe.

#### FR-12: Xem danh sách chuyến xe

Hệ thống cung cấp danh sách các chuyến xe phù hợp với điều kiện tìm kiếm.

#### FR-13: Kiểm tra số ghế

Hệ thống phải xác định số ghế còn trống của chuyến xe trước khi tạo booking.

---

### 1.4.4. Booking

#### FR-14: Đặt vé

Người dùng đã đăng nhập có thể tạo booking cho một chuyến xe.

Thông tin booking tối thiểu bao gồm:

* Chuyến xe.
* Thông tin hành khách.
* Số lượng vé.
* Giá vé.
* Tổng tiền.
* Thời gian đặt.
* Trạng thái booking.

#### FR-15: Kiểm tra khả năng đặt vé

Trước khi tạo booking, hệ thống phải kiểm tra số ghế còn trống.

Nếu số lượng vé yêu cầu lớn hơn số ghế còn trống, hệ thống từ chối yêu cầu đặt vé.

#### FR-16: Cập nhật số ghế

Sau khi booking được tạo thành công, số ghế còn trống của chuyến xe được cập nhật tương ứng.

#### FR-17: Xem booking

Người dùng có thể xem thông tin các booking thuộc tài khoản của mình.

#### FR-18: Hủy booking

Người dùng có thể hủy booking của mình nếu booking đang ở trạng thái cho phép hủy.

Sau khi booking được hủy, số ghế tương ứng được hoàn lại cho chuyến xe theo quy tắc nghiệp vụ của hệ thống.

---

### 1.4.5. Administration

#### FR-19: Quản lý người dùng

Admin có thể xem và quản lý thông tin người dùng.

#### FR-20: Quản lý nhà xe

Admin có thể thêm, xem, cập nhật và xóa thông tin nhà xe.

#### FR-21: Quản lý phương tiện

Admin có thể thêm, xem, cập nhật và xóa thông tin phương tiện.

#### FR-22: Quản lý địa điểm

Admin có thể quản lý các địa điểm được sử dụng làm điểm đi và điểm đến.

#### FR-23: Quản lý tuyến đường

Admin có thể tạo, cập nhật và xóa các tuyến đường.

#### FR-24: Quản lý chuyến xe

Admin có thể tạo, cập nhật, xem và xóa các chuyến xe.

#### FR-25: Quản lý booking

Admin có thể xem và quản lý các booking trong hệ thống.

## 1.5. Nghiệp vụ

### 1.5.1. Business Rules

#### BR-01: Email người dùng là duy nhất

Mỗi tài khoản người dùng phải có một địa chỉ email duy nhất trong hệ thống.

#### BR-02: Chỉ người dùng đã xác thực mới được đặt vé

Chức năng đặt vé yêu cầu người dùng phải đăng nhập.

#### BR-03: Chuyến xe phải có số ghế hợp lệ

Số ghế của phương tiện phải lớn hơn 0.

#### BR-04: Không được đặt vượt quá số ghế còn trống

Số lượng vé trong một booking không được lớn hơn số ghế còn trống của chuyến xe.

#### BR-05: Cập nhật số ghế sau khi đặt vé

Khi booking được tạo thành công, số ghế còn trống phải giảm tương ứng với số lượng vé đã đặt.

#### BR-06: Hoàn lại số ghế khi hủy booking

Khi booking được hủy hợp lệ, số ghế tương ứng được hoàn lại cho chuyến xe.

#### BR-07: Người dùng chỉ quản lý booking của mình

User chỉ được xem và hủy các booking thuộc tài khoản của mình.

Admin có quyền quản lý booking trong toàn hệ thống.

#### BR-08: Chuyến xe phải có tuyến đường và phương tiện

Một chuyến xe phải được gắn với một tuyến đường và một phương tiện hợp lệ.

#### BR-09: Điểm đi và điểm đến phải khác nhau

Một tuyến đường không được có điểm đi trùng với điểm đến.

#### BR-10: Không cho phép đặt vé cho chuyến xe không còn khả năng phục vụ

Booking không được tạo đối với chuyến xe đã kết thúc, bị hủy hoặc không còn hoạt động.

### 1.5.2. Business Flows

#### BF-01: Đăng ký và đăng nhập

```text
User
  ↓
Nhập thông tin đăng ký
  ↓
Hệ thống kiểm tra thông tin
  ↓
Tạo tài khoản
  ↓
Đăng nhập
  ↓
Xác thực thông tin
  ↓
Cấp thông tin xác thực
```

#### BF-02: Tìm kiếm chuyến xe

```text
User
  ↓
Nhập điểm đi
  ↓
Nhập điểm đến
  ↓
Chọn ngày khởi hành
  ↓
Hệ thống tìm kiếm chuyến phù hợp
  ↓
Hiển thị danh sách chuyến xe
```

#### BF-03: Đặt vé

```text
User
  ↓
Tìm kiếm chuyến xe
  ↓
Chọn chuyến
  ↓
Nhập thông tin hành khách
  ↓
Nhập số lượng vé
  ↓
Kiểm tra số ghế còn trống
  ↓
 ┌───────────────────────┐
 │ Đủ ghế?               │
 └───────────┬───────────┘
             │
        ┌────┴────┐
       Có         Không
        │           │
        ↓           ↓
 Tạo booking    Từ chối
        │
        ↓
Cập nhật số ghế
        │
        ↓
Trả kết quả booking
```

#### BF-04: Hủy booking

```text
User
  ↓
Xem booking
  ↓
Chọn booking cần hủy
  ↓
Kiểm tra quyền sở hữu
  ↓
Kiểm tra trạng thái booking
  ↓
Hủy booking
  ↓
Hoàn lại số ghế
  ↓
Cập nhật trạng thái booking
```

#### BF-05: Quản lý chuyến xe

```text
Admin
  ↓
Tạo / cập nhật chuyến xe
  ↓
Chọn tuyến đường
  ↓
Chọn phương tiện
  ↓
Thiết lập thời gian
  ↓
Thiết lập giá vé
  ↓
Thiết lập trạng thái
  ↓
Lưu chuyến xe
```
