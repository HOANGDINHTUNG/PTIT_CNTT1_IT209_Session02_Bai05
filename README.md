# Bài 5: Quản lý quyền sở hữu thư mục Web (Directory Permission Management)

## Mục tiêu
- Nắm vững cơ chế phân quyền tệp tin và thư mục (sử dụng `chown` và `chmod`) trên Linux.
- Tối ưu hóa phân quyền để tài khoản `devops` (user thường) có toàn quyền chỉnh sửa và tải lên mã nguồn mà không cần nhờ đến `sudo`.
- Đảm bảo ứng dụng web (Nginx chạy dưới user/group là `www-data`) vẫn có đủ quyền hạn để đọc file và render website (không bị lỗi HTTP 403 Forbidden).

## Hướng dẫn thực thi

### Bước 1: Thay đổi Chủ sở hữu và Nhóm sở hữu (Chown)
Để phân quyền lại toàn bộ thư mục gốc của website, ta sử dụng lệnh `chown` với cờ `-R` (đệ quy áp dụng cho mọi file và folder bên trong). 
Gán Chủ sở hữu (Owner) là `devops` và Nhóm (Group) là `www-data`:
```bash
sudo chown -R devops:www-data /var/www/ptit-web/
```

### Bước 2: Thiết lập quyền truy cập (Chmod)
Tiếp theo, ta cấu hình quyền truy cập (Read/Write/Execute) để bảo mật hệ thống.
Nguyên tắc tốt nhất:
- **Thư mục (Directories)** nên có quyền `750`: Chủ (Read/Write/Execute), Nhóm (Read/Execute), Khác (Không có quyền).
- **Tập tin (Files)** nên có quyền `640`: Chủ (Read/Write), Nhóm (Read), Khác (Không có quyền).

Chạy lệnh giới hạn quyền cho tất cả các thư mục con:
```bash
sudo find /var/www/ptit-web/ -type d -exec chmod 750 {} \;
```
Chạy lệnh giới hạn quyền cho tất cả các tập tin bên trong:
```bash
sudo find /var/www/ptit-web/ -type f -exec chmod 640 {} \;
```

### Bước 3: Đánh giá quá trình phân quyền
Kiểm tra cấu trúc phân quyền và owner mới của thư mục:
```bash
ls -la /var/www/ptit-web/
```

> **Kết quả phân quyền `ls -la`:**
> *Hãy dán đầu ra của lệnh trên vào ô bên dưới, đảm bảo phải xuất hiện `devops` và `www-data` cho các thư mục:*
> ```text
> [Dán log text đầu ra của lệnh ls -la vào đây]
> ```

---

## Kiểm tra khả năng làm việc thực tế
Đăng nhập vào Droplet bằng user `devops` qua SSH. Do bạn đã là Chủ sở hữu của thư mục web, bạn không cần dùng tới quyền quản trị `sudo` nữa.
Thử chỉnh sửa trực tiếp mã nguồn bằng cách chèn thêm một dòng chữ vào file `index.html`:
```bash
echo "<h2>Update by Devops Team</h2>" >> /var/www/ptit-web/html/index.html
```
- **Kết quả mong muốn**: Lệnh chạy trơn tru, không báo lỗi `Permission denied`.
- Tiếp theo, truy cập vào IP của Droplet qua trình duyệt `http://<IP_ADDRESS_DROPLET>`. Web vẫn phải chạy bình thường (không bị lỗi 403 Forbidden) và có xuất hiện dòng Update vừa tạo.

> **Log ghi file thành công:**
> *Chèn kết quả chạy lệnh echo thành công (hoặc ảnh chụp web mới) vào đây:*
> ```text
> [Dán text hoặc link ảnh vào đây]
> ```
