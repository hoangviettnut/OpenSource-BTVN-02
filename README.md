# OpenSource-BTVN-02
# SINH VIÊN THỰC HIỆN: LƯƠNG HOÀNG VIỆT - K225480106073
# MÔN HỌC: PHÁT TRIỂN ỨNG DỤNG VỚI MÃ NGUỒN MỞ
# BÀI TẬP 02
# BÀI LÀM
## 1. Thiết kế Cơ sở dữ liệu ( vẽ tay)

<img width="1920" height="2560" alt="z7808644023397_9213dabaab6ae17d1e313be2d2c207e0" src="https://github.com/user-attachments/assets/46847ec7-55f6-4475-a379-a377610c300f" />

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

### Note: Do dùng Cloudflare Tunnel nên cái này không cần thiết vì đã được cấp HTTPS. Dùng trong trường hợp đăng nhập Django dính 403 (không có HTTPS mà dùng HTTP).

```
CSRF_TRUSTED_ORIGINS = [

    'http://192.168.1.17:8001', #Ip máy này thay đổi thoải mái.
    
    'http://localhost:8001',
    
    'https://vayno.luonghoangviet.io.vn', 
    
]
```

Khai báo app: Thêm 'pawn_app', vào list INSTALLED_APPS

Kết nối DB: Sửa khối DATABASES thành:

<img width="475" height="249" alt="image" src="https://github.com/user-attachments/assets/37ac64a9-988d-410b-9296-8a2087dd3125" />

## 4. Tạo Bảng và Giao diện Admin 

### a) Viết Models (pawn_app/models.py): 

<img width="1608" height="757" alt="image" src="https://github.com/user-attachments/assets/038f22e2-1fac-4578-bcff-d21f7de8fb94" />

### b) Cấu hình Admin (pawn_app/admin.py): 

<img width="1012" height="280" alt="image" src="https://github.com/user-attachments/assets/cc7a923f-0ce2-4415-8ab2-be395c4c1d14" />


### c) Áp dụng CSDL và Tạo Superuser: 

Sử dụng các command dưới đây lần lượt:

```
docker compose exec web python manage.py makemigrations

docker compose exec web python manage.py migrate

docker compose exec web python manage.py createsuperuser
```

Sau đó tạo user: luongviet, pw: viet2004

## 5. Tạo trang Danh Sách Con Nợ

### a) Viết View (pawn_app/views.py):

<img width="1070" height="595" alt="image" src="https://github.com/user-attachments/assets/5c9e8ac2-dae1-41be-8079-fdffbce315dc" />

### b) Cấu hình URLs (config/urls.py):

<img width="560" height="241" alt="image" src="https://github.com/user-attachments/assets/a22363e5-989d-41b5-82d6-f4f1250e8bf2" />

Truy cập: http://192.168.1.17:8001/admin/ để đăng nhập vào Django

<img width="1920" height="1140" alt="image" src="https://github.com/user-attachments/assets/a9426e99-71e7-4a31-9f8b-4c5ed37d3449" />

<img width="1920" height="1140" alt="image" src="https://github.com/user-attachments/assets/c8aa3e3f-a35d-451f-824c-469cdb0498a6" />

Truy cập: http://192.168.1.17:8081 để view bằng phpmyadmin

<img width="1920" height="1140" alt="image" src="https://github.com/user-attachments/assets/3780a218-dfa9-4155-a53f-fc7e3f24b240" />

### c) Tạo Template HTML (pawn_app/templates/home.html):

Đoạn code sử dụng Jinja 2:
```
{% for hd in con_no %}
        <tr
            class="{% if hd.phan_loai == 'qua_han' %}hang-qua-han{% elif hd.phan_loai == 'sap_han' %}hang-sap-han{% else %}hang-trong-han{% endif %}">
            <td>{{ hd.khach_hang.ho_ten }}</td>
            <td>{{ hd.khach_hang.sdt }}</td>
            <td>{{ hd.the_chap }}</td>
            <td>{{ hd.so_tien }} VNĐ</td>
            <td>
                {{ hd.dead_line|date:"d/m/Y" }}

                {% if hd.phan_loai == 'qua_han' %}
                <span class="badge badge-do">ĐÃ QUÁ HẠN</span>
                {% elif hd.phan_loai == 'sap_han' %}
                <span class="badge badge-vang">Sắp đến hạn</span>
                {% else %}
                <span class="badge badge-xanh">Trong hạn (An toàn)</span>
                {% endif %}
            </td>
        </tr>
        {% empty %}
        <tr>
            <td colspan="5" style="text-align: center; padding: 20px; font-weight: bold;">Không có hợp đồng nào đang
                cầm.</td>
        </tr>
        {% endfor %}
```


Thêm dữ liệu và truy cập http://192.168.1.17:8001/ để kiểm tra template:

<img width="1920" height="1140" alt="image" src="https://github.com/user-attachments/assets/00a93552-b36e-4996-924e-167e62dcc34d" />

## 6. Triển khai CloudFlare

### a) Thêm mạng nội bộ cho các Container (dockerc-compose.yml)

Add các dòng này vào mỗi Container

```
networks:

      - default
      
      - web-gateway
```

Thêm phần này vào cuối file

```
networks:

  default:
  
  web-gateway:
  
    external: true
```

### Note: Trong bài này cấu hình sử dụng Nginx và Cloudflare thành master nên chỉ cần mở 1 port cho nhiều project => Thêm cấu hình là xong thay vì phải tạo thêm Container vào docker-compose.yml của từng project => Tiết kiệm RAM, port.
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

Thêm view bằng PHP: pma.luonghoangviet.io.vn

Truy cập kiểm tra: 

camdo.luonghoangviet.io.vn

<img width="1920" height="1140" alt="image" src="https://github.com/user-attachments/assets/8c89c9c3-ecee-45fe-a7a1-5ac8e49d5bce" />

pma.luonghoangviet.io.vn

<img width="1920" height="1140" alt="image" src="https://github.com/user-attachments/assets/28212cf1-4327-489e-b179-15680c03cc09" />





    


