# OpenSource-BTVN-02
# SINH VIÊN THỰC HIỆN: LƯƠNG HOÀNG VIỆT - K225480106073
# MÔN HỌC: PHÁT TRIỂN ỨNG DỤNG VỚI MÃ NGUỒN MỞ
# BÀI TẬP 02
# BÀI LÀM
## 1. Thiết kế Cơ sở dữ liệu
## 2. Dựng Hạ tầng Docker
Sử dụng SSH để kết nối sang IDE (VS code) giúp thao tác edit dễ dàng hơn
### a) Tạo thư mục dự án:"camdo" trong Ubuntu server

<img width="372" height="26" alt="image" src="https://github.com/user-attachments/assets/a0a13aa7-4021-4804-8898-3cf9fbcc41d5" /> 

### b) Tại thư mục dự án tạo các thư mục:
1. File requirements.txt
<img width="342" height="140" alt="image" src="https://github.com/user-attachments/assets/ca4be9b4-62de-4df6-ac67-da6edeeae2f3" />
2.  File Dockerfile<br>
<img width="837" height="413" alt="image" src="https://github.com/user-attachments/assets/ad91edf5-53b6-4490-889c-d29fe59f0979" />
3. File docker-compose.yml
<img width="1339" height="987" alt="image" src="https://github.com/user-attachments/assets/3a724a0e-f90a-4d9b-943c-570675669b01" />
4. Khởi động bằng lệnh:
docker compose build
docker compose up -d
Kiểm tra bằng docker ps
<img width="1007" height="158" alt="image" src="https://github.com/user-attachments/assets/5ba9881d-488e-46bd-9c68-513cba3998c1" /> 

## 3. Khởi tạo Code Django & Cấu hình 

### a) Tạo Project và App:

Chạy các command dưới đây: 
<img width="1049" height="89" alt="image" src="https://github.com/user-attachments/assets/cc70e76b-9c59-443b-9ffd-82159c58337b" />
Sau khi chạy sẽ sinh thêm các Components mới: 
<img width="371" height="217" alt="image" src="https://github.com/user-attachments/assets/25e7696f-f870-461f-8d57-77690ee04d95" />

### b) Sửa file config/settings.py 

Cấp phép truy cập: ALLOWED_HOSTS = ['*']
Khai báo app: Thêm 'pawn_app', vào list INSTALLED_APPS
Kết nối DB: Sửa khối DATABASES thành:
<img width="475" height="249" alt="image" src="https://github.com/user-attachments/assets/37ac64a9-988d-410b-9296-8a2087dd3125" />
## 4. Tạo Bảng và Giao diện Admin 

### a) Viết Models (pawn_app/models.py): 

<img width="1325" height="687" alt="image" src="https://github.com/user-attachments/assets/5c56b28c-502d-4584-b35b-970496a58e66" />

### b) Cấu hình Admin (pawn_app/admin.py): 

<img width="1035" height="253" alt="image" src="https://github.com/user-attachments/assets/d1c6f85a-2b81-46e3-9d59-cfeea33b0cc2" />

### c) Áp dụng CSDL và Tạo Superuser: 

Sử dụng các command dưới đây lần lượt:
docker compose exec web python manage.py makemigrations
docker compose exec web python manage.py migrate
docker compose exec web python manage.py createsuperuser
Sau đó tạo user: luongviet, pw: viet2004
Truy cập: http://192.168.1.17:8001/admin/ để đăng nhập vào Django

## 5. Tạo trang "Con Nợ Đến Hạn"

### a) Viết View (pawn_app/views.py):

<img width="1025" height="611" alt="image" src="https://github.com/user-attachments/assets/17f47f08-b253-416e-bbc6-47e2b3a52179" />


### b) Cấu hình URLs (config/urls.py):

<img width="560" height="241" alt="image" src="https://github.com/user-attachments/assets/a22363e5-989d-41b5-82d6-f4f1250e8bf2" />

Truy cập vào Django để test: 
<img width="1920" height="1140" alt="image" src="https://github.com/user-attachments/assets/6dd3f693-8c98-4520-a321-e278a1593bd2" />


### c) Tạo Template HTML (pawn_app/templates/home.html):

<img width="368" height="104" alt="image" src="https://github.com/user-attachments/assets/922222a9-d116-4dc3-894f-2ae316c279a6" />

Thêm dữ liệu và truy cập http://192.168.1.17:8001/ để kiểm tra template:

<img width="1920" height="1140" alt="image" src="https://github.com/user-attachments/assets/106c2c3b-839f-4821-894b-eb8a3659faef" />


## 6. Triển khai CloudFlare

### a) Thêm mạng nội bộ cho các Container (dockerc-compose.yml)

Add các dòng này vào mỗi Container
networks:
      - default
      - web-gateway

Thêm phần này vào cuối file
networks:
  default:
  web-gateway:
    external: true

### Note: Trong bài này cấu hình sử dụng Nginx và Cloudflare thành master nên chỉ cần mở 1 port cho nhiều project => Thêm cấu hình là xong thay vì phải tạo ở các Container của từng project => Tiết kiệm RAM, port.
### b) Cấu hình Nginx.conf

<img width="934" height="824" alt="image" src="https://github.com/user-attachments/assets/64692b26-1879-4be4-ae2b-a2687053608a" />

### c) Cấu hình Docker-compose.yml tổng:

<img width="1376" height="631" alt="image" src="https://github.com/user-attachments/assets/0cc88f5b-a251-4bf6-b07c-14f14b238b65" />

Sau đó truy cập nơi chứa nginx tổng chạy lệnh: docker compose restart nginx-master
Tại project camdo: 
docker compose down
docker compose up -d

### d) Thêm subdomain để truy cập

Xem danh sách con nợ: camdo.luonghoangviet.io.vn
View bằng PHP: pma.luonghoangviet.io.vn
Truy cập kiểm tra: 
camdo.luonghoangviet.io.vn

<img width="1920" height="1140" alt="image" src="https://github.com/user-attachments/assets/6ddad18f-1d2c-4bb6-9058-429613c72091" />

pma.luonghoangviet.io.vn

<img width="1920" height="1140" alt="image" src="https://github.com/user-attachments/assets/6517d143-695c-4380-80c7-3a738b9a331e" />



    


