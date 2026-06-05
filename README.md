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


