# Báo cáo thực hành - Bài 1: Quản lý người dùng giới hạn và Truyền tải dữ liệu qua SFTP trên Windows

## 1. Thông tin bài nộp
- **Đường dẫn trên GitHub:** `homework/session_07/ex1/README.md`
- **Bài tập:** Quản lý người dùng giới hạn và Truyền tải dữ liệu qua SFTP trên Windows
- **Mục tiêu:** Tạo tài khoản giới hạn `sftp-user`, cấu hình phân quyền thư mục log giả lập và tải file an toàn về máy tính Windows qua giao thức SFTP.

---

## 2. Các bước triển khai chi tiết

### Bước 1: Khởi tạo tài khoản người dùng giới hạn
Chúng ta tạo một tài khoản người dùng mới tên là `sftp-user` và đặt mật khẩu bảo mật mạnh cho tài khoản này. Tài khoản này mặc định không nằm trong nhóm `sudo` (không có quyền quản trị).

```bash
sudo adduser sftp-user
```
*(Nhập mật khẩu mạnh cho tài khoản và nhấn Enter qua các thông tin tùy chọn)*

### Bước 2: Tạo thư mục log giả lập và gán quyền truy cập
Tạo thư mục lưu trữ log `/var/log/app-backup/`, tạo file log giả lập, sau đó cấu hình quyền sở hữu và quyền truy cập sao cho `sftp-user` chỉ có quyền đọc mà không thể can thiệp sâu vào hệ thống:

```bash
# Tạo thư mục log
sudo mkdir -p /var/log/app-backup/

# Tạo file log giả lập với nội dung mẫu
sudo touch /var/log/app-backup/backup-check.log
sudo bash -c 'echo "Backup status: SUCCESS at $(date)" > /var/log/app-backup/backup-check.log'

# Thay đổi quyền sở hữu: Thư mục thuộc sở hữu của root và nhóm sftp-user
sudo chown -R root:sftp-user /var/log/app-backup

# Phân quyền thư mục (chỉ root và group sftp-user được truy cập)
sudo chmod 750 /var/log/app-backup

# Phân quyền tệp tin log (chỉ root được ghi, group sftp-user được đọc)
sudo chmod 640 /var/log/app-backup/backup-check.log
```

---

## 3. Kiểm tra cấu hình trên VPS

Chạy các lệnh kiểm tra sau để xác minh tài khoản và quyền hạn của tệp tin:

### 1. Kiểm tra thông tin người dùng `sftp-user`:
```bash
id sftp-user
```
*Kết quả mong đợi:*
```text
uid=1001(sftp-user) gid=1001(sftp-user) groups=1001(sftp-user)
```
*(Lưu ý: Tài khoản `sftp-user` không thuộc nhóm `sudo` hoặc `wheel`, đảm bảo quyền tối thiểu).*

### 2. Kiểm tra quyền hạn tệp tin log:
```bash
ls -lh /var/log/app-backup/backup-check.log
```
*Kết quả mong đợi:*
```text
-rw-r----- 1 root sftp-user 43 Oct 24 10:00 /var/log/app-backup/backup-check.log
```

---

## 4. Kết nối SFTP từ Windows và tải tệp tin

1. **Mở SFTP Client trên Windows** (ví dụ: *WinSCP* hoặc *Bitvise SSH Client*).
2. **Nhập các thông số kết nối:**
   - **Host / IP Address:** `IP_của_VPS_của_bạn`
   - **Port:** `22` (hoặc cổng SSH tùy chỉnh của bạn)
   - **Username:** `sftp-user`
   - **Password:** Mật khẩu đã thiết lập ở Bước 1.
3. **Thực hiện tải file:**
   - Điều hướng khung thư mục bên phải (Remote) đến đường dẫn: `/var/log/app-backup/`
   - Chọn tệp `backup-check.log`.
   - Kéo và thả (hoặc nhấn Download) tệp tin này sang khung thư mục bên trái (Local - Máy tính Windows).

---

## 5. Minh chứng kết quả thực hiện (Ảnh chụp màn hình)

*Học viên thực hiện chụp ảnh màn hình các bước và lưu vào cùng thư mục này, sau đó cập nhật lại đường dẫn ảnh dưới đây:*

### Ảnh 1: Kết nối SFTP thành công và tìm thấy file log trên Server
<!-- Thay thế link ảnh dưới đây bằng ảnh thực tế của bạn, ví dụ: ![SFTP Connection](./sftp_connected.png) -->
![SFTP Connection](https://images.viblo.asia/df4c82c6-3023-41c1-9657-3fb8078ef131.png) *(Hình ảnh minh họa kết nối SFTP)*

### Ảnh 2: Tải file thành công về máy tính cá nhân và kiểm tra nội dung
<!-- Thay thế link ảnh dưới đây bằng ảnh thực tế của bạn, ví dụ: ![File Downloaded](./file_downloaded.png) -->
![File Downloaded](https://images.viblo.asia/4bf23cfd-91b5-4a62-84da-56fa6c9a3ee4.png) *(Hình ảnh minh họa nội dung tệp log sau khi tải về trùng khớp nội dung gốc)*