# Bài 3: Viết Dockerfile Đóng Gói Trang Web HTML Tĩnh

## 1. Mục tiêu
- Tự viết tệp `Dockerfile` ngắn gọn để đóng gói một trang HTML tĩnh thành Docker Image.
- Sử dụng base image Nginx phiên bản Alpine siêu nhẹ (`nginx:alpine`).
- Triển khai và khởi chạy container cung cấp dịch vụ web tĩnh trên cổng tùy chọn (`8081`).

---

## 2. Cấu trúc thư mục
```text
homework/session_09/ex3/
├── Dockerfile
├── index.html
└── README.md
```

---

## 3. Chi tiết mã nguồn

### 3.1. Tệp `index.html`
Nội dung trang web tĩnh:
```html
<h1>Hello Docker Session 09!</h1>
```

### 3.2. Tệp `Dockerfile`
Tệp Dockerfile gồm 2 chỉ thị đóng gói:
```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

**Giải thích các chỉ thị:**
- `FROM nginx:alpine`: Sử dụng base image Nginx chạy trên nền tảng Alpine Linux siêu nhẹ (~26MB - 40MB), tối ưu dung lượng và tốc độ tải.
- `COPY index.html /usr/share/nginx/html/index.html`: Sao chép file `index.html` từ máy host vào thư mục web root mặc định của Nginx (`/usr/share/nginx/html/`).

---

## 4. Hướng dẫn Build, Chạy và Kiểm tra

### Bước 1: Build Docker Image
Chạy lệnh tại thư mục chứa `Dockerfile`:
```bash
docker build -t my-html-app:v1 .
```

### Bước 2: Khởi chạy Container
Chạy container ở chế độ detached (`-d`) và ánh xạ cổng máy host `8081` vào cổng `80` của container:
```bash
docker run -d -p 8081:80 --name html-app my-html-app:v1
```

### Bước 3: Kiểm tra kết quả
Gửi yêu cầu HTTP đến cổng `8081`:
```bash
curl http://localhost:8081
```

*(Hoặc trên Windows PowerShell: `curl.exe http://localhost:8081`)*

**Kết quả trả về:**
```html
<h1>Hello Docker Session 09!</h1>
```

Hoặc truy cập bằng trình duyệt web tại địa chỉ: [http://localhost:8081](http://localhost:8081).

---

## 5. Kết quả thực thi thực tế

### 5.1. Kiểm tra Image đã build
```bash
docker images my-html-app:v1
```
Output:
```text
IMAGE            ID             DISK USAGE   CONTENT SIZE   EXTRA
my-html-app:v1   8f4e65a0fc44       93.6MB         26.3MB        
```

### 5.2. Kiểm tra trạng thái Container
```bash
docker ps --filter "name=html-app"
```
Output:
```text
CONTAINER ID   IMAGE            COMMAND                  CREATED         STATUS         PORTS                                     NAMES
a558cd0c7c5e   my-html-app:v1   "/docker-entrypoint.…"   Up              Up             0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   html-app
```

### 5.3. Kiểm tra Logs của Nginx Container
```bash
docker logs html-app
```
Output ghi nhận các request HTTP thành công (`HTTP 200`):
```text
172.17.0.1 - - [08/Oct/2026:16:14:55 +0000] "GET / HTTP/1.1" 200 34 "-" "curl/8.21.0" "-"
172.17.0.1 - - [08/Oct/2026:16:15:02 +0000] "GET / HTTP/1.1" 200 34 "-" "curl/8.21.0" "-"
```

---

## 6. Lệnh Dọn dẹp (Cleanup)
Khi muốn dừng và xóa container thử nghiệm:
```bash
docker stop html-app
docker rm html-app
```
Xóa image nếu không còn sử dụng:
```bash
docker rmi my-html-app:v1
```
