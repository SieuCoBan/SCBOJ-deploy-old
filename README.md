# SCBOJ Deploy
Sử dụng docker cài đặt [SCBOJ](https://github.com/SieuCoBan/SCBOJ) dựa trên [DMOJ Docker](https://github.com/Ninjaclasher/dmoj-docker)
- Triển khai tại: [HackNao.com](https://hacknao.com)
- Dự án chỉ gồm phần Online Jugde mà chư bao gồm máy chấm
- Xem hướng dẫn cài máy chấm tại: [SCBOJ-judge](https://github.com/SieuCoBan/SCBOJ-judge)

Lưu ý:
--------------------------------
- Nếu đang dùng user có quyền root thì có thể bỏ lệnh ```sudo``` ở các dòng lệnh trong hướng dẫn bên dưới
## 1. Cài đặt docker
- Update hệ thống và cài chứng chỉ
```
sudo apt update
sudo apt install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```
- Cài Docker và Docker Compose
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io

sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```
## 2. Cài đặt SCBOJ

## 2.1. Clone git của dự án
```
git clone --recursive https://github.com/SieuCoBan/SCBOJ-deploy.git
cd SCBOJ-deploy/dmoj
```
## 2.2. Cấu hình
### 2.2.1. Cấu hình website và logo

Thay đổi logo và cấu hình trong local_settings.py
```
sudo ./scripts/initialize
```
### 2.2.1. Cấu hình cơ sở dữ liệu và tên miền
**Cách 1: sử dụng cấu hình demo (không dùng cho product)**

Sao chép các file cấu hình demo trong ```dmoj/environment/```
```
cp environment/mysql-admin.env.example environment/mysql-admin.env
cp environment/mysql.env.example environment/mysql.env
cp environment/site.env.example environment/site.env
```

**Cách 2: tự cấu hình**

Sao chép các file cấu hình mặc định trong ```dmoj/environment/```
```
cp environment/mysql-admin.env.default environment/mysql-admin.env
cp environment/mysql.env.default environment/mysql.env
cp environment/site.env.default environment/site.env
```

Chỉnh sữa biến môi trường trong của các file trong thư mục 
- Nếu triển khai cho product thì thay localhost bằng địa chỉ website. Ví dụ: [HackNao.com](https://hacknao.com)


```
nano environment/mysql.env
nano environment/mysql-admin.env
nano environment/site.env
```

Chỉnh sữa server_name trong ```nginx/conf.d/nginx.conf```
```
nano nginx/conf.d/nginx.conf
```

## 2.3. Build docker và load dữ liệu

Build docker
```
sudo docker compose build
sudo docker compose up -d site
```
Build dữ liệu và file tĩnh
```
sudo ./scripts/migrate
sudo ./scripts/copy_static
sudo ./scripts/manage.py loaddata navbar
sudo ./scripts/manage.py loaddata language_small
sudo ./scripts/manage.py loaddata demo
```

Restart docker để đảm bảo không có lỗi
```
sudo docker compose restart
```

Nếu muốn tạo thêm tài khoản superuser (quyền admin)
```
sudo ./scripts/manage.py createsuperuser
```

Sau đó truy cập http://localhost hoặc địa chỉ bạn cấu hình. VD: http://hacknao.com


## 2.4. Cập nhật khi cần thiết

```
sudo ./scripts/initialize
sudo ./scripts/migrate
sudo ./scripts/copy_static
```
Khởi động lại toàn bộ docker
```
sudo docker compose restart
```

# 3. Cài máy chấm
- Xem hướng dẫn cài máy chấm tại: [SCBOJ-judge](https://github.com/SieuCoBan/SCBOJ-judge)

