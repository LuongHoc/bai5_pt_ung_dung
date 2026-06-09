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

## PHẦN 5. Tạo flow Node-RED lấy nhiệt độ thực tế

1. Mở giao diện Node-RED

Trên trình duyệt Windows, mở:

```
http://192.168.1.99:1881
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7ca026ab-aa71-4c62-8145-10f74835743e" />

2. Thêm node inject

Tại cột node bên trái, tìm node: ```inject```

Kéo node vào vùng làm việc ở giữa.

Nhấp đúp vào node inject để cấu hình.

Điền như sau:

- msg.payload = timestamp

- Tích vào ô: Inject once after

- Giữ thời gian: 1 seconds

- Chọn: interval

Sau đó giao diện sẽ xuất hiện ô thời gian.

Điền: 60 và chọn đơn vị: seconds

- Nhấn Done

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/22b2520f-a29e-4994-9596-995f854c4cc1" />

3. Thêm node http request

Tại ô tìm kiếm bên trái:

Nhập: ```http request```

Nhấp đúp vào node http request.

Điền:


- Name:	Gọi API Open-Meteo
- Method:	GET
- Return:	a UTF-8 string
- Ô URL dán:

```
https://api.open-meteo.com/v1/forecast?latitude=21.5942&longitude=105.8482&current=temperature_2m&timezone=Asia%2FBangkok
```

- Nhấn: Done

Sau đó nối dây:

inject --> http request

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/531000a9-583d-4ac1-bcb8-2e72e912e29a" />

4. Thêm node JSON

Tại ô tìm kiếm bên trái:

Gõ: ```json```

Kéo node json vào bên phải node HTTP request.

Nhấp đúp node json.

Điền:

- Name:	Chuyển JSON thành Object
- Action:	Always convert to JavaScript Object
- Property:	msg.payload

Nhấn: Done

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/41fccf7a-8f89-4a39-a291-f56e020ae57a" />


5. Tạo node Debug để kiểm tra dữ liệu API

Tại thanh tìm kiếm node bên trái, nhập:

```
debug
```

Nhấp đúp vào node debug.

Điền thông tin:

- Name:	Xem dữ liệu API
- Output:	msg.payload

Nhấn: Done

6. Chạy flow

Nhấn nút: ```Deploy```

- Có thể bấm nút nhỏ bên trái node: Lấy nhiệt độ mỗi 60 giây để chạy thử thủ công.

**Kiểm tra kết quả**

Mở tab: Debug ở phía bên phải giao diện Node-RED.

Kết quả trả về có dạng:

```
msg.payload : Object
    latitude: 21.61687
    longitude: 105.84442
    timezone: "Asia/Bangkok"
    timezone_abbreviation: "GMT+7"
    current_units: object
    current: object
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6ce3a118-4d77-4972-bbc6-c776d2a02b59" />

## PHẦN 6. Phân loại nhiệt độ và lưu giá trị mới nhất vào MariaDB

Bước 1. Cài node kết nối MariaDB

Cài thêm module: ```node-red-node-mysql```

1.1. Mở Palette Manager

Trong giao diện Node-RED, nhấn biểu tượng ba gạch ngang ở góc trên bên phải: ☰

Chọn: ```Manage palette```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/3b0747d8-42b3-4545-8a94-3b3fc0a6b379" />

1.2. Tìm module MySQL

- Chọn tab: ```Install```

- Tại ô tìm kiếm, nhập: ```node-red-node-mysql```

- Khi kết quả xuất hiện, nhấn: ```Install```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/c00400fd-8441-4c0d-9347-d447a400bb2a" />

1.3. Kiểm tra node mới

- Tại ô tìm kiếm node bên trái, nhập: mysql

Bạn cần thấy node màu xanh có tên:

mysql

Chưa kéo node này vào flow ngay. Trước tiên, hãy tạo node xử lý dữ liệu.


Bước 2. Thêm node function

2.1. Tìm node Function

- Tại ô tìm kiếm bên trái, nhập: function

- Nối node JSON với Function

- Mở cấu hình Function

Tại ô: Name

nhập: Phân loại và tạo câu SQL

Nội dung code:

```
// Lấy object current từ dữ liệu Open-Meteo
const current = msg.payload.current;

// Kiểm tra dữ liệu đầu vào
if (!current || current.temperature_2m === undefined) {
    node.error("Không đọc được nhiệt độ từ Open-Meteo", msg);
    return null;
}

// Lấy nhiệt độ và thời gian
const temperature = Number(current.temperature_2m);

let observedAt = String(current.time).replace("T", " ");

// MariaDB DATETIME cần đủ giây: YYYY-MM-DD HH:mm:ss
if (observedAt.length === 16) {
    observedAt += ":00";
}

// Phân loại trạng thái nhiệt độ
let status = "OK";

if (temperature < 18) {
    status = "ALERT LOW";
} else if (temperature > 35) {
    status = "ALERT HIGH";
}

// Tạo câu SQL để ghi đè dòng có id = 1
msg.topic = `
    INSERT INTO weather_latest (
        id,
        city,
        temperature,
        status,
        observed_at
    )
    VALUES (
        1,
        ?,
        ?,
        ?,
        ?
    )
    ON DUPLICATE KEY UPDATE
        city = VALUES(city),
        temperature = VALUES(temperature),
        status = VALUES(status),
        observed_at = VALUES(observed_at);
`;

// Gán dữ liệu vào các dấu ? trong câu SQL
msg.payload = [
    "Thai Nguyen",
    temperature,
    status,
    observedAt
];

// Tạo thêm object để dễ kiểm tra trên Debug
msg.weather = {
    city: "Thai Nguyen",
    temperature: temperature,
    status: status,
    observed_at: observedAt
};

return msg;
```

- Nhấn: Done

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/06917934-4165-4944-a32f-40e6625e912b" />

Bước 3. Thêm node MySQL

Tại ô tìm kiếm bên trái, nhập: mysql

Kéo node mysql vào bên phải node: Phân loại và tạo câu SQL

Cấu hình kết nối MariaDB: Bên cạnh ô cấu hình database, nhấn biểu tượng bút chì để thêm kết nối mới.

Điền:

- Name:	MariaDB Monitor
- Host:	mariadb
- Port:	3306
- User:	monitor_user
- Password:	monitor123
- Database:	monitor_db
- Timezone:	Để mặc định 

Nhấn: Add

Sau đó tại ô Name của node MySQL, nhập: Ghi giá trị mới nhất vào MariaDB

Nhấn: Done

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c4db713e-d695-437f-9bb1-2472169e94f9" />

Bước 4. Thêm node Debug kiểm tra kết quả ghi database

4.1. Thêm Debug mới

Tìm: debug

Kéo một node Debug mới vào bên phải node MySQL.

4.2. Cấu hình Debug

Nhấp đúp node Debug mới.

Điền:

- Name:	Kết quả ghi MariaDB
- Output:	complete msg object

Nhấn: Done

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ba5c3ba7-178d-4601-8547-694e8d698f31" />


Node này giúp quan sát kết quả sau khi chạy câu lệnh SQL.

Bước 5. Deploy và kiểm tra flow

Nhấn: Deploy

Bấm thủ công nút nhỏ bên trái node:

Lấy nhiệt độ mỗi 60 giây

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/142a755d-ee60-4474-8aec-985c1dcf0f10" />


Bước 5. Kiểm tra dữ liệu trong MariaDB

Quay lại Terminal Ubuntu và chạy:

```
cd ~/app-monitor-realtime
```

Sau đó chạy:

```
docker exec -it monitor_mariadb mariadb \
  -u monitor_user \
  -pmonitor123 \
  monitor_db \
  -e "SELECT * FROM weather_latest;"
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/7d02009c-6ca1-4185-a156-0b53f7a1650a" />

## PHẦN 7. Ghi dữ liệu lịch sử vào InfluxDB

1. Phân biệt MariaDB và InfluxDB

MariaDB chỉ giữ một dòng mới nhất:

09:15 → 23.0°C

09:30 → ghi đè thành 24.0°C

09:45 → ghi đè thành 24.5°C

InfluxDB sẽ giữ toàn bộ lịch sử:

09:15 → 23.0°C

09:30 → 24.0°C

09:45 → 24.5°C

Sau này Grafana đọc dữ liệu trong InfluxDB để tạo biểu đồ thay đổi nhiệt độ theo thời gian.

Node node-red-contrib-influxdb hỗ trợ ghi và truy vấn dữ liệu trên InfluxDB 1.x và 2.0. Với node output, dữ liệu gửi qua msg.payload; nếu msg.payload là một mảng gồm hai object thì object đầu tiên là các field, object thứ hai là các tag.

2. Cài node kết nối InfluxDB trong Node-RED
Bước 1. Mở Manage palette

- Trong giao diện Node-RED tại: ```http://192.168.1.99:1881```

- nhấn biểu tượng ba gạch ngang ở góc trên bên phải: ☰

- Chọn: Manage palette

Bước 2. Cài module InfluxDB

- Chọn tab: Install

- Tại ô tìm kiếm, nhập chính xác: ```node-red-contrib-influxdb```

- Nhấn: Install

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/23382503-9cb7-4ec6-8bc7-1ba8416fd8a4" />

3. Thêm node Function chuẩn bị dữ liệu InfluxDB

Bước 1. Kéo node Function

- Tại ô tìm kiếm bên trái, nhập: ```function```

- Kéo thêm một node: ```function``` đặt phía dưới nhánh MariaDB.

Bước 2. Nối dây

Nối từ node:

- Chuyển JSON thành Object sang node Function mới.

Bước 3. Cấu hình Function

- Nhấp đúp node Function mới.

- Tại ô Name, nhập: Chuẩn bị dữ liệu InfluxDB

Xóa code mặc định và dán đoạn sau:

```
// Lấy object current từ dữ liệu Open-Meteo
const current = msg.payload.current;

// Kiểm tra dữ liệu đầu vào
if (!current || current.temperature_2m === undefined) {
    node.error("Không đọc được nhiệt độ để lưu vào InfluxDB", msg);
    return null;
}

// Chuyển nhiệt độ về kiểu số
const temperature = Number(current.temperature_2m);

// Phân loại trạng thái
let status = "OK";

if (temperature < 18) {
    status = "ALERT LOW";
} else if (temperature > 35) {
    status = "ALERT HIGH";
}

// Object thứ nhất: fields - dữ liệu cần lưu và vẽ biểu đồ
// Object thứ hai: tags - nhãn giúp lọc dữ liệu
msg.payload = [
    {
        temperature: temperature
    },
    {
        city: "Thai Nguyen",
        status: status
    }
];

return msg;
```

Nhấn: Done

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c61a4069-0fa2-42f8-a127-1746ab5530c9" />


4. Thêm node influxdb out

Bước 1. Kéo node vào flow

- Tại ô tìm kiếm bên trái, nhập: ```influx```

- Kéo node: ```influxdb out``` vào bên phải node: Chuẩn bị dữ liệu InfluxDB

Bước 2. Mở cấu hình

- Nhấp đúp vào node: influxdb out

- Tại phần cấu hình server, nhấn biểu tượng bút chì để thêm kết nối mới.

7. Cấu hình kết nối InfluxDB 2.0

Điền như sau:

- Name:	InfluxDB Monitor
- Version:	2.0
- URL:	http://influxdb:8086
- Token:	monitor-secret-token-2026-tnut-realtime

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e0daa0c7-4dac-4e9f-86a3-f311e7f94d9f" />

5. Điền thông tin cho node influxdb out

Sau khi thêm server, quay lại cửa sổ cấu hình node output.

Điền:

- Name:	Lưu lịch sử vào InfluxDB
- Measurement:	weather
- Organization:	monitor-org
- Bucket:	weather-history
- Time Precision:	Chọn ms

Nhấn: Done

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/57f96494-b43d-4475-bd25-c4750a96b3f6" />

6. Deploy và chạy thử

- Nhấn: Deploy

- Bấm nút nhỏ bên trái node: Lấy nhiệt độ mỗi 60 giây

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/89c39c00-8f50-4562-9d34-dd12d4142b9f" />

Trong tab Debug, thấy dữ liệu:

```
array[2]
  0: object
      temperature: 23
  1: object
      city: "Thai Nguyen"
      status: "OK"
```

PHẦN 8. Kiểm tra dữ liệu InfluxDB và thêm Grafana


### A. Kiểm tra dữ liệu lịch sử trong InfluxDB

Bước 1. Truy cập giao diện InfluxDB

- Mở trình duyệt và truy cập: ```http://192.168.1.99:8086```

- Đăng nhập bằng tài khoản đã khai báo trong file .env:

Username: admin

Password: admin123456

Bước 2. Mở Data Explorer

- Tại menu bên trái, chọn: Data Explorer

- Data Explorer cho phép truy vấn và trực quan hóa dữ liệu lịch sử đã lưu trong InfluxDB.

Bước 3. Chọn bucket

- Trong cột: FROM

- chọn bucket: weather-history

- Bucket này được sử dụng để lưu chuỗi dữ liệu nhiệt độ theo thời gian.

Bước 4. Chọn measurement

- Trong cột tiếp theo, tại mục: ```_measurement```

- tích chọn: ```weather```

- Measurement có thể hiểu gần giống như tên bảng trong cơ sở dữ liệu quan hệ. Trong ứng dụng này, measurement weather chứa dữ liệu thời tiết.

Bước 5. Chọn field nhiệt độ

- Trong cột: ```_field```

- tích chọn: ```temperature```

- Field temperature là giá trị nhiệt độ được Node-RED gửi vào InfluxDB.

Bước 6. Chọn khoảng thời gian

- Tại mục chọn thời gian ở phía trên bên phải, chọn: Past 1h

Bước 7. Chạy truy vấn

Nhấn nút: ```SUBMIT```

- InfluxDB sẽ hiển thị biểu đồ nhiệt độ theo thời gian.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/aedeba0e-21d7-4d3d-ba5c-39cc6f802191" />


### B. Thêm container Grafana

Sau khi xác nhận InfluxDB có dữ liệu, quay lại Terminal Ubuntu.

Bước 1. Kiểm tra cổng 3000

kiểm tra máy Ubuntu đã có ứng dụng nào dùng cổng này chưa:

```
sudo ss -tulpn | grep :3000
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/a0a9e36d-e680-4462-8c70-b5a034cca36e" />

Bước 2. Bổ sung tài khoản Grafana vào .env

Mở file:

```
nano .env
```

Giữ nguyên các dòng hiện tại và thêm hai dòng cuối:

```
GRAFANA_ADMIN_USER=admin
GRAFANA_ADMIN_PASSWORD=admin123
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/dfb9180a-e686-4ef0-adb1-03c729e74eb5" />

Lưu:

- Ctrl + O
- Enter
- Ctrl + X

Bước 3. Mở file Docker Compose

Chạy:

```
nano docker-compose.yml
```

Thêm service Grafana ngay dưới service influxdb:

```
  grafana:
    image: grafana/grafana:12.4
    container_name: monitor_grafana
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      GF_SECURITY_ADMIN_USER: ${GRAFANA_ADMIN_USER}
      GF_SECURITY_ADMIN_PASSWORD: ${GRAFANA_ADMIN_PASSWORD}
    volumes:
      - grafana_data:/var/lib/grafana
    networks:
      - monitor_network
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/2150de30-bf50-454c-898a-d6ab277f2f61" />

Bước 4. Khai báo volume Grafana

Ở cuối file, tìm phần:

```
volumes:
```

Thêm dòng:

```
  grafana_data:
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/890e1894-0e72-4e06-8d1c-bbe0465ebb51" />

Bước 5. Khởi động Grafana

Chạy:

```
docker compose up -d
```

Sau khi hoàn tất, kiểm tra:

```
docker compose ps
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/89db348e-bfed-4c31-a3d9-f313bee67aae" />

Kết quả có bốn container:

monitor_nodered
monitor_mariadb
monitor_influxdb
monitor_grafana

Bước 9. Truy cập Grafana

Trên trình duyệt Windows, mở:

```
http://192.168.1.99:3000
```

Đăng nhập:

- Username: admin
- Password: admin123

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c6e8233b-358a-4d52-a32c-686fa7c8a6ec" />


## PHẦN 9. Kết nối Grafana với InfluxDB
1. Mở phần Data Sources

- Trong giao diện Grafana, nhìn menu bên trái và chọn: ```Connections```

- Sau đó chọn: ```Data sources```

2. Thêm data source mới

- Nhấn: ```Add new data source```

- Tại ô tìm kiếm, nhập: ```InfluxDB```

- Chọn data source: ```InfluxDB```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/35a78801-3660-4b73-be0b-a78dbaa432e6" />

3. Điền tên data source

Tại ô ```Name```

nhập: ```InfluxDB Weather```

4. Chọn ngôn ngữ truy vấn Flux

Tìm mục: ```Query language```

Chọn: ```Flux```

5. Điền địa chỉ kết nối

Tại phần: HTTP

Tìm ô: URL

Nhập: ```http://influxdb:8086```

6. Điền thông tin xác thực InfluxDB

Cuộn xuống phần: ```InfluxDB Details```

Điền chính xác:

- Organization:	monitor-org
- Token:	monitor-secret-token-2026-tnut-realtime
- Default Bucket:	weather-history

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9d4393dd-fcec-4b99-ad43-876bf18722a5" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2e625ff1-e767-4b7c-a53c-cc31a61059b7" />

7. Lưu và kiểm tra kết nối

Cuộn xuống cuối trang, nhấn: ```Save & test```


## PHẦN 10. Tạo Dashboard và biểu đồ nhiệt độ trong Grafana

1. Mở trang tạo Dashboard

 Trong menu: Dashboard → New → New dashboard → Add visualization

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/5b467f92-4bad-4199-8c1c-657ba6747960" />

2. Chọn nguồn dữ liệu

Khi cửa sổ chọn data source xuất hiện, nhấn:

InfluxDB Weather

Đây là data source bạn vừa cấu hình.

Sau khi chọn, Grafana mở trang chỉnh sửa panel.

3. Nhập truy vấn Flux

Vì data source được cấu hình với ngôn ngữ:

Flux

Grafana sẽ hiển thị vùng nhập code truy vấn. Với Flux, Grafana sử dụng code editor thay vì trình kéo thả trực quan.

Xóa đoạn code mặc định nếu có và dán:

```
from(bucket: "weather-history")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r._measurement == "weather")
  |> filter(fn: (r) => r._field == "temperature")
  |> filter(fn: (r) => r.city == "Thai Nguyen")
  |> aggregateWindow(every: v.windowPeriod, fn: mean, createEmpty: false)
  |> yield(name: "mean")
```

4. Kiểm tra biểu đồ

- Sau khi dán truy vấn, Grafana thường tự chạy truy vấn. Nếu chưa thấy biểu đồ, nhấn: ```Refresh```

- Thấy đường biểu diễn nhiệt độ tương tự biểu đồ đã xuất hiện trong InfluxDB Data Explorer.

5. Chọn kiểu biểu đồ

- Ở phần bên phải

- Chọn: ```Time series```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/002ba798-4ad2-4c0f-a5d1-4f0b5444268b" />

Kiểu Time series phù hợp để biểu diễn nhiệt độ thay đổi theo thời gian.

6. Đặt tên biểu đồ

- Ở bảng cấu hình bên phải, tìm: ```Panel options```

- Tại ô: ```Title```

- Nhập: Lịch sử nhiệt độ Thái Nguyên

7. Chọn đơn vị nhiệt độ

- Tìm mục: Standard options
- Tại ô: Unit
- Tìm: Celsius
- Chọn: Temperature → Celsius (°C)

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/58134bb1-1dfd-4bbf-90d6-33e74f31ed40" />


8. Lưu Dashboard

- Nhấn biểu tượng lưu:
- Save dashboard
- Nhập tên Dashboard: Weather Monitor
- Nhấn: Save


## PHẦN 11. Tạo Flask API đọc dữ liệu từ MariaDB

1. Tạo thư mục Flask API

Tạo thư mục:

```
mkdir -p api
```

Kiểm tra:

```
ls
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/a369608e-9665-48cb-9aa5-0259978992e5" />

2. Tạo file thư viện Python

Chạy:

```
nano api/requirements.txt
```

Dán nội dung:

```
Flask==3.1.2
PyMySQL==1.1.2
```

<img width="1103" height="639" alt="image" src="https://github.com/user-attachments/assets/f1305f5f-b93f-4051-a02f-045fd3e6c234" />

Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

3. Tạo chương trình Flask API

Chạy:

```
nano api/app.py
```

Dán toàn bộ code sau:

```
import os

import pymysql
from flask import Flask, jsonify

app = Flask(__name__)


def get_db_connection():
    """
    Tạo kết nối đến MariaDB.
    Các thông tin được đọc từ biến môi trường của container.
    """
    return pymysql.connect(
        host=os.getenv("DB_HOST", "mariadb"),
        port=int(os.getenv("DB_PORT", "3306")),
        user=os.getenv("DB_USER", "monitor_user"),
        password=os.getenv("DB_PASSWORD", "monitor123"),
        database=os.getenv("DB_NAME", "monitor_db"),
        cursorclass=pymysql.cursors.DictCursor,
    )


@app.get("/health")
def health():
    """
    API đơn giản để kiểm tra Flask đang hoạt động.
    """
    return jsonify({
        "status": "ok",
        "service": "monitor_api"
    })


@app.get("/api/latest")
def get_latest_weather():
    """
    Đọc nhiệt độ mới nhất từ bảng weather_latest trong MariaDB.
    """
    try:
        connection = get_db_connection()

        with connection.cursor() as cursor:
            cursor.execute(
                """
                SELECT
                    id,
                    city,
                    CAST(temperature AS DOUBLE) AS temperature,
                    status,
                    DATE_FORMAT(observed_at, '%%Y-%%m-%%d %%H:%%i:%%s') AS observed_at
                FROM weather_latest
                WHERE id = 1;
                """
            )

            result = cursor.fetchone()

        connection.close()

        if result is None:
            return jsonify({
                "error": "Không tìm thấy dữ liệu nhiệt độ"
            }), 404

        return jsonify(result)

    except Exception as error:
        return jsonify({
            "error": "Không thể đọc dữ liệu từ MariaDB",
            "detail": str(error)
        }), 500


if __name__ == "__main__":
    app.run(
        host="0.0.0.0",
        port=5000,
        debug=False
    )
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7138d493-c1b2-4061-9fec-002a6896444b" />

Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

4. Tạo Dockerfile cho Flask API

Dockerfile mô tả các bước tạo image từ source code.

Chạy:

```
nano api/Dockerfile
```

Dán nội dung:

```
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY app.py .

EXPOSE 5000

CMD ["python", "app.py"]
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/16f8aa38-27d0-4382-9578-5b855cb0d5f6" />

Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

5. Bổ sung service API vào Docker Compose

Mở file:

```
nano docker-compose.yml
```

Thêm service sau phía dưới service grafana nhưng phía trên phần volumes::

```
  api:
    build:
      context: ./api
    container_name: monitor_api
    restart: unless-stopped
    ports:
      - "5001:5000"
    environment:
      DB_HOST: mariadb
      DB_PORT: 3306
      DB_USER: ${MARIADB_USER}
      DB_PASSWORD: ${MARIADB_PASSWORD}
      DB_NAME: ${MARIADB_DATABASE}
    depends_on:
      - mariadb
    networks:
      - monitor_network
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/b419053e-f515-4be0-959c-7f6de3df909d" />

Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

6. Build và chạy Flask API

Chạy:

```
docker compose up -d --build api
```

7. Kiểm tra trạng thái container

Chạy:

```
docker compose ps
```
<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/5e7015c1-d6e9-460d-bd32-aa5bbf181dfd" />

Kết quả cần có năm container:

- monitor_nodered
- monitor_mariadb
- monitor_influxdb
- monitor_grafana
- monitor_api

Container API phải có trạng thái: Up và cổng: 0.0.0.0:5001->5000/tcp

8. Xem log Flask API

Chạy:

```
docker logs monitor_api --tail 30
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/0bdf8c89-227a-41e8-a6bb-087a20a45dc4" />

Kết quả:

- Running on all addresses (0.0.0.0)
- Running on http://127.0.0.1:5000

9. Kiểm tra route /health

Chạy trên Terminal Ubuntu:

```
curl http://localhost:5001/health
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/9d1167eb-67af-4621-8764-30574118a684" />

Kết quả:

{"service":"monitor_api","status":"ok"}

10. Kiểm tra route /api/latest

Chạy:

```
curl http://localhost:5001/api/latest
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/e1676726-b43b-4afe-ac72-75afb7e1f79c" />

Kết quả:

{
  "city": "Thai Nguyen",
  "id": 1,
  "observed_at": "2026-06-09 13:15:00",
  "status": "OK",
  "temperature": 26.7
}

Có thể mở trực tiếp từ trình duyệt Windows:

```
http://192.168.1.99:5001/api/latest
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/26691730-60e4-491e-ab48-22076052f19c" />


## PHẦN 12. Tạo giao diện web và chạy bằng Nginx

1. Tạo thư mục front-end

Tạo thư mục:

```
mkdir -p frontend
```

2. Tạo file HTML

Chạy:

```
nano frontend/index.html
```

Dán toàn bộ nội dung sau:

```
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weather Monitor</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <header>
        <h1>APP MONITOR + ALERT DATA REALTIME</h1>
        <p>Giám sát nhiệt độ thời tiết tại Thái Nguyên</p>
    </header>

    <main>
        <section class="card">
            <h2>Dữ liệu nhiệt độ tức thời</h2>

            <div class="temperature-box">
                <span id="temperature">--</span>
                <span class="unit">°C</span>
            </div>

            <div class="information">
                <p>
                    <strong>Khu vực:</strong>
                    <span id="city">Đang tải dữ liệu...</span>
                </p>

                <p>
                    <strong>Trạng thái:</strong>
                    <span id="status" class="status waiting">WAITING</span>
                </p>

                <p>
                    <strong>Thời gian ghi nhận:</strong>
                    <span id="observed-at">--</span>
                </p>

                <p>
                    <strong>Lần làm mới giao diện:</strong>
                    <span id="updated-at">--</span>
                </p>
            </div>
        </section>

        <section class="card">
            <h2>Ngưỡng cảnh báo</h2>

            <table>
                <thead>
                    <tr>
                        <th>Khoảng nhiệt độ</th>
                        <th>Trạng thái</th>
                    </tr>
                </thead>

                <tbody>
                    <tr>
                        <td>Nhỏ hơn 18°C</td>
                        <td>ALERT LOW</td>
                    </tr>

                    <tr>
                        <td>Từ 18°C đến 35°C</td>
                        <td>OK</td>
                    </tr>

                    <tr>
                        <td>Lớn hơn 35°C</td>
                        <td>ALERT HIGH</td>
                    </tr>
                </tbody>
            </table>
        </section>

        <section class="card">
            <h2>Biểu đồ dữ liệu lịch sử</h2>
            <p>
                Biểu đồ Grafana sẽ được nhúng vào khu vực này
                sau khi trang web hoạt động ổn định.
            </p>
        </section>
    </main>

    <footer>
        <p>APP MONITOR + ALERT DATA REALTIME</p>
    </footer>

    <script src="app.js"></script>
</body>
</html>
```
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5fed606b-5130-4ffd-a308-e481a64e11e1" />

Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

3. Tạo file CSS

Chạy:

```
nano frontend/style.css
```

Dán nội dung:

```
* {
    box-sizing: border-box;
}

body {
    margin: 0;
    font-family: Arial, sans-serif;
    background: #f4f6f8;
    color: #1f2937;
}

header {
    padding: 28px 16px;
    text-align: center;
    background: #1f2937;
    color: white;
}

header h1 {
    margin: 0;
    font-size: 26px;
}

header p {
    margin: 10px 0 0;
}

main {
    width: min(960px, 92%);
    margin: 24px auto;
}

.card {
    margin-bottom: 20px;
    padding: 22px;
    background: white;
    border-radius: 12px;
    box-shadow: 0 3px 12px rgba(0, 0, 0, 0.08);
}

.card h2 {
    margin-top: 0;
}

.temperature-box {
    margin: 18px 0;
    text-align: center;
}

#temperature {
    font-size: 72px;
    font-weight: bold;
}

.unit {
    font-size: 32px;
}

.information p {
    line-height: 1.6;
}

.status {
    display: inline-block;
    padding: 6px 12px;
    border-radius: 20px;
    font-weight: bold;
}

.status.waiting {
    background: #e5e7eb;
    color: #374151;
}

.status.ok {
    background: #dcfce7;
    color: #166534;
}

.status.alert-low {
    background: #dbeafe;
    color: #1d4ed8;
}

.status.alert-high {
    background: #fee2e2;
    color: #b91c1c;
}

.status.error {
    background: #fee2e2;
    color: #b91c1c;
}

table {
    width: 100%;
    border-collapse: collapse;
}

th,
td {
    padding: 12px;
    border: 1px solid #d1d5db;
    text-align: left;
}

th {
    background: #f3f4f6;
}

footer {
    padding: 18px;
    text-align: center;
    background: #1f2937;
    color: white;
}
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/dde89495-255f-458d-8277-26963be94c6f" />

Lưu:

- Ctrl + O
- Enter
- Ctrl + X

4. Tạo file JavaScript

Chạy:

```
nano frontend/app.js
```

Dán nội dung:

```
async function loadLatestWeather() {
    const temperatureElement = document.getElementById("temperature");
    const cityElement = document.getElementById("city");
    const statusElement = document.getElementById("status");
    const observedAtElement = document.getElementById("observed-at");
    const updatedAtElement = document.getElementById("updated-at");

    try {
        const response = await fetch("/api/latest");

        if (!response.ok) {
            throw new Error(`API trả về mã lỗi ${response.status}`);
        }

        const data = await response.json();

        temperatureElement.textContent = Number(data.temperature).toFixed(1);
        cityElement.textContent = data.city;
        statusElement.textContent = data.status;
        observedAtElement.textContent = data.observed_at;

        const statusClass = data.status
            .toLowerCase()
            .replace(/\s+/g, "-");

        statusElement.className = `status ${statusClass}`;

        updatedAtElement.textContent =
            new Date().toLocaleString("vi-VN");

    } catch (error) {
        console.error("Không thể tải dữ liệu:", error);

        temperatureElement.textContent = "--";
        cityElement.textContent = "Không thể kết nối API";
        statusElement.textContent = "ERROR";
        statusElement.className = "status error";
        observedAtElement.textContent = "--";
        updatedAtElement.textContent =
            new Date().toLocaleString("vi-VN");
    }
}

// Tải dữ liệu ngay khi mở trang
loadLatestWeather();

// Tự cập nhật lại dữ liệu sau mỗi 5 giây
setInterval(loadLatestWeather, 5000);
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b6b3b756-2192-47c8-8789-f2aaf0279c55" />

Lưu:

- Ctrl + O
- Enter
- Ctrl + X

5. Tạo file cấu hình Nginx

Chạy:

```
nano frontend/nginx.conf
```

Dán nội dung:

```
server {
    listen 80;
    server_name _;

    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        proxy_pass http://api:5000;
        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b672fb96-e6bc-4ba7-b29c-7e580d9d6752" />

Lưu:

- Ctrl + O
- Enter
- Ctrl + X

6. Kiểm tra các file front-end

Chạy:

```
find frontend -maxdepth 1 -type f -print
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/ca0fd15e-29a7-40b1-acc1-2ed94d6f52c6" />

Kết quả:

- frontend/index.html
- frontend/style.css
- frontend/app.js
- frontend/nginx.conf

7. Thêm service Nginx vào Docker Compose

Mở file:

```
nano docker-compose.yml
```

Thêm service sau bên dưới service api nhưng phía trên phần volumes::

```
  nginx:
    image: nginx:alpine
    container_name: monitor_nginx
    restart: unless-stopped
    ports:
      - "8082:80"
    volumes:
      - ./frontend:/usr/share/nginx/html:ro
      - ./frontend/nginx.conf:/etc/nginx/conf.d/default.conf:ro
    depends_on:
      - api
    networks:
      - monitor_network
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/e6a0ba91-a3b8-41b1-aced-6462a35a180c" />


Lưu:

- Ctrl + O
- Enter
- Ctrl + X

8. Chạy container Nginx

Chạy:

```
docker compose up -d nginx
```

Kiểm tra:

```
docker compose ps
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/3ab86698-787c-4d75-a267-9417a1eae826" />

Kết quả:

Container mới cần có cổng: 0.0.0.0:8082->80/tcp

9. Kiểm tra reverse proxy bằng Terminal

Chạy:

```
curl http://localhost:8082/api/latest
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/0461fc29-9561-4791-9648-7be48c90ec19" />

Kết quả:

{
  "city": "Thai Nguyen",
  "id": 1,
  "observed_at": "2026-06-09 15:30:00",
  "status": "OK",
  "temperature": 25.4
}

15. Mở giao diện web

Trên trình duyệt Windows, mở:

```
http://192.168.1.99:8082
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5d14be2e-6a97-42a0-88ab-fe7699a332c6" />

## PHẦN 13. Nhúng biểu đồ Grafana vào trang web
### Bước 1. Cho phép nhúng iframe trong Grafana

Mở file Docker Compose:

```
nano docker-compose.yml
```

- Tìm service: ```grafana:```

- Bổ sung ba dòng cấu hình mới bên dưới mật khẩu:

```
      GF_SECURITY_ALLOW_EMBEDDING: "true"
      GF_AUTH_ANONYMOUS_ENABLED: "true"
      GF_AUTH_ANONYMOUS_ORG_ROLE: Viewer
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/6b909093-8998-4da0-8049-295131dd6ea6" />

Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

Giải thích

- GF_SECURITY_ALLOW_EMBEDDING:	Cho phép hiển thị Grafana trong iframe
- GF_AUTH_ANONYMOUS_ENABLED:	Cho phép xem biểu đồ mà không cần đăng nhập
- GF_AUTH_ANONYMOUS_ORG_ROLE:	Chỉ cấp quyền xem, không cấp quyền sửa

Bước 2. Khởi động lại riêng container Grafana

Chạy:

```
docker compose up -d --force-recreate grafana
```

Kiểm tra:

```
docker compose ps
```

Container monitor_grafana phải có trạng thái Up: 

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/cb2cea4c-24db-4306-b5f8-d227491ebeac" />

Bước 3. Kiểm tra quyền xem ẩn danh

- Mở cửa sổ ẩn danh trên trình duyệt: ```Ctrl + Shift + N```

- Truy cập:

```
http://192.168.1.99:3000
```

Grafana mở được mà không yêu cầu đăng nhập, cấu hình đã có hiệu lực.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6fe94730-f56d-4c96-b5cc-84f157bf569f" />

Bước 4. Lấy mã iframe của panel Grafana

Quay lại Grafana bằng cửa sổ trình duyệt thông thường:

```
http://192.168.1.99:3000
```

- Mở Dashboard: Dashboards → Weather Monitor

- Di chuột vào panel: Lịch sử nhiệt độ Thái Nguyên
- Nhấn menu của panel ở góc trên bên phải hoặc nhấn vào tên panel.
- Chọn: Share embed

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/b0ecc87f-44f6-44ee-ba3c-63d62bc7cf2b" />


Cấu hình trong cửa sổ Embed

- Tắt ```Lock time range``` để biểu đồ luôn hiển thị dữ liệu mới.
- Chọn theme: Light
- Nhấn: Copy to clipboard

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c35f6c2f-2ff2-4c7a-944a-df8402459461" />

Bước 5. Thêm iframe vào file HTML

Quay lại Terminal Ubuntu:

```
nano frontend/index.html
```

Tìm đoạn:

```
<section class="card">
    <h2>Biểu đồ dữ liệu lịch sử</h2>
    <p>
        Biểu đồ Grafana sẽ được nhúng vào khu vực này
        sau khi trang web hoạt động ổn định.
    </p>
</section>
```

Xóa phần <p>...</p> và dán iframe vừa sao chép.

Thêm thuộc tính:

```
class="grafana-frame"
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/9f332f23-881e-431d-9d59-9e3d8138bc25" />


Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

Bước 6. Thêm CSS cho iframe

Mở file:

```
nano frontend/style.css
```

Thêm đoạn sau vào cuối file:

```
.grafana-frame {
    width: 100%;
    height: 430px;
    border: none;
    border-radius: 8px;
}
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/0fcfc4f8-5a34-44b8-bd5b-d796f7dba75a" />

Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

Bước 7. Mở lại trang web


Trên trình duyệt, mở lại:

```
http://192.168.1.99:8082
```

Nhấn:

Ctrl + F5

để tải lại toàn bộ HTML và CSS.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5055850f-37bc-479c-a40c-c0809b928c91" />


## PHẦN 14. Tạo Telegram Bot và lấy Chat ID của nhóm

1. Tạo Telegram Bot bằng BotFather

Mở Telegram tìm tài khoản chính thức: ```@BotFather```

Gửi lệnh:

```
/newbot
```

BotFather yêu cầu nhập tên bot. Nhập:

```
TNUT Weather Monitor
```

Tiếp theo, BotFather yêu cầu nhập username. Username bắt buộc kết thúc bằng chữ: ```bot```

```
tnut_weather_monitor_2026_bot
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/1ba41f14-7a24-4ff8-810e-5e3f5f4a87f5" />

2. Tạo nhóm Telegram để nhận cảnh báo

- Tạo một nhóm Telegram mới.

- Tên nhóm có thể đặt: TNUT Weather Monitor Alert

- Thêm ít nhất một người khác vào nhóm. Khi cộng thêm bạn và bot, nhóm sẽ có các thành viên cần thiết để demo cảnh báo.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c1f9f0d9-bcf3-4a87-9110-bc4ffac1ccd3" />

3. Gửi một lệnh thử nghiệm trong nhóm

Trong nhóm Telegram, gửi:

/test

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/cf1fbef1-7209-4c14-9f35-f079510378c1" />

4. Lấy Chat ID bằng Terminal Ubuntu

- Quay lại Terminal Ubuntu.

- Chạy lệnh:

```
curl "https://api.telegram.org/botTOKEN_CUA_BAN/getUpdates"
```
- Tìm Chat ID trong kết quả

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/218dcde5-49db-48c9-b257-148204c475f8" />

ID là:
```
-5269158210
```

5. Kiểm tra bot bằng lệnh gửi tin nhắn trực tiếp

Sau khi có Chat ID, chạy thử lệnh sau. Thay token và Chat ID bằng giá trị thật:

```
curl -X POST "https://api.telegram.org/bot8514316926:AAFXqgzgOC-yMNR08k-kOq8EKQof5Etf0QI/sendMessage" \
  -d "chat_id=-5269158210" \
  --data-urlencode "text=TEST: Bot cảnh báo thời tiết đã kết nối thành công."
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/700aebcd-023c-4b85-985b-6e84a95b0626" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c7bf9d47-030c-4f1f-a08f-9c08120ef405" />

Nhóm Telegram nhận được tin nhắn: TEST: Bot cảnh báo thời tiết đã kết nối thành công. thì bot đã hoạt động đúng.

6. Bổ sung cấu hình vào file .env

Sau khi kiểm tra bot thành công, quay lại thư mục dự án:

```
cd ~/app-monitor-realtime
```

Mở file:

```
nano .env
```

Thêm hai dòng cuối:

```
TELEGRAM_BOT_TOKEN=TOKEN_THAT_CUA_BAN
TELEGRAM_CHAT_ID=CHAT_ID_THAT_CUA_NHOM
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/b5ecaa0a-4d6d-4c4b-8598-3ab13300b539" />

Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

File .env đã nằm trong .gitignore, vì vậy không đẩy file này lên GitHub.

































































