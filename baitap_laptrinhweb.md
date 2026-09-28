# MÔN HỌC: LẬP TRÌNH WEB
## Họ và tên: TRẦN NHẤT NAM
## MSSV: K235480106001
## Lớp: K59KMT

---

### BÀI 1 - LẬP TRÌNH WEB
1. Môi trường Giả lập & Containerization
* **Môi trường:** Máy ảo Ubuntu Linux (VMware / VirtualBox)
* **Công nghệ:** Docker & Docker Compose v2
* **Quản lý mã nguồn:** Git & SSH Key xác thực với GitHub
## 2. Các Dịch vụ Triển khai (Docker Compose Services)
Hệ thống bao gồm **5 container** hoạt động độc lập được khai báo trong `docker-compose.yml`:
1. **`nginx`** (Web Server & Reverse Proxy): Xử lý Virtual Hosts cho 2 trang web độc lập.
2. **`nodered`** (Backend Engine): Phục vụ xử lý logic và cung cấp API.
3. **`mariadb`** (Database): Hệ quản trị cơ sở dữ liệu quan hệ.
4. **`phpmyadmin`** (Database Manager): Giao diện quản trị CSDL MariaDB.
5. **`cloudflared`** (Cloudflare Tunnel): Kết nối an toàn các dịch vụ nội bộ ra Internet không cần mở port NAT.
## 3. Danh sách Tên miền & Đường dẫn Dịch vụ (Public Hostnames)
Tất cả tên miền đều sử dụng SSL/TLS HTTPS thông qua Cloudflare Tunnel:
* **Website 1 (Chi nhánh 1):** https://web1.trannhatnam59kmt.id.vn
* **Website 2 (Chi nhánh 2):** https://web2.trannhatnam59kmt.id.vn
* **Quản trị CSDL (phpMyAdmin):** https://pma.trannhatnam59kmt.id.vn
## 4. Cấu trúc Cây Thư mục Dự án

```text
LTWeb/
├── .gitignore            # Bỏ qua file môi trường và data rác
├── baitap_laptrinhweb.md # Báo cáo chi tiết bài tập
├── docker-compose.yml    # File cấu hình 5 dịch vụ Docker
└── nginx/
    ├── conf.d/
    │   └── default.conf  # Cấu hình Nginx Virtual Hosts (Web1 & Web2)
    └── html/
        ├── web1/         # Giao diện Website 1
        │   └── index.html
        └── web2/         # Giao diện Website 2
            └── index.html
```
---

### BÀI 2:
1. sử dụng nodered: dùng node http_in + http_response => tạo api đơn giản
<img width="956" height="541" alt="image" src="https://github.com/user-attachments/assets/e16b7430-27b7-42f5-b651-b130a0b971c4" />
Chương trình khối template:
```
{
  "status": "success",
  "message": "Lấy bảng điểm thành công",
  "mon_hoc": "Lập trình Web",
  "danh_sach": [
    {"stt": 1, "masv": "SV001", "ho_ten": "Trần Nhất Nam", "diem_qt": 8.5, "diem_thi": 9.0},
    {"stt": 2, "masv": "SV002", "ho_ten": "Khang Long", "diem_qt": 7.0, "diem_thi": 8.0},
    {"stt": 3, "masv": "SV003", "ho_ten": "Trần Bình", "diem_qt": 9.0, "diem_thi": 9.5}
  ]
}
```

<img width="957" height="539" alt="image" src="https://github.com/user-attachments/assets/9afca479-5e38-46f8-a55a-a578528353a4" />

2. cấu hình nginx để web dùng js gọi đc API trên nodered, thuật toán cho api
Cập nhật cấu hình để Nginx điều hướng các yêu cầu /api/ sang Node-RED:
```
server {
    listen 80;
    server_name web1.trannhatnam59kmt.id.vn;

    location / {
        root /usr/share/nginx/html/web1;
        index index.html index.htm;
    }

    # Cấu hình Proxy để gọi API Node-RED
    location /api/ {
        proxy_pass http://nodered:1880/api/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

server {
    listen 80;
    server_name web2.trannhatnam59kmt.id.vn;

    location / {
        root /usr/share/nginx/html/web2;
        index index.html index.htm;
    }
}
```
<img width="956" height="545" alt="image" src="https://github.com/user-attachments/assets/8af15796-4056-4c7a-9e63-072daf532de2" />

3.Code js vào trang html để gọi đc api
Trình duyệt truy cập:web1.trannhatnam59kmt.id.vn

```
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Quản Lý Bảng Điểm - Bài Tập 2</title>
    <style>
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; margin: 40px; background-color: #eef2f5; }
        .card { max-width: 700px; background: white; padding: 25px; border-radius: 10px; box-shadow: 0 4px 15px rgba(0,0,0,0.08); margin: 0 auto; }
        h2 { color: #1a365d; border-bottom: 2px solid #3182ce; padding-bottom: 10px; margin-top: 0; }
        button { background-color: #3182ce; color: white; border: none; padding: 11px 20px; border-radius: 6px; cursor: pointer; font-size: 15px; font-weight: bold; transition: 0.2s; }
        button:hover { background-color: #2b6cb0; }
        #msg { margin-top: 15px; font-weight: bold; color: #2d3748; }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        th, td { border: 1px solid #e2e8f0; padding: 12px; text-align: center; }
        th { background-color: #3182ce; color: white; }
        tr:nth-child(even) { background-color: #f7fafc; }
    </style>
</head>
<body>

<div class="card">
    <h2>Hệ Thống Tra Cứu Bảng Điểm</h2>
    <button onclick="taiBangDiem()">Tải Bảng Điểm Mới Nhất</button>
    
    <p id="msg"></p>

    <table id="bangDiemTable" style="display: none;">
        <thead>
            <tr>
                <th>STT</th>
                <th>Mã SV</th>
                <th>Họ và Tên</th>
                <th>Điểm Quá Trình</th>
                <th>Điểm Thi</th>
            </tr>
        </thead>
        <tbody id="theDiemBody"></tbody>
    </table>
</div>

<script>
async function taiBangDiem() {
    const msg = document.getElementById('msg');
    const table = document.getElementById('bangDiemTable');
    const tbody = document.getElementById('theDiemBody');

    msg.innerText = "Đang kết nối API...";
    msg.style.color = "#d69e2e";

    try {
        // Gọi API mới /api/bangdiem
        const res = await fetch('/api/bangdiem');
        const data = await res.json();

        if (data.status === "success") {
            msg.innerText = `${data.message} - Môn: ${data.mon_hoc}`;
            msg.style.color = "#38a169";
            tbody.innerHTML = "";

            data.danh_sach.forEach(sv => {
                const row = `<tr>
                    <td>${sv.stt}</td>
                    <td><b>${sv.masv}</b></td>
                    <td style="text-align: left;">${sv.ho_ten}</td>
                    <td>${sv.diem_qt}</td>
                    <td>${sv.diem_thi}</td>
                </tr>`;
                tbody.innerHTML += row;
            });

            table.style.display = "table";
        } else {
            msg.innerText = "Không thể lấy dữ liệu!";
            msg.style.color = "#e53e3e";
        }
    } catch (err) {
        console.error("Lỗi:", err);
        msg.innerText = "Lỗi kết nối tới Server API!";
        msg.style.color = "#e53e3e";
    }
}
</script>

</body>
</html>
```
Truy cập thành công trang web, giao diện như trong ảnh:
<img width="958" height="537" alt="image" src="https://github.com/user-attachments/assets/24ab439c-0906-4c0b-bcd3-56eedf3f2d96" />

Lấy API thành công, trả về kết quả như hình:
<img width="959" height="535" alt="image" src="https://github.com/user-attachments/assets/ea2e1ce4-61ff-44ce-9b1f-0322fff23d51" />

















