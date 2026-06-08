# Họ và tên: Lương Văn Học - MSSV: K225480106025
# Lớp: K58KTP
# Môn: Phát triển ứng dụng với mã nguồn mở - tee0421
# Bài tập 5:

# A) Phần lý thuyết
## 1) Docker là gì?

Docker là một nền tảng dùng để đóng gói ứng dụng và toàn bộ môi trường chạy của nó vào trong các container.

Nói đơn giản:

- Bạn viết app trên máy của mình
- App chạy được trên máy bạn
- Khi mang sang máy khác, app vẫn chạy y như vậy vì đã có đủ thư viện, cấu hình, runtime cần thiết

**Thành phần cơ bản**

- Image: bản mẫu để tạo container
- Container: môi trường đang chạy từ image
- Dockerfile: file mô tả cách tạo image
- Docker Compose: file cấu hình để chạy nhiều container cùng lúc

**Ví dụ**

Nếu có một app Python:

- Python version
- thư viện Flask
- file code app
- biến môi trường

Tất cả có thể được đóng gói vào Docker để khi chạy ở máy khác không bị lỗi “thiếu thư viện” hay “sai phiên bản”.

## 2) Các keyword trong docker-compose.yml

```docker-compose.yml``` là file dùng để khai báo và quản lý nhiều service trong một ứng dụng.

Các từ khóa thường gặp:

**a) version**

Chỉ định phiên bản cú pháp của file Compose.

Ví dụ:

```
version: "3.8"
```

Ý nghĩa:

Docker Compose sẽ hiểu file theo chuẩn version 3.8.

**b) services**

Dùng để khai báo các container/dịch vụ trong hệ thống.

Ví dụ:

```
services:
  web:
    image: nginx
  db:
    image: mariadb
```
**Ý nghĩa:**

Trong ứng dụng có 2 service:

- web: chạy nginx
- db: chạy mariadb

**c) image**

Chỉ định image sẽ dùng để tạo container.

**Ví dụ:**

```
services:
  web:
    image: nginx:alpine
```

**Ý nghĩa:**

Dùng image nginx:alpine để chạy service web.

**d) build**

Dùng để build image từ Dockerfile.

**Ví dụ:**

```
services:
  app:
    build: .
```

**Ý nghĩa:**

Docker sẽ đọc Dockerfile trong thư mục hiện tại để tạo image.

**e) container_name**

Đặt tên cụ thể cho container.

**Ví dụ:**

```
services:
  app:
    container_name: my_app
```
**Ý nghĩa:**

Container sẽ có tên my_app.

**f) ports**

Map port từ máy host vào container.

**Ví dụ:**

```
services:
  web:
    ports:
      - "8080:80"
```
**Ý nghĩa:**

Truy cập localhost:8080 trên máy host sẽ được chuyển vào cổng 80 trong container.

**g) environment**

Khai báo biến môi trường.

**Ví dụ:**

```
services:
  db:
    environment:
      MYSQL_ROOT_PASSWORD: 123456
```

**Ý nghĩa:**

Đặt mật khẩu root cho MariaDB/MySQL.

**h) volumes**

Dùng để lưu dữ liệu bền vững hoặc mount thư mục từ host vào container.

**Ví dụ:**

```
services:
  db:
    volumes:
      - db_data:/var/lib/mysql
```

**Ý nghĩa:**

Dữ liệu database được lưu ở volume db_data, không mất khi container bị xoá.

**i) networks**

Khai báo mạng để các container giao tiếp với nhau.

**Ví dụ:**

```
services:
  web:
    networks:
      - app_net
  db:
    networks:
      - app_net
```
**Ý nghĩa:**

web và db cùng nằm trong mạng app_net, nên có thể liên lạc bằng tên service.

**j) depends_on**

Chỉ định service nào cần khởi động trước.

**Ví dụ:**

```
services:
  app:
    depends_on:
      - db
```

**Ý nghĩa:**
db sẽ được khởi động trước app.

**Lưu ý:**

depends_on chỉ đảm bảo thứ tự khởi động, không đảm bảo dịch vụ đã sẵn sàng hoàn toàn.

**k) restart**

Quy định chính sách tự khởi động lại container.

**Ví dụ:**

```
services:
  app:
    restart: always
```

**Ý nghĩa:**

Nếu container lỗi hoặc máy khởi động lại thì container sẽ tự chạy lại.

**l) command**

Ghi đè lệnh mặc định khi container chạy.

**Ví dụ:**

```
services:
  app:
    command: python app.py
```

**Ý nghĩa:**

Container sẽ chạy lệnh python app.py.

**m) volumes: ở mức gốc**

Khai báo named volume ở ngoài phần services.

**Ví dụ:**

```
volumes:
  db_data:
```

**Ý nghĩa:**
Tạo volume tên db_data để dùng chung hoặc lưu dữ liệu.

**n) networks: ở mức gốc**
Khai báo network riêng.

**Ví dụ:**

```
networks:
  app_net:
```
**Ý nghĩa:**

Tạo network riêng tên app_net.

3) Ưu điểm khi triển khai app bằng Docker là gì?
a) Chạy ổn định, đồng nhất
App chạy trên máy nào cũng giống nhau nếu dùng cùng image.

b) Dễ đóng gói và di chuyển
Chỉ cần mang image/container sang máy khác là chạy được.

c) Tiết kiệm tài nguyên hơn máy ảo
Docker nhẹ hơn VM vì dùng chung kernel của hệ điều hành.

d) Dễ triển khai nhiều dịch vụ
Rất phù hợp với hệ thống gồm:

web
database
cache
monitoring
message broker
e) Dễ bảo trì và cập nhật
Chỉ cần sửa Dockerfile / compose rồi rebuild.

f) Tách biệt môi trường
App không bị ảnh hưởng nhiều bởi môi trường cài đặt trên máy host.

4) Nếu app đã test OK trên laptop cá nhân, muốn triển khai lên máy chủ thật không có internet thì làm thế nào?
Đây là tình huống offline deployment.

Các bước cơ bản:
Bước 1: Build image trên máy có internet
Trên laptop, bạn build image cho từng service.

Ví dụ:

bash
docker build -t myapp-web:1.0 .
Hoặc dùng compose:

bash
docker compose build
Bước 2: Lưu image ra file
Xuất image thành file .tar.

Ví dụ:

bash
docker save -o myapp-web.tar myapp-web:1.0
Nếu có nhiều image:

bash
docker save -o all-images.tar image1 image2 image3
Bước 3: Chuyển file sang máy chủ offline
Dùng:

USB
ổ cứng ngoài
SCP nội bộ nếu có mạng LAN
công cụ sao chép nội bộ
Bước 4: Load image vào máy chủ thật
Trên server offline, nạp lại image từ file tar:

bash
docker load -i myapp-web.tar
Bước 5: Copy file compose và dữ liệu cần thiết
Mang theo:

docker-compose.yml
.env nếu có
file cấu hình
thư mục dữ liệu, nếu dùng volume bind mount
Bước 6: Chạy container trên server
bash
docker compose up -d
Bước 7: Kiểm tra hoạt động
Dùng:

bash
docker ps
docker logs <container_name>
5) Mẫu câu trả lời ngắn gọn để đưa vào bài
Bạn có thể viết như sau:

Docker là nền tảng dùng để đóng gói ứng dụng và các thành phần phụ thuộc vào trong container, giúp ứng dụng chạy đồng nhất trên nhiều môi trường.
File docker-compose.yml dùng để khai báo nhiều service như services, image, build, ports, volumes, networks, environment, depends_on, restart, command... nhằm mô tả hệ thống chạy ứng dụng.
Ưu điểm của Docker là dễ triển khai, nhẹ hơn máy ảo, chạy đồng nhất, dễ mở rộng và bảo trì.
Khi muốn triển khai ứng dụng lên máy chủ không có internet, cần build image trên máy có mạng, export image bằng docker save, chuyển file sang server, import lại bằng docker load, sau đó chạy docker compose up -d.



# B) Thực hành

**Bước 1. Mở máy ảo Ubuntu**


Tại PowerShell trên Windows, SSH vào máy ảo:

```
ssh hoc@192.168.1.99
```

**Bước 2. Kiểm tra Docker**

Chạy:

```
sudo docker --version
```

Tiếp tục chạy:

```
sudo docker compose version
```

<img width="1105" height="644" alt="image" src="https://github.com/user-attachments/assets/ace7eb06-42c5-4e18-a59d-636d04fe5d99" />

**Bước 3. Tạo thư mục dự án**

Chạy:

```
mkdir -p ~/app-monitor-realtime
```

Đi vào thư mục:

```
cd ~/app-monitor-realtime
```

Kiểm tra vị trí hiện tại:

```
pwd
```

<img width="1109" height="642" alt="image" src="https://github.com/user-attachments/assets/4b868467-6db3-465d-be6c-b899081f507f" />

**Bước 4. Tạo file docker-compose.yml**

Chạy:

```
nano docker-compose.yml
```

<img width="1106" height="644" alt="image" src="https://github.com/user-attachments/assets/fd476a3b-af9d-44f9-ae7d-237e8d53fe66" />

Dán nội dung sau:

```
services:
  nodered:
    image: nodered/node-red:latest
    container_name: monitor_nodered
    restart: unless-stopped
    ports:
      - "1881:1880"
    volumes:
      - nodered_data:/data
    networks:
      - monitor_network

volumes:
  nodered_data:

networks:
  monitor_network:
    driver: bridge
```

<img width="1103" height="639" alt="image" src="https://github.com/user-attachments/assets/7567348a-beb0-47a6-87f8-421ac5e875e1" />

**Bước 5. Chạy container Node-RED**

Chạy:

```
docker compose up -d
```

<img width="1104" height="641" alt="image" src="https://github.com/user-attachments/assets/fd4e9272-7c17-41f9-a336-d70583ef3cc8" />

**Bước 6. Kiểm tra container mới**

Chạy:

```
docker compose ps
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/48586b10-ecd6-4b48-aa6f-4eb4a605191f" />


**Bước 7. Xem log Node-RED**

Chạy:

```
docker logs monitor_nodered --tail 30
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/fd45779d-44a4-43b5-bf30-de16967612e1" />

**Bước 8. Mở Node-RED từ trình duyệt Windows**

Trên Windows, mở trình duyệt và nhập:

```
http://192.168.1.99:1881
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/cb8803fd-8ee8-495b-a3b7-33a0bf994da6" />


## PHẦN 3. Thêm MariaDB để lưu giá trị nhiệt độ tức thời

1. Tạo thư mục chứa file khởi tạo database

Tạo thư mục db:

```
mkdir -p db
```

Kiểm tra:

```
ls
```

<img width="1108" height="640" alt="image" src="https://github.com/user-attachments/assets/67c25ea3-9bb4-4a60-95c1-18d897586c53" />

2. Tạo file SQL khởi tạo bảng

Chạy:

```
nano db/init.sql
```

Dán nội dung sau:

```
CREATE DATABASE IF NOT EXISTS monitor_db;

USE monitor_db;

CREATE TABLE IF NOT EXISTS weather_latest (
    id INT PRIMARY KEY,
    city VARCHAR(100) NOT NULL,
    temperature DECIMAL(5,2) NOT NULL,
    status VARCHAR(30) NOT NULL,
    observed_at DATETIME NOT NULL
);

INSERT INTO weather_latest (
    id,
    city,
    temperature,
    status,
    observed_at
)
VALUES (
    1,
    'Thai Nguyen',
    0,
    'WAITING',
    NOW()
)
ON DUPLICATE KEY UPDATE
    city = VALUES(city);
```

Lưu file: 

- Ctrl + O
- Enter
- Ctrl + X

Kiểm tra lại:

cat db/init.sql

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/d99454b4-191f-40b0-81fa-d60bfebff25e" />

3. Tạo file .env để lưu cấu hình


Chạy:

```
nano .env
```

Dán nội dung:

```
MARIADB_ROOT_PASSWORD=root123
MARIADB_DATABASE=monitor_db
MARIADB_USER=monitor_user
MARIADB_PASSWORD=monitor123
```

Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

Kiểm tra:

```
cat .env
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/1579af10-103e-422d-b7ce-6b472acb8523" />

4. Tạo file .gitignore

File .env chứa mật khẩu nên không được đẩy lên GitHub.

Chạy:

```
nano .gitignore
```

Dán nội dung:

```
.env
```

<img width="1103" height="639" alt="image" src="https://github.com/user-attachments/assets/bc19d36e-769a-4e4d-b22c-53ec2103fa27" />

Lưu lại:

- Ctrl + O
- Enter
- Ctrl + X

5. Sửa file docker-compose.yml

Mở file:

```
nano docker-compose.yml
```

Xóa nội dung cũ và thay bằng toàn bộ nội dung sau:

```
services:
  nodered:
    image: nodered/node-red:latest
    container_name: monitor_nodered
    restart: unless-stopped
    ports:
      - "1881:1880"
    volumes:
      - nodered_data:/data
    networks:
      - monitor_network

  mariadb:
    image: mariadb:11.4
    container_name: monitor_mariadb
    restart: unless-stopped
    env_file:
      - .env
    ports:
      - "3307:3306"
    volumes:
      - mariadb_data:/var/lib/mysql
      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql:ro
    networks:
      - monitor_network

volumes:
  nodered_data:
  mariadb_data:

networks:
  monitor_network:
    driver: bridge
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c7f016ee-70c8-4ef4-a132-cbdb0fd8f5e4" />

Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

6. Khởi động MariaDB

Chạy:

```
docker compose up -d
```

Kiểm tra:

```
docker compose ps
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/022b6aad-42e4-460c-b27f-acf95d07782e" />


## PHẦN 4. Thêm InfluxDB để lưu dữ liệu lịch sử

Bước 1. Bổ sung cấu hình InfluxDB vào file .env

Mở file:

```
nano .env

```
Bổ sung thêm các dòng InfluxDB bên dưới:

```
INFLUXDB_USERNAME=admin
INFLUXDB_PASSWORD=admin123456
INFLUXDB_ORG=monitor-org
INFLUXDB_BUCKET=weather-history
INFLUXDB_TOKEN=monitor-secret-token-2026-tnut-realtime
```

<img width="1103" height="639" alt="image" src="https://github.com/user-attachments/assets/ab7b5cd5-2cfb-4724-b8b8-d19a21b98a04" />

Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

Bước 2. Sửa file docker-compose.yml

Mở file:

```
nano docker-compose.yml
```

Xóa nội dung cũ và thay bằng toàn bộ nội dung sau:

```
services:
  nodered:
    image: nodered/node-red:latest
    container_name: monitor_nodered
    restart: unless-stopped
    ports:
      - "1881:1880"
    volumes:
      - nodered_data:/data
    networks:
      - monitor_network

  mariadb:
    image: mariadb:11.4
    container_name: monitor_mariadb
    restart: unless-stopped
    env_file:
      - .env
    ports:
      - "3307:3306"
    volumes:
      - mariadb_data:/var/lib/mysql
      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql:ro
    networks:
      - monitor_network

  influxdb:
    image: influxdb:2.8.0
    container_name: monitor_influxdb
    restart: unless-stopped
    ports:
      - "8086:8086"
    environment:
      DOCKER_INFLUXDB_INIT_MODE: setup
      DOCKER_INFLUXDB_INIT_USERNAME: ${INFLUXDB_USERNAME}
      DOCKER_INFLUXDB_INIT_PASSWORD: ${INFLUXDB_PASSWORD}
      DOCKER_INFLUXDB_INIT_ORG: ${INFLUXDB_ORG}
      DOCKER_INFLUXDB_INIT_BUCKET: ${INFLUXDB_BUCKET}
      DOCKER_INFLUXDB_INIT_ADMIN_TOKEN: ${INFLUXDB_TOKEN}
    volumes:
      - influxdb_data:/var/lib/influxdb2
      - influxdb_config:/etc/influxdb2
    networks:
      - monitor_network

volumes:
  nodered_data:
  mariadb_data:
  influxdb_data:
  influxdb_config:

networks:
  monitor_network:
    driver: bridge
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c846c220-2d81-42c8-9f5f-a6357832b7dc" />


Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

Bước 3. Chạy InfluxDB

Chạy:

```
docker compose up -d
```

Sau khi hoàn tất, chạy:

```
docker compose ps
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/6601c1e5-9968-484a-9171-8df5e74f45c7" />

Kết quả cần có ba container:

- monitor_nodered
- monitor_mariadb
- monitor_influxdb

Bước 4. Kiểm tra InfluxDB bằng trình duyệt

Trên Windows, mở:

http://192.168.1.99:8086

Đăng nhập:

Username: admin

Password: admin123456

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9523c50f-0c94-42ee-9c0d-c0c3c64efe67" />





































