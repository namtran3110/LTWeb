# BÁO CÁO BÀI TẬP 1 - LẬP TRÌNH WEB

* **Họ và tên:** Trần Nhất Nam
* **Mã sinh viên:** 59KMT
* **Repository GitHub:** https://github.com/namtran3110/Myapp

---

## 1. Môi trường Giả lập & Containerization
* **Môi trường:** Máy ảo Ubuntu Linux (VMware / VirtualBox)
* **Công nghệ:** Docker & Docker Compose v2
* **Quản lý mã nguồn:** Git & SSH Key xác thực với GitHub

---

## 2. Các Dịch vụ Triển khai (Docker Compose Services)
Hệ thống bao gồm **5 container** hoạt động độc lập được khai báo trong `docker-compose.yml`:

1. **`nginx`** (Web Server & Reverse Proxy): Xử lý Virtual Hosts cho 2 trang web độc lập.
2. **`nodered`** (Backend Engine): Phục vụ xử lý logic và cung cấp API.
3. **`mariadb`** (Database): Hệ quản trị cơ sở dữ liệu quan hệ.
4. **`phpmyadmin`** (Database Manager): Giao diện quản trị CSDL MariaDB.
5. **`cloudflared`** (Cloudflare Tunnel): Kết nối an toàn các dịch vụ nội bộ ra Internet không cần mở port NAT.

---

## 3. Danh sách Tên miền & Đường dẫn Dịch vụ (Public Hostnames)
Tất cả tên miền đều sử dụng SSL/TLS HTTPS thông qua Cloudflare Tunnel:

* **Website 1 (Chi nhánh 1):** https://web1.trannhatnam59kmt.id.vn
* **Website 2 (Chi nhánh 2):** https://web2.trannhatnam59kmt.id.vn
* **Quản trị CSDL (phpMyAdmin):** https://pma.trannhatnam59kmt.id.vn

---

## 4. Cấu trúc Cây Thư mục Dự án

```text
myapp/
├── .gitignore            # Bỏ qua file môi trường và data rác
├── README.md             # Báo cáo chi tiết bài tập
├── docker-compose.yml    # File cấu hình 5 dịch vụ Docker
└── nginx/
    ├── conf.d/
    │   └── default.conf  # Cấu hình Nginx Virtual Hosts (Web1 & Web2)
    └── html/
        ├── web1/         # Giao diện Website 1
        │   └── index.html
        └── web2/         # Giao diện Website 2
            └── index.html
