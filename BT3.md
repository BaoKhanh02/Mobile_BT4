# BÀI TẬP 03 - WORDPRESS DOCKER

Môn: Phát triển ứng dụng với mã nguồn mở - TEE0421  
Lớp: 58KTPM  
Mã số sinh viên: K22548106028
Sinh viên thực hiện: Vũ Bảo Khánh  

---

# 1. Giới thiệu

Bài tập yêu cầu sử dụng Docker trên Ubuntu để triển khai hệ thống WordPress gồm:

- MariaDB
- phpMyAdmin
- WordPress

Sau đó sử dụng Cloudflare Tunnel để public website lên internet thông qua sub-domain.

---

# 2. Công nghệ sử dụng

- Ubuntu
- Docker
- Docker Compose
- WordPress
- MariaDB
- phpMyAdmin
- Cloudflare Tunnel
- VMware Workstation

---

# 3. Cấu hình máy ảo

| Thành phần | Cấu hình |
|---|---|
| RAM | 4GB |
| CPU | 2 Core |
| Disk | 25GB |
| Network | NAT |

---

# 4. Cài đặt Docker

## Cập nhật hệ thống

```bash
sudo apt update
sudo apt upgrade -y
```

## Cài Docker

```bash
sudo apt install docker.io -y
```

## Khởi động Docker

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

## Kiểm tra Docker

```bash
docker --version
```

---

# 5. Cài Docker Compose

## Kiểm tra compose

```bash
docker compose version
```

## Nếu chưa có

```bash
sudo apt install docker-compose -y
```

---

# 6. Tạo project WordPress

## Tạo thư mục

```bash
mkdir wordpress-lab
cd wordpress-lab
```

---

# 7. File docker-compose.yml

```yaml
services:

  mariadb:
    image: mariadb:latest
    container_name: mariadb
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 123456
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: 123456
    ports:
      - "3306:3306"

  phpmyadmin:
    image: phpmyadmin:latest
    container_name: phpmyadmin
    restart: always
    ports:
      - "8080:80"
    environment:
      PMA_HOST: mariadb

  wordpress:
    image: wordpress:latest
    container_name: wordpress
    restart: always
    ports:
      - "8000:80"
    environment:
      WORDPRESS_DB_HOST: mariadb:3306
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: 123456
      WORDPRESS_DB_NAME: wordpress
```

---

# 8. Chạy hệ thống

```bash
sudo docker compose up -d
```

## Kiểm tra container

```bash
sudo docker ps
```

---

# 9. Truy cập hệ thống

## WordPress

```text
http://localhost:8000
```

Hoặc:

```text
http://IP_MAY_AO:8000
```

---

## phpMyAdmin

```text
http://localhost:8080
```

Thông tin đăng nhập:

| User | Password |
|---|---|
| root | 123456 |

---

# 10. Cài đặt WordPress

Sau khi mở website WordPress:

- Chọn ngôn ngữ
- Nhập:
  - Site Title
  - Username
  - Password
  - Email

Sau đó nhấn:

```text
Install WordPress
```

---

# 11. Public website bằng Cloudflare Tunnel

## Cài cloudflared

```bash
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
```

```bash
sudo dpkg -i cloudflared-linux-amd64.deb
```

---

## Đăng nhập Cloudflare

```bash
cloudflared tunnel login
```

---

## Tạo tunnel

```bash
cloudflared tunnel create wordpress
```

---

## Tạo file cấu hình

```bash
mkdir -p ~/.cloudflared
nano ~/.cloudflared/config.yml
```

Nội dung:

```yaml
tunnel: TUNNEL_ID
credentials-file: /home/khanh/.cloudflared/TUNNEL_ID.json

ingress:
  - hostname: wordpress.baokhanh2004210.com
    service: http://localhost:8000
  - service: http_status:404
```

---

## Tạo DNS route

```bash
cloudflared tunnel route dns wordpress wordpress.baokhanh2004210.com
```

---

## Chạy tunnel

```bash
cloudflared tunnel run wordpress
```

---

# 12. Nội dung website

## Bài viết 1

Giới thiệu bản thân:

- Họ tên
- Sở thích
- Thông tin cá nhân
- Hình ảnh
- Video

---

## Bài viết 2

Giới thiệu ngành học yêu thích tại TNUT:

- Nội dung ngành học
- Hình ảnh
- Video
- Định hướng tương lai

---

# 13. Nhận xét về WordPress

## Ưu điểm

- Dễ sử dụng
- Giao diện đẹp
- Nhiều plugin hỗ trợ
- Không cần lập trình quá nhiều

## Nhược điểm

- Tốn RAM nếu cài nhiều plugin
- Cần cấu hình bảo mật tốt
- Website có thể chậm khi dùng hosting yếu

---

# 14. Nhận xét về Docker

## Ưu điểm

- Triển khai nhanh
- Dễ backup
- Dễ di chuyển hệ thống
- Quản lý service thuận tiện

## Nhược điểm

- Cần hiểu command line
- Tốn tài nguyên nếu chạy nhiều container

---

# 15. Một số lệnh Docker thường dùng

## Xem container

```bash
sudo docker ps
```

## Dừng hệ thống

```bash
sudo docker compose down
```

## Khởi động lại

```bash
sudo docker compose up -d
```

## Xem log

```bash
sudo docker logs wordpress
```

---

# 16. Kết quả đạt được

- Cài đặt thành công WordPress bằng Docker
- Kết nối MariaDB thành công
- Sử dụng phpMyAdmin quản lý cơ sở dữ liệu
- Public website bằng Cloudflare Tunnel
- Tạo được website cá nhân bằng WordPress

---

# 17. Hình ảnh minh họa

## Docker containers

<img width="1920" height="1029" alt="image" src="https://github.com/user-attachments/assets/d39ef699-3b43-453d-a7b4-8d10091ac996" />

---

## WordPress website

<img width="1920" height="1029" alt="image" src="https://github.com/user-attachments/assets/f62647cc-c58b-48e1-9c62-ec8ea5811f42" />

---

## phpMyAdmin

<img width="1920" height="1029" alt="image" src="https://github.com/user-attachments/assets/82c38f01-0270-4ea8-92ee-2f77ec896b35" />

<img width="1920" height="1029" alt="image" src="https://github.com/user-attachments/assets/2c7b879e-9b5b-45af-9309-f39cfc5f5bea" />

<img width="1920" height="1029" alt="image" src="https://github.com/user-attachments/assets/e3af8a7c-c264-431f-aacf-07a9e2702c7b" />

---

## Cloudflare Tunnel

<img width="1920" height="1029" alt="image" src="https://github.com/user-attachments/assets/95090bb0-f5f7-46d7-a51e-e0fa7d0b3fa9" />

---

# 19. Kết luận

Qua bài tập này em đã học được:

- Cách sử dụng Docker
- Cách triển khai WordPress
- Cách sử dụng MariaDB
- Cách public website bằng Cloudflare Tunnel
- Kỹ năng quản trị hệ thống Linux cơ bản
