# Họ và tên: Lương Văn Học - MSSV: K225480106025
# Lớp: K58KTP
# Môn: Phát triển ứng dụng với mã nguồn mở - tee0421
# Bài tập 5:



# B. Thực hành

# Phần 1. Chuẩn bị môi trường và triển khai các service

## 1.1. Kiểm tra Docker 

### 1.1.1. Kiểm tra phiên bản Docker

Chạy:

```
sudo docker --version
```

Tiếp tục chạy:

```
sudo docker compose version
```

<img width="1105" height="644" alt="image" src="https://github.com/user-attachments/assets/ace7eb06-42c5-4e18-a59d-636d04fe5d99" />

## 1.2. Tạo thư mục dự án

### 1.2.1. Tạo thư mục

Chạy:

```
mkdir -p ~/app-monitor-realtime
```

### 1.2.2. Đi vào thư mục:

```
cd ~/app-monitor-realtime
```

### 1.2.3. Kiểm tra vị trí hiện tại:

```
pwd
```

<img width="1109" height="642" alt="image" src="https://github.com/user-attachments/assets/4b868467-6db3-465d-be6c-b899081f507f" />

## 1.2. Triển khai Node-RED

### 1.2.1. Tạo file ```docker-compose.yml```

Chạy:

```
nano docker-compose.yml
```

### 1.2.2. Khai báo service Node-RED

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

### 1.2.3. Khởi động Node-RED

Chạy:

```
docker compose up -d
```

<img width="1104" height="641" alt="image" src="https://github.com/user-attachments/assets/fd4e9272-7c17-41f9-a336-d70583ef3cc8" />

**Kiểm tra container mới**

Chạy:

```
docker compose ps
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/48586b10-ecd6-4b48-aa6f-4eb4a605191f" />


**Xem log Node-RED**

Chạy:

```
docker logs monitor_nodered --tail 30
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/fd45779d-44a4-43b5-bf30-de16967612e1" />

### 1.2.4. Mở Node-RED từ trình duyệt Windows

Trên Windows, mở trình duyệt và nhập:

```
http://192.168.1.99:1881
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7ca026ab-aa71-4c62-8145-10f74835743e" />

## 1.3. Tạo file cấu hình môi trường

### 1.3.1. Tạo file ```.env``` 

```
nano .env
```

### 1.3.2. Khai báo biến môi trường

```
MARIADB_ROOT_PASSWORD=root123
MARIADB_DATABASE=monitor_db
MARIADB_USER=monitor_user
MARIADB_PASSWORD=monitor123

INFLUXDB_USERNAME=admin
INFLUXDB_PASSWORD=admin123456
INFLUXDB_ORG=monitor-org
INFLUXDB_BUCKET=weather-history
INFLUXDB_TOKEN=monitor-secret-token-2026-tnut-realtime

GRAFANA_ADMIN_USER=admin
GRAFANA_ADMIN_PASSWORD=admin123

TELEGRAM_BOT_TOKEN=8514316926:AAFXqgzgOC-yMNR08k-kOq8EKQof5Etf0QI
TELEGRAM_CHAT_ID=-5269158210
```

Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

<img width="1103" height="639" alt="image" src="https://github.com/user-attachments/assets/72f52ca3-ad43-4916-b92d-be6ac70aa2a1" />

### 1.3.3. Tạo file .gitignore


Chạy:

```
nano .gitignore
```

Dán nội dung:

```
.env
backup/
```

<img width="1103" height="639" alt="image" src="https://github.com/user-attachments/assets/fd83c153-eaab-4c5f-b755-3e9cce43e623" />

Lưu lại:

- Ctrl + O
- Enter
- Ctrl + X

## 1.4. Triển khai MariaDB

### 1.4.1. Tạo thư mục chứa file khởi tạo database

Tạo thư mục db:

```
mkdir -p db
```

Kiểm tra:

```
ls
```

<img width="1108" height="640" alt="image" src="https://github.com/user-attachments/assets/67c25ea3-9bb4-4a60-95c1-18d897586c53" />

## 1.4.2. Tạo file SQL khởi tạo bảng

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

### 1.4.3. Khai báo service MariaDB

- Sửa file ```docker-compose.yml```
- Mở file:

```
nano docker-compose.yml
```

Thêm nội dung sau:

```
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
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c7f016ee-70c8-4ef4-a132-cbdb0fd8f5e4" />

Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

### 1.4.4 Khởi động MariaDB

Chạy:

```
docker compose up -d
```

Kiểm tra:

```
docker compose ps
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/022b6aad-42e4-460c-b27f-acf95d07782e" />


## 1.5. Triển khai InfluxDB

### 1.5.1. Khai báo service InfluxDB

Mở file:

```
nano docker-compose.yml
```

Thêm nội dung sau:

```
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
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c846c220-2d81-42c8-9f5f-a6357832b7dc" />


Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

### 1.5.2. Khởi động InfluxDB

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

### 1.5.2. Kiểm tra InfluxDB bằng trình duyệt

Trên Windows, mở:
```
http://192.168.1.99:8086
```
Đăng nhập:

- Username: ```admin```
- Password: ```admin123456```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9523c50f-0c94-42ee-9c0d-c0c3c64efe67" />

## 1.6. Triển khai Grafana
### 1.6.1. Khai báo service Grafana

Chạy:

```
nano docker-compose.yml
```

**Thêm service Grafana ngay dưới service influxdb:**

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

**Khai báo volume Grafana**

Ở cuối file, tìm phần:

```
volumes:
```

Thêm dòng:

```
  grafana_data:
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/890e1894-0e72-4e06-8d1c-bbe0465ebb51" />

### 1.6.2. Khởi động Grafana

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

- monitor_nodered
- monitor_mariadb
- monitor_influxdb
- monitor_grafana

### 1.6.3. Truy cập giao diện Grafana

Trên trình duyệt Windows, mở:

```
http://192.168.1.99:3000
```

Đăng nhập:

- Username: admin
- Password: admin123

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c6e8233b-358a-4d52-a32c-686fa7c8a6ec" />

# 2. Xây dựng flow Node-RED

## 2.1. Lấy dữ liệu từ Open-Meteo API

Tạo flow Node-RED lấy nhiệt độ thực tế

### 2.1.1. Mở giao diện Node-RED

Trên trình duyệt Windows, mở:

```
http://192.168.1.99:1881
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7ca026ab-aa71-4c62-8145-10f74835743e" />

### 2.1.2. Tạo node inject

Cấu hình node ```inject``` như sau:

- msg.payload = timestamp

- Tích vào ô: Inject once after

- Giữ thời gian: 1 seconds

- Chọn: interval

- Nhấn Done

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/22b2520f-a29e-4994-9596-995f854c4cc1" />

### 2.1.3. Tạo node http request

Cấu hình node ```http request```:

- Name:	Gọi API Open-Meteo
- Method:	GET
- Return:	a UTF-8 string
- Ô URL dán:

```
https://api.open-meteo.com/v1/forecast?latitude=21.5942&longitude=105.8482&current=temperature_2m&timezone=Asia%2FBangkok
```

- Nhấn: Done

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/531000a9-583d-4ac1-bcb8-2e72e912e29a" />

### 2.1.4. Tạo node JSON

Cấu hình node ```json```:

- Name:	Chuyển JSON thành Object
- Action:	Always convert to JavaScript Object
- Property:	msg.payload

Nhấn: Done

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/41fccf7a-8f89-4a39-a291-f56e020ae57a" />

### 2.1.5. Tạo node Debug để kiểm tra dữ liệu API

Cấu hình node ```Debug```:

- Name:	Xem dữ liệu API
- Output:	msg.payload

Nhấn: Done

<img width="1920" height="1031" alt="image" src="https://github.com/user-attachments/assets/9adadfea-00f7-46de-b51b-7792b763f715" />

### 2.1.6. Chạy flow

Nối các node với nhau:
```
Inject → HTTP Request → JSON → Debug
```

Nhấn nút: ```Deploy```

Có thể bấm nút nhỏ bên trái node: Lấy nhiệt độ mỗi 60 giây để chạy thử thủ công.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6ce3a118-4d77-4972-bbc6-c776d2a02b59" />

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

## 2.2. Lưu giá trị mới nhất vào MariaDB


### 2.2.1. Cài node MySQL

Bước 1. Mở Palette Manager

Trong giao diện Node-RED, nhấn biểu tượng ba gạch ngang ở góc trên bên phải: ☰

Chọn: ```Manage palette```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/3b0747d8-42b3-4545-8a94-3b3fc0a6b379" />

Bước 3. Tìm module MySQL

- Chọn tab: ```Install```
- Tại ô tìm kiếm, nhập: ```node-red-node-mysql```
- Khi kết quả xuất hiện, nhấn: ```Install```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/c00400fd-8441-4c0d-9347-d447a400bb2a" />

### 2.2.2. Tạo Function phân loại và tạo câu SQL

Mở cấu hình Function

- Tại ô ```Name``` nhập: ```Phân loại và tạo câu SQL```
- Nội dung code:

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

### 2.2.3. Tạo node MySQL

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

Sau đó tại ô Name của node MySQL, nhập: ```Ghi giá trị mới nhất vào MariaDB```

Nhấn: Done

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c4db713e-d695-437f-9bb1-2472169e94f9" />

### 2.2.4. Thêm node Debug kiểm tra kết quả ghi database

Cấu hình Debug

- Name:	Kết quả ghi MariaDB
- Output:	complete msg object

Nhấn: Done

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ba5c3ba7-178d-4601-8547-694e8d698f31" />


Node này giúp quan sát kết quả sau khi chạy câu lệnh SQL.

### 2.2.5. Deploy và kiểm tra flow

Nối các node với nhau:
```
json → function → sql → Debug
```

Nhấn: Deploy

Bấm thủ công nút nhỏ bên trái node: ```Lấy nhiệt độ mỗi 60 giây```

<img width="1628" height="926" alt="image" src="https://github.com/user-attachments/assets/68327274-586d-4c92-ab19-69067edd0441" />

### 2.2.6. Kiểm tra dữ liệu trong MariaDB

Quay lại Terminal Ubuntu và chạy:

```
docker exec -it monitor_mariadb mariadb \
  -u monitor_user \
  -pmonitor123 \
  monitor_db \
  -e "SELECT * FROM weather_latest;"
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/7d02009c-6ca1-4185-a156-0b53f7a1650a" />

## 2.3. Lưu dữ liệu lịch sử vào InfluxDB

### 2.3.1. Cài module InfluxDB

Bước 1. Mở Manage palette

- nhấn biểu tượng ba gạch ngang ở góc trên bên phải: ☰

- Chọn: Manage palette

Bước 2. Cài module InfluxDB

- Chọn tab: Install

- Tại ô tìm kiếm, nhập chính xác: ```node-red-contrib-influxdb```

- Nhấn: Install

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/23382503-9cb7-4ec6-8bc7-1ba8416fd8a4" />

### 2.3.2. Tạo Function chuẩn bị dữ liệu

Cấu hình Function:

- Tại ô ```Name``` nhập: ```Chuẩn bị dữ liệu InfluxDB```

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


### 2.3.3. Tạo node `influxdb out`

**Cấu hình kết nối InfluxDB 2.0**

Điền như sau:

- Name:	InfluxDB Monitor
- Version:	2.0
- URL:	http://influxdb:8086
- Token:	monitor-secret-token-2026-tnut-realtime

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e0daa0c7-4dac-4e9f-86a3-f311e7f94d9f" />

**Điền thông tin cho node influxdb out**

Sau khi thêm server, quay lại cửa sổ cấu hình node output.

Điền:

- Name:	Lưu lịch sử vào InfluxDB
- Measurement:	weather
- Organization:	monitor-org
- Bucket:	weather-history
- Time Precision:	Chọn ms

Nhấn: Done

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/57f96494-b43d-4475-bd25-c4750a96b3f6" />

### 2.3.4. Deploy và chạy thử

Nối các node với nhau:
```
json → function → influxdb out
```

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

## PHẦN 15. Tích hợp Telegram Bot vào Node-RED

Bước 1. Cho container Node-RED đọc file .env

Mở Docker Compose:

```
nano docker-compose.yml
```

Tìm service: nodered:

Thêm phần:

```
    env_file:
      - .env
```
   
<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/a7bd4467-2f31-44b6-94da-29f79e6c1dcf" />

Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

Bước 2. Tạo lại riêng container Node-RED

Chạy:

```
docker compose up -d --force-recreate nodered
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/020a307e-d40c-45e6-b134-ac016443e619" />

Bước 3. Thêm node Function kiểm tra cảnh báo

- Tìm node: function

- Kéo một node Function mới vào dưới các nhánh hiện có.

- Nối: Chuyển JSON thành Object -> Function mới

- Nhấp đúp node Function.

- Đặt tên: Kiểm tra cảnh báo Telegram

- Xóa code mặc định và dán:

```
// Đọc nhiệt độ từ payload
const current = msg.payload.current;

if (!current || current.temperature_2m === undefined) {
    node.error("Không đọc được nhiệt độ để kiểm tra cảnh báo", msg);
    return null;
}

const temperature = Number(current.temperature_2m);
const observedAt = String(current.time).replace("T", " ");

let status = "OK";

if (temperature < 18) {
    status = "ALERT LOW";
} else if (temperature > 35) {
    status = "ALERT HIGH";
}

// Dùng flow context để các node khác cùng đọc được trạng thái
const previousStatus =
    flow.get("telegramPreviousStatus") || "UNKNOWN";

// Trạng thái OK không cần gửi Telegram.
// Tuy nhiên vẫn phải lưu lại để reset hệ thống.
if (status === "OK") {
    flow.set("telegramPreviousStatus", "OK");

    node.status({
        fill: "green",
        shape: "dot",
        text: "OK - không gửi cảnh báo"
    });

    return null;
}

// Không gửi lặp lại cùng một cảnh báo
if (status === previousStatus) {
    node.status({
        fill: "grey",
        shape: "ring",
        text: "Bỏ qua cảnh báo trùng: " + status
    });

    return null;
}

const token = env.get("TELEGRAM_BOT_TOKEN");
const chatId = env.get("TELEGRAM_CHAT_ID");

if (!token || !chatId) {
    node.error("Thiếu TELEGRAM_BOT_TOKEN hoặc TELEGRAM_CHAT_ID", msg);
    return null;
}

// Chuẩn bị request Telegram
msg.method = "POST";
msg.url = `https://api.telegram.org/bot${token}/sendMessage`;

msg.headers = {
    "Content-Type": "application/json"
};

msg.payload = {
    chat_id: chatId,
    text:
        "⚠️ CẢNH BÁO NHIỆT ĐỘ\n" +
        "Khu vực: Thái Nguyên\n" +
        "Giá trị bất thường: " + temperature + " °C\n" +
        "Trạng thái: " + status + "\n" +
        "Thời gian ghi nhận: " + observedAt
};

// Chưa lưu previousStatus tại đây.
// Chỉ lưu sau khi Telegram xác nhận gửi thành công.
msg.alertStatus = status;

node.status({
    fill: "yellow",
    shape: "dot",
    text: "Đang gửi: " + status
});

return msg;
```

- Nhấn: Done

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/fb3737e7-cd08-40fb-b710-99c4b4135947" />


Bước 6. Thêm node HTTP Request gửi Telegram

- Tìm node: http request

- Kéo vào bên phải node:

- Kiểm tra cảnh báo Telegram

- Nối dây: Kiểm tra cảnh báo Telegram -> http request

- Nhấp đúp node HTTP Request và cấu hình:

Name:	Gửi cảnh báo Telegram

Method:	- set by msg.method -

URL:	Để trống 

Return:	a parsed JSON object

- Nhấn: Done

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b7c2e4d5-436f-4b8d-8651-7365f15462e5" />

Bước 7. Thêm node Function xác nhận gửi thành công

- Kéo thêm một node: function

- Nối: Gửi cảnh báo Telegram -> function

- Đặt tên: Xác nhận đã gửi Telegram

Dán code:

```
// Telegram trả về { ok: true, ... } nếu gửi thành công
if (msg.payload && msg.payload.ok === true) {
    flow.set("telegramPreviousStatus", msg.alertStatus);

    node.status({
        fill: "green",
        shape: "dot",
        text: "Đã gửi: " + msg.alertStatus
    });

    return msg;
}

node.warn("Telegram chưa xác nhận gửi thành công");

node.status({
    fill: "red",
    shape: "ring",
    text: "Gửi thất bại - có thể thử lại"
});

return msg;
```

Nhấn: Done

Bước 8. Thêm node Debug kiểm tra kết quả

Tìm node: debug

- Kéo vào bên phải node: Gửi cảnh báo Telegram

- Nối dây: Gửi cảnh báo Telegram -> Xem kết quả Telegram

- Cấu hình Debug:

Name:	Xem kết quả Telegram

Output:	msg.payload

- Nhấn: Done

- Nhấn: Deploy

## PHẦN 16. Tạo node kiểm thử cảnh báo


Bước 1. Tạo node test ALERT HIGH

Kéo một node: inject vào flow.

Nhấp đúp và cấu hình:

- Name:	TEST ALERT HIGH 40°C
msg.payload:	Chọn kiểu JSON

Dán JSON:

```
{
  "current": {
    "temperature_2m": 40,
    "time": "2026-06-09T22:00"
  }
}
```

Nhấn: Done

Nối: TEST ALERT HIGH 40°C -> Kiểm tra cảnh báo Telegram

Bước 2. Tạo node reset về OK

Kéo thêm một node Inject.

Cấu hình:

- Name:	TEST RESET OK 25°C
- msg.payload:	Kiểu JSON

Dán:

```
{
  "current": {
    "temperature_2m": 25,
    "time": "2026-06-09T22:01"
  }
}
```
Nối trực tiếp vào: Kiểm tra cảnh báo Telegram


Bước 3. Tạo node test ALERT LOW

Kéo thêm một node Inject.

Cấu hình:

- Name:	TEST ALERT LOW 10°C
- msg.payload:	Kiểu JSON

Dán:

```
{
  "current": {
    "temperature_2m": 10,
    "time": "2026-06-09T22:02"
  }
}
```

Nối vào: Kiểm tra cảnh báo Telegram

Nhấn: Deploy

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4a49dd3f-3f24-422b-8ac4-dc71a849e90e" />

## PHẦN 17. Kiểm thử Telegram Alert

Thực hiện theo thứ tự:

1. Bấm TEST ALERT HIGH 40°C
2. Kiểm tra nhóm Telegram

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/43715d3a-f67b-4cd7-aa5c-88c5cbb5bb5d" />

3. Bấm TEST RESET OK 25°C
4. Bấm TEST ALERT LOW 10°C
5. Kiểm tra nhóm Telegram lần nữa

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/8805e45a-c202-4fd6-8795-f89a297acaac" />


## GIAI ĐOẠN A. Sao lưu và tạo bộ cài đặt offline

Bước 1. Tạo thư mục lưu file sao lưu

Chạy:
```
mkdir -p backup
```
<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/e160a804-24c0-47a1-a59f-905a8960b1c4" />

Bước 2. Tạo file .env.example

File .env thật đang chứa mật khẩu và token Telegram. Không đưa file đó lên GitHub.

Tạo file mẫu:
```
nano .env.example
```
Dán nội dung:
```
MARIADB_ROOT_PASSWORD=YOUR_ROOT_PASSWORD
MARIADB_DATABASE=monitor_db
MARIADB_USER=monitor_user
MARIADB_PASSWORD=YOUR_DATABASE_PASSWORD

INFLUXDB_USERNAME=admin
INFLUXDB_PASSWORD=YOUR_INFLUXDB_PASSWORD
INFLUXDB_ORG=monitor-org
INFLUXDB_BUCKET=weather-history
INFLUXDB_TOKEN=YOUR_INFLUXDB_TOKEN

GRAFANA_ADMIN_USER=admin
GRAFANA_ADMIN_PASSWORD=YOUR_GRAFANA_PASSWORD

TELEGRAM_BOT_TOKEN=YOUR_TELEGRAM_BOT_TOKEN
TELEGRAM_CHAT_ID=YOUR_TELEGRAM_CHAT_ID
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d113fd21-1bcb-4707-8c9d-3d1e48ff972e" />

Lưu file:

- Ctrl + O
- Enter
- Ctrl + X

Bước 5. Tạo hoặc kiểm tra file .gitignore

Chạy:
```
nano .gitignore
```
Bảo đảm file có tối thiểu các dòng:
```
.env
backup/
```
<img width="1103" height="639" alt="image" src="https://github.com/user-attachments/assets/2ff773d4-e9c6-4c86-9501-8099dbe58a67" />

Ý nghĩa:
- .env	Không đẩy mật khẩu và token thật lên GitHub
- backup/	Không đẩy file image .tar dung lượng lớn lên GitHub

Lưu:

- Ctrl + O
- Enter
- Ctrl + X

Bước 6. Xuất flow Node-RED

Flow Node-RED hiện đang lưu trong Docker volume. Để dễ khôi phục và đưa lên GitHub, nên xuất thêm file JSON.

Mở Node-RED:

http://192.168.1.99:1881

Thực hiện:

☰ → Expor → Current flow → formatted → Download

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/2e4ea97d-059c-4af0-90b2-f9f12fe9c86d" />

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/12c2d8f0-4f23-4a5d-b1b4-eb237b2d63f1" />

Lưu file với tên:
```
flows_app_monitor.json
```
Trên Ubuntu, tạo thư mục để sau này đưa file vào repo:
```
mkdir -p node-red
```

Bước 7. Sao lưu source code

Chạy:
```
tar \
  --exclude='./.env' \
  --exclude='./backup' \
  -czf backup/app-monitor-source.tar.gz \
  .
```
Lệnh này nén source code dự án nhưng không đưa file .env thật vào gói nén.

Kiểm tra:
```
ls -lh backup
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/01586abb-a0cb-46cf-ade5-434eb634c0d6" />

Thấy: ```app-monitor-source.tar.gz```

Bước 8. Xem chính xác tên các Docker image

Chạy:
```
docker compose images
```
<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/2a1ba935-8285-46d3-a03b-3b4da1a88479" />

Bước 9. Xuất toàn bộ image ra file .tar

chạy:
```
docker image save \
  -o backup/app-monitor-images.tar \
  nodered/node-red:latest \
  mariadb:11.4 \
  influxdb:2.8.0 \
  grafana/grafana:12.4 \
  nginx:alpine \
  app-monitor-realtime-api:latest
```
Lệnh có thể chạy trong vài phút vì file khá lớn.

Kiểm tra:
```
ls -lh backup

Kết quả cần có hai file:
```
app-monitor-source.tar.gz
app-monitor-images.tar

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/a8d9d5dc-9af8-45f9-b3ce-cd450bdfd614" />

## GIAI ĐOẠN B. Sao lưu volume trước khi xóa container

Bước 10. Kiểm tra các volume của dự án

Chạy:
```
docker volume ls | grep app-monitor-realtime
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/ee156c34-2323-4858-8698-b941a30fd18f" />

Bước 11. Sao lưu từng volume

Chạy lần lượt:
```
docker run --rm \
  -v app-monitor-realtime_nodered_data:/data \
  -v "$(pwd)/backup":/backup \
  alpine \
  tar -czf /backup/nodered_data.tar.gz -C /data .
```
```
docker run --rm \
  -v app-monitor-realtime_mariadb_data:/data \
  -v "$(pwd)/backup":/backup \
  alpine \
  tar -czf /backup/mariadb_data.tar.gz -C /data .
```
```
docker run --rm \
  -v app-monitor-realtime_influxdb_data:/data \
  -v "$(pwd)/backup":/backup \
  alpine \
  tar -czf /backup/influxdb_data.tar.gz -C /data .
```
```
docker run --rm \
  -v app-monitor-realtime_influxdb_config:/data \
  -v "$(pwd)/backup":/backup \
  alpine \
  tar -czf /backup/influxdb_config.tar.gz -C /data .
```
```
docker run --rm \
  -v app-monitor-realtime_grafana_data:/data \
  -v "$(pwd)/backup":/backup \
  alpine \
  tar -czf /backup/grafana_data.tar.gz -C /data .
```

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/7ce1bea4-4158-419f-92b0-1943e2aa8397" />

Kiểm tra:
```
ls -lh backup
```
Cần thấy thêm:

nodered_data.tar.gz
mariadb_data.tar.gz
influxdb_data.tar.gz
influxdb_config.tar.gz
grafana_data.tar.gz

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/e2e5a0c2-7ef3-4702-8579-7b8613d598df" />

## GIAI ĐOẠN D. Xóa các container của dự án
Bước 1. Xóa container và network của riêng dự án

Chạy:
```
docker compose down
```

Bước 2. Kiểm tra kết quả

Chạy:
```
docker compose ps
```
<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/3940718c-871a-41e9-ac8d-8a6d23e239f0" />


Kiểm tra volume vẫn còn:
```
docker volume ls | grep app-monitor-realtime
```
<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/36b8f394-8883-41f0-b637-e24f99d2225d" />

## GIAI ĐOẠN E. Nạp lại Docker image từ file nén
Bước 1. Load image

Chạy:
```
docker image load -i backup/app-monitor-images.tar
```
Lệnh có thể mất một vài phút.

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/0b9b8b72-0880-4327-bd13-025534ef96ca" />


Bước 2. Kiểm tra danh sách image

Chạy:
```
docker image ls
```
<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/4f1fcd7a-cb42-44d4-9bb5-2b0d51d213cc" />

## GIAI ĐOẠN F. Khôi phục container bằng Docker Compose
Bước 1. Khởi động lại toàn bộ ứng dụng

Chạy:
```
docker compose up -d
```
Docker Compose sẽ dùng file: ```docker-compose.yml``` để tạo lại sáu container.

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/0c8a45cc-2dbf-467c-a096-0de55b0c0bf1" />

Bước 2. Kiểm tra container

Chờ khoảng 15–30 giây rồi chạy:
```
docker compose ps
```
Kết quả cần có:

- monitor_api
- monitor_grafana
- monitor_influxdb
- monitor_mariadb
- monitor_nginx
- monitor_nodered

Trạng thái cần là: Up

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/c02ce3e8-9ec0-4e65-8a24-217939df08ab" />

## GIAI ĐOẠN G. Kiểm tra hệ thống sau khôi phục
1. Kiểm tra API Flask

Chạy:
```
curl http://localhost:5001/health
```

Kết quả:

{"service":"monitor_api","status":"ok"}

Tiếp tục:
```
curl http://localhost:5001/api/latest
```
Kết quả gần giống:

{
  "city": "Thai Nguyen",
  "id": 1,
  "observed_at": "2026-06-10 09:45:00",
  "status": "OK",
  "temperature": 25.0
}
2. Kiểm tra Nginx reverse proxy

Chạy:
```
curl http://localhost:8082/api/latest
```
Kết quả cần giống API Flask.

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/c2bfd5a3-b100-4ee4-9b11-85bdc11e1687" />

3. Kiểm tra website

Mở trình duyệt:
```
http://192.168.1.99:8082
```
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/52953933-8ab6-4171-b8d3-5efa0025b9c4" />

4. Kiểm tra Node-RED

Mở:
```
http://192.168.1.99:1881
```
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6c36be70-ffa2-4d51-a262-2123e56e042b" />

Flow cũ cần vẫn còn đầy đủ, vì volume:

app-monitor-realtime_nodered_data

không bị xóa.

5. Kiểm tra Grafana

Mở:
```
http://192.168.1.99:3000
```
Dashboard:

Weather Monitor

vẫn cần tồn tại.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/409b2758-b894-4287-afb2-41bbbd2e9ce2" />

6. Kiểm tra lịch sử InfluxDB

Mở:

http://192.168.1.99:8086

Vào:

Data Explorer
→ weather-history
→ weather
→ temperature

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d1bfaf28-9119-4d59-9af4-00cb53634956" />


Biểu đồ lịch sử cũ vẫn cần hiển thị.

7. Kiểm thử Telegram

Trong Node-RED, bấm:

TEST RESET OK 25°C

sau đó:

TEST ALERT HIGH 40°C

Nhóm Telegram cần nhận được cảnh báo mới.

<img width="1980" height="1080" alt="image" src="https://github.com/user-attachments/assets/a72ccf90-0aa7-43d9-96c6-a8fb307f141e" />











































































