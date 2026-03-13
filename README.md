# Báo cáo sửa lỗi Bài tập 07 - Nhóm 5 - NT208.Q24.ANTT

## Thông tin nhóm

| STT | Họ và tên             | MSSV     |
| --- | --------------------- | -------- |
| 1   | Nguyễn Cao Xuân Trung | 24521885 |
| 2   | Nguyễn Ngọc Thu An    | 24520063 |
| 3   | Trần Nhật Duy         | 24520403 |
| 4   | Tô Công Hữu Nhân      | 24521238 |

## 1. Thông tin bài tập

**Bài tập 07**
**Cách chạy:**

```bash
php -S localhost:8000
```

Mở trình duyệt tại:

```text
http://localhost:8000
```

Các route cần kiểm tra:

- `/` hoặc `/?page=dashboard`
- `/?page=orders`
- `/?page=checkout`
- `/?page=customers`
- `/?page=reports`
- `/?page=settings`

## 2. Tổng quan lỗi đã tìm được

Tổng số lỗi đã tìm: **10 lỗi**

### Phân loại

**Lỗi syntax:**

- `pages/customers.php`
- `pages/reports.php`
- `pages/settings.php`

**Lỗi logic:**

- `includes/data.php` (2 lỗi)
- `pages/dashboard.php` (2 lỗi)
- `pages/orders.php`
- `pages/checkout.php`
- `pages/reports.php` (1 lỗi)

## 3. Chi tiết từng lỗi

### Lỗi 1 — `includes/data.php`

**Loại lỗi:** Logic  
**Mô tả:** Hàm tính tổng đơn hàng không nhân với số lượng sản phẩm.

#### Trước sửa

```php
$subtotal += $products[$item['sku']]['price'];
```

#### Đã sửa

```php
$subtotal += $products[$item['sku']]['price'] * $item['qty'];
```

#### Giải thích

Mỗi sản phẩm có số lượng `qty`, nên tổng tiền phải bằng **đơn giá × số lượng**. Code cũ chỉ cộng giá của 1 sản phẩm nên kết quả bị sai.

### Lỗi 2 — `includes/data.php`

**Loại lỗi:** Logic  
**Mô tả:** Hàm tính giá trị tồn kho dùng sai công thức.

#### Trước sửa

```php
$value += $product['price'] + $product['stock'];
```

#### Đã sửa

```php
$value += $product['price'] * $product['stock'];
```

#### Giải thích

Giá trị tồn kho phải là **giá × số lượng tồn**. Dùng phép cộng sẽ cho kết quả sai.

### Lỗi 3 — `pages/dashboard.php`

**Loại lỗi:** Logic  
**Mô tả:** Đếm đơn hoàn tất bằng trạng thái `pending`.

#### Trước sửa

```php
if ($order['status'] === 'pending') {
    $completedOrders++;
    $totalRevenue += calculate_order_total($order, $products);
}
```

#### Đã sửa

```php
if ($order['status'] === 'completed') {
    $completedOrders++;
    $totalRevenue += calculate_order_total($order, $products);
}
```

#### Giải thích

`pending` là đơn đang chờ xử lý, còn `completed` mới là đơn đã hoàn thành. Vì vậy phải dùng `completed` để tính doanh thu và số đơn đã xử lý.

### Lỗi 4 — `pages/dashboard.php`

**Loại lỗi:** Logic  
**Mô tả:** Điều kiện kiểm tra hàng sắp hết chưa hợp lý.

#### Trước sửa

```php
if ($product['stock'] < 1) {
```

#### Đã sửa

```php
if ($product['stock'] <= 1) {
```

#### Giải thích

Sản phẩm còn đúng 1 món vẫn nên được xem là sắp hết hàng. Điều kiện cũ bỏ sót trường hợp này.

### Lỗi 5 — `pages/orders.php`

**Loại lỗi:** Logic  
**Mô tả:** Biến `$pendingOnly` nhưng lại lọc đơn `completed`.

#### Trước sửa

```php
if ($order['status'] === 'completed') {
    $pendingOnly[] = $order;
}
```

#### Đã sửa

```php
if ($order['status'] === 'pending') {
    $pendingOnly[] = $order;
}
```

#### Giải thích

Trang orders yêu cầu hiển thị các đơn đang chờ xử lý. Vì vậy phải lọc trạng thái `pending`.

### Lỗi 6 — `pages/checkout.php`

**Loại lỗi:** Logic  
**Mô tả:** Biến `$subtotal` bị ghi đè trong mỗi vòng lặp.

#### Trước sửa

```php
$subtotal = $products[$item['sku']]['price'] * $item['qty'];
```

#### Đã sửa

```php
$subtotal += $products[$item['sku']]['price'] * $item['qty'];
```

#### Giải thích

Tổng tiền giỏ hàng phải là tổng của tất cả sản phẩm. Dùng phép gán `=` sẽ chỉ giữ lại giá trị của sản phẩm cuối cùng.

---

### Lỗi 7 — `pages/customers.php`

**Loại lỗi:** Syntax  
**Mô tả:** Sai dấu ngoặc khi truy cập phần tử mảng trong câu lệnh `if`.

#### Trước sửa

```php
if ($customer['active') {
```

#### Đã sửa

```php
if ($customer['active']) {
```

#### Giải thích

Khi truy cập phần tử mảng, phải đóng `]` trước rồi mới đóng `)`. Lỗi này làm file bị parse error.

### Lỗi 8 — `pages/reports.php`

**Loại lỗi:** Syntax  
**Mô tả:** Thiếu dấu chấm phẩy sau câu lệnh gán.

#### Trước sửa

```php
$reportRows = []
```

#### Đã sửa

```php
$reportRows = [];
```

#### Giải thích

Trong PHP, mỗi câu lệnh phải kết thúc bằng dấu `;`. Thiếu dấu này sẽ gây lỗi syntax.

### Lỗi 9 — `pages/reports.php`

**Loại lỗi:** Logic  
**Mô tả:** Nhóm dữ liệu theo tên sản phẩm thay vì theo danh mục.

#### Trước sửa

```php
$category = $product['name'];
```

#### Đã sửa

```php
$category = $product['category'];
```

#### Giải thích

Báo cáo cần tổng hợp theo **danh mục sản phẩm** (`category`), không phải theo tên từng sản phẩm (`name`).

### Lỗi 10 — `pages/settings.php`

**Loại lỗi:** Syntax  
**Mô tả:** Khai báo mảng sai dấu phân tách phần tử.

#### Trước sửa

```php
$config = [
    'currency' => 'USD',
    'timezone' => 'Asia/Ho_Chi_Minh',
    'language' => 'en';
];
```

#### Đã sửa

```php
$config = [
    'currency' => 'USD',
    'timezone' => 'Asia/Ho_Chi_Minh',
    'language' => 'en',
];
```

#### Giải thích

Trong mảng PHP, các phần tử phải được ngăn cách bằng dấu phẩy `,`. Dùng dấu `;` sẽ gây parse error.

---

## 4. Kết quả sau khi sửa

Sau khi sửa toàn bộ các lỗi trên:

- Tất cả route đều mở được bình thường.
- Trang dashboard hiển thị số liệu hợp lý.
- Trang orders hiển thị đúng danh sách đơn đang chờ.
- Trang checkout tính tiền đúng logic.
- Trang customers không còn lỗi cú pháp.
- Trang reports và settings không còn lỗi syntax.

---

## 5. Kết luận

Qua quá trình kiểm tra source code, đã tìm được **10 lỗi** gồm cả **lỗi cú pháp** và **lỗi logic**. Sau khi sửa, project đã hoạt động ổn định hơn, các trang quản trị hiển thị đúng dữ liệu và không còn bị lỗi parse error.
