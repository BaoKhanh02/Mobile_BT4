# BÀI TẬP 04 - KHAI THÁC N8N ĐỂ TỰ ĐỘNG ĐĂNG BÀI LÊN WORDPRESS

## Môn học

Phát triển ứng dụng với mã nguồn mở - TEE0421

## Sinh viên thực hiện

* Họ và tên: Vũ Bảo Khánh
* Lớp: 58KTPM

---

# 1. Mục tiêu bài tập

Bài tập này nhằm xây dựng hệ thống website WordPress chạy bằng Docker trên Ubuntu VMware và tích hợp nền tảng tự động hóa n8n để tự động đăng bài viết lên WordPress thông qua Telegram Bot và AI Google Gemini.

Hệ thống bao gồm:

* MariaDB
* phpMyAdmin
* WordPress
* Cloudflare Tunnel
* n8n

---

# 2. Công nghệ sử dụng

| Thành phần        | Công nghệ         |
| ----------------- | ----------------- |
| Ảo hóa            | VMware            |
| Hệ điều hành      | Ubuntu            |
| Container         | Docker            |
| Quản lý container | Docker Compose    |
| Website CMS       | WordPress         |
| Cơ sở dữ liệu     | MariaDB           |
| Quản trị CSDL     | phpMyAdmin        |
| Tunnel public     | Cloudflare Tunnel |
| Automation        | n8n               |
| AI                | Google Gemini     |
| Chatbot           | Telegram Bot      |

---

# 3. Kiến trúc hệ thống

```text
Telegram Bot
      ↓
Telegram Trigger (n8n)
      ↓
Google Gemini AI
      ↓
JavaScript Code Node
      ↓
WordPress Create Post
      ↓
Website WordPress
```

---

# 4. File docker-compose.yml

```yaml
services:

  mariadb:
    image: mariadb:latest
    container_name: mariadb
    restart: always
    environment:
      TZ: "Asia/Ho_Chi_Minh"
      MARIADB_ROOT_PASSWORD: 123456
      MARIADB_DATABASE: wordpress
      MARIADB_USER: wpuser
      MARIADB_PASSWORD: 123456
    ports:
      - "3306:3306"

  phpmyadmin:
    image: phpmyadmin:latest
    container_name: phpmyadmin
    restart: always
    environment:
      PMA_HOST: mariadb
      PMA_ARBITRARY: 1
    ports:
      - "8080:80"

  wordpress:
    image: wordpress:latest
    container_name: wordpress
    restart: always
    environment:
      WORDPRESS_DB_HOST: mariadb
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: 123456
    ports:
      - "8000:80"

  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    restart: always
    ports:
      - "5678:5678"
    environment:
      WEBHOOK_URL: https://n8n.baokhanh2004210.com/
      TZ: Asia/Ho_Chi_Minh

  cloudflared:
    image: cloudflare/cloudflared:latest
    container_name: cloudflared
    restart: always
    command: tunnel --no-autoupdate run --token YOUR_TOKEN
```

---

# 5. Triển khai hệ thống

## Bước 1: Tạo thư mục project

```bash
mkdir n8n-wordpress
cd n8n-wordpress
```

---

## Bước 2: Tạo file docker-compose.yml

```bash
nano docker-compose.yml
```

Dán nội dung docker compose vào file.

---

## Bước 3: Chạy docker compose

```bash
sudo docker compose up -d
```

---

## Bước 4: Kiểm tra container

```bash
sudo docker ps
```

Kết quả:

* mariadb running
* phpmyadmin running
* wordpress running
* n8n running
* cloudflared running

---

# 6. Public hệ thống bằng Cloudflare Tunnel

## Các subdomain sử dụng

| Dịch vụ    | Domain                                |
| ---------- | ------------------------------------- |
| WordPress  | https://wordpress.baokhanh2004210.com |
| phpMyAdmin | https://pma.baokhanh2004210.com       |
| n8n        | https://n8n.baokhanh2004210.com       |

---

# 7. Cài đặt WordPress

Sau khi truy cập website WordPress:

* tạo tài khoản admin
* cấu hình website
* đăng nhập wp-admin

Sau khi cài đặt thành công, WordPress tự động tạo các bảng dữ liệu trong MariaDB:

* wp_posts
* wp_users
* wp_options
* wp_comments
* ...

---

# 8. Cấu hình Telegram Bot

## Tạo bot bằng BotFather

Telegram:

* tìm @BotFather
* sử dụng lệnh:

```text
/newbot
```

Sau đó:

* đặt tên bot
* lấy Access Token

Ví dụ token:

```text
123456:AAxxxxxxxxxxxxxxxx
```

---

# 9. Cấu hình Google Gemini API

## Truy cập:

https://aistudio.google.com/api-keys

## Thực hiện:

* tạo project mới
* tạo API KEY
* copy API KEY để dùng trong n8n

---

# 10. Cấu hình workflow n8n

## Các node sử dụng

### 1. Telegram Trigger

* nhận nội dung từ Telegram Bot

### 2. Google Gemini AI

* sinh bài viết HTML + CSS
* trả kết quả JSON

### 3. JavaScript Code

* xử lý dữ liệu JSON

Code sử dụng:

```javascript
const rawText = $input.first().json.content.parts[0].text;

const cleanData = JSON.parse(rawText);

return {
  title: cleanData.post_title,
  content: cleanData.post_content
};
```

### 4. WordPress Create Post

<img width="1920" height="1029" alt="Screenshot 2026-05-25 003613" src="https://github.com/user-attachments/assets/5431dc6a-8847-4563-84ca-944faae17e51" />

---

# 11. Cấu hình WordPress Credential trong n8n

## Tạo Application Password

WordPress:

* Users
* Profile
* Application Passwords

Tạo password:

```text
n8n
```

Copy mật khẩu 24 ký tự để cấu hình trong n8n.

---

# 12. Kết quả đạt được

Hệ thống hoạt động thành công:

* Chat nội dung từ điện thoại qua Telegram Bot
* Telegram Trigger nhận dữ liệu
* Gemini AI tạo bài viết HTML + CSS
* JavaScript node xử lý JSON
* WordPress node tự động đăng bài
* Bài viết xuất hiện trực tiếp trên website WordPress

---

# 13. Nhận xét

## Ưu điểm

* Tự động hóa mạnh mẽ
* Dễ tích hợp AI
* Triển khai nhanh bằng Docker
* Có thể mở rộng nhiều workflow khác nhau
* WordPress dễ quản trị nội dung

## Nhược điểm

* Cấu hình ban đầu khá dài
* Phụ thuộc API bên ngoài
* Tốn RAM khi chạy nhiều container
* Cần Internet ổn định

---

# 14. Kết luận

Qua bài tập này em đã:

* hiểu cách triển khai hệ thống Docker Compose
* sử dụng Cloudflare Tunnel để public dịch vụ
* cấu hình WordPress với MariaDB
* sử dụng n8n để tự động hóa workflow
* tích hợp Telegram Bot và Google Gemini AI
* tự động đăng bài viết lên WordPress bằng AI

Bài tập giúp em hiểu rõ hơn về:

* DevOps cơ bản
* hệ thống mã nguồn mở
* AI Automation
* triển khai dịch vụ thực tế trên Linux.
