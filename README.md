# 2.1. lý thuyết: <br>
  1. Docker là gì?<br>
- Docker là một nền tảng mã nguồn mở cho phép tự động hóa quy trình đóng gói, phân phối và triển khai ứng dụng. Thay vì phân phối mã nguồn thô, Docker đóng gói toàn bộ ứng dụng cùng tất cả các thành phần phụ thuộc đi kèm như thư viện, môi trường thực thi, công cụ hệ thống và các tệp cấu hình vào một đơn vị duy nhất gọi là Container.<br>
- Về mặt bản chất kỹ thuật, Docker sử dụng công nghệ ảo hóa ở cấp độ hệ điều hành. Khác với các máy ảo truyền thống vốn đòi hỏi phải giả lập toàn bộ phần cứng và chạy một hệ điều hành khách độc lập, các Docker Container chia sẻ chung nhân hệ điều hành của máy chủ vật lý. Nhờ cơ chế này, các ứng dụng chạy bên trong container được cô lập hoàn toàn về mặt tài nguyên nhưng lại có tốc độ khởi động gần như tức thì và tiêu tốn cực kỳ ít tài nguyên RAM hay CPU.<br>
2. Các từ khóa phổ biến trong file docker-compose.yml<br>
Tệp tin cấu hình docker-compose.yml được viết theo định dạng YAML, có nhiệm vụ định nghĩa và quản lý một hệ thống phức tạp gồm nhiều container liên thông với nhau. Các từ khóa cốt lõi trong tệp tin này bao gồm:<br>
- Nhóm từ khóa cấu trúc tổng thể<br>
+ Từ khóa version dùng để chỉ định phiên bản cú pháp cấu trúc của Docker Compose được áp dụng trong tệp tin.<br>

+ Từ khóa services đóng vai trò là vùng khai báo bắt buộc, dùng để bắt đầu định nghĩa danh sách các container thành phần cấu tạo nên hệ thống.
<br>
+ Từ khóa networks dùng để thiết lập các cấu trúc mạng nội bộ biệt lập, cho phép các container trong cùng hệ thống có thể nhìn thấy và truyền nhận dữ liệu bảo mật với nhau.<br>

+ Từ khóa volumes dùng để định nghĩa các vùng lưu trữ dữ liệu bền vững do Docker quản lý, giúp bảo toàn dữ liệu ngay cả khi container bị xóa bỏ.<br>

- Nhóm từ khóa mô tả một Service hoặc một Container cụ thể<br>
+ Từ khóa image có ý nghĩa chỉ định bộ cài mẫu được tải về từ Docker Hub hoặc kho lưu trữ để làm phôi dựng nên container. Ví dụ minh họa: image: mariadb:latest nghĩa là hệ thống sẽ dùng bản cài mới nhất của MariaDB làm nền tảng.<br>

+ Từ khóa container_name có ý nghĩa đặt một cái tên tường minh và cố định cho container khi khởi chạy, giúp người quản trị dễ dàng quản lý thay vì để Docker tự sinh tên ngẫu nhiên. Ví dụ minh họa: container_name: flask_api_service.<br>

+ Từ khóa ports dùng để thực hiện kỹ thuật ánh xạ cổng giao tiếp từ máy vật lý chủ vào bên trong container theo quy tắc cổng máy thật đứng trước, cổng container đứng sau. Ví dụ minh họa: ports: ["5000:5000"] nghĩa là khi người dùng truy cập vào cổng 5000 của máy thật, tín hiệu sẽ được điều hướng thẳng vào cổng 5000 của dịch vụ bên trong container.<br>

+ Từ khóa environment dùng để thiết lập và truyền các biến môi trường cấu hình ngầm vào bên trong container khi khởi chạy, thường ứng dụng để đặt tài khoản hoặc mật khẩu. Ví dụ minh họa:<br>

```YAML
environment:
  - MYSQL_ROOT_PASSWORD=rootpassword
```
Từ khóa volumes dùng để liên kết một thư mục trên máy thật hoặc một ổ đĩa ảo của Docker vào một đường dẫn bên trong container nhằm lưu trữ dữ liệu hoặc đồng bộ mã nguồn theo thời gian thực. Ví dụ minh họa:<br>

```YAML
volumes:
  - ./web_frontend:/usr/share/nginx/html
```
Từ khóa networks dùng để chỉ định cụ thể container này sẽ gia nhập vào mạng nội bộ nào để giao tiếp với các dịch vụ đồng cấp. Ví dụ minh họa:<br>

```YAML
networks:
  - ten_network
```
Từ khóa depends_on dùng để thiết lập sơ đồ phân cấp và thứ tự khởi động giữa các container. Container chứa từ khóa này sẽ phải đợi container được chỉ định bật lên trước thì nó mới kích hoạt theo. Ví dụ minh họa:<br>

```YAML
depends_on:
  - mariadb
```
Từ khóa restart dùng để quy định chính sách tự động khởi động lại của container khi gặp sự cố đột ngột hoặc khi máy chủ vật lý được khởi động lại. Ví dụ minh họa: restart: always.<br>

Ví dụ minh họa một file docker-compose.yml tổng hợp:<br>
```YAML
version: '3.8'

services:
  mariadb:
    image: mariadb:latest
    container_name: ten_db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: ten_data
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - ten_network

  flask_api:
    image: python:3.9-slim
    container_name: flask_api_service
    ports:
      - "5000:5000"
    depends_on:
      - mariadb
    networks:
      - ten_network

volumes:
  db_data:

networks:
  ten_network:
    driver: bridg
```
3. Ưu điểm khi triển khai ứng dụng sử dụng Docker<br>
- Giải quyết triệt để sự xung đột môi trường: Docker giúp xóa bỏ hoàn toàn lỗi kinh điển mang tên "chạy trên máy của lập trình viên thì được nhưng lên máy chủ trường hoặc doanh nghiệp lại lỗi". Môi trường bên trong container đã được khóa cứng, đảm bảo tính nhất quán tuyệt đối khi di chuyển qua các máy tính khác nhau.<br>

- Tốc độ triển khai cực nhanh: Thay vì mất nhiều giờ đồng hồ cài đặt thủ công từng thư viện Python, cài đặt cấu hình MariaDB hay Nginx trên máy chủ mới, người quản trị chỉ cần chạy duy nhất một câu lệnh khởi động của Docker Compose. Hệ thống sẽ tự động thiết lập toàn bộ các dịch vụ phức tạp chỉ trong vài chục giây.<br>

- Tối ưu hóa tài nguyên phần cứng: Do không phải gánh thêm một hệ điều hành khách nặng nề như công nghệ máy ảo truyền thống, Docker Container tiêu tốn rất ít RAM và dung lượng ổ cứng. Điều này cho phép một máy tính có cấu hình tầm trung vẫn có thể vận hành ổn định cùng lúc từ 5 đến 7 dịch vụ độc lập mà không bị giật lag.
<br>
- Tính cô lập và bảo mật cao: Mỗi dịch vụ phần mềm được vận hành trong một không gian khép kín riêng. Nếu chẳng may một container như Flask API gặp lỗi nghiêm trọng hoặc bị tấn công sập luồng, nó hoàn toàn không thể ảnh hưởng ngầm hay làm rò rỉ dữ liệu của container MariaDB hay Node-RED chạy song song bên cạnh.<br>

4. Quy trình triển khai ứng dụng sang máy chủ thật không có Internet<br>
- Khi đối mặt với môi trường máy chủ bảo mật hoặc máy chủ phòng thí nghiệm không có kết nối internet, quy trình triển khai sẽ được thực hiện theo phương thức "đóng gói ngoại vi cục bộ" qua 4 bước cơ bản sau:<br>

- Bước thứ nhất: Gom và xuất toàn bộ Docker Images ra file nén trên máy cá nhân. Tại chiếc laptop có kết nối internet, sau khi đã lập trình và chạy thử nghiệm hệ thống ổn định, bạn sử dụng lệnh docker save để đóng gói toàn bộ các bộ cài (Images) có trong file cấu hình thành một tệp tin lưu trữ dạng .tar duy nhất:<br>

```Bash
docker save -o my_app_images.tar mariadb:latest python:3.9-slim nginx:latest
```
Bước thứ hai: Đóng gói toàn bộ mã nguồn và cấu hình hệ thống. Bạn tiến hành nén thư mục dự án hiện hành — bao gồm tệp tin cấu hình docker-compose.yml, các mã nguồn của API Flask, mã nguồn giao diện HTML/JavaScript của Frontend — thành một tệp nén gọn nhẹ dạng .tar.gz:<br>

```Bash
tar -czvf my_app_source.tar.gz ./thumuc_duan
```
Bước thứ ba: Chuyển các file nén sang máy chủ offline bằng thiết bị lưu trữ vật lý. Bạn sử dụng các thiết bị ngoại vi như USB, ổ cứng di động, hoặc kết nối mạng LAN nội bộ để sao chép hai tệp tin vừa đóng gói ở bước một và bước hai sang bộ nhớ cục bộ của máy chủ thật. Điều kiện tiên quyết là máy chủ này đã được cài đặt sẵn công cụ Docker Engine từ trước.<br>

Bước thứ tư: Giải nén và kích hoạt hệ thống tại máy chủ thật. Tại giao diện Terminal của máy chủ thật, bạn thực hiện lệnh docker load để nạp tệp tin hình ảnh trực tiếp vào bộ nhớ lưu trữ cục bộ của Docker:<br>

```Bash
docker load -i my_app_images.tar
```
Sau đó, bạn giải nén tệp chứa mã nguồn, di chuyển vào thư mục dự án và chạy lệnh khởi động:<br>

```Bash
tar -xzvf my_app_source.tar.gz
cd thumuc_duan
docker compose up -d
```
2.2. Thực hành áp dụng: APP MONITOR + ALERT DATA REALTIME<br>
Bước 1: Tạo các thư mục dự án<br>
Chạy lệnh tạo thư mục gốc và các thư mục con chứa mã nguồn:<br>

```Bash
mkdir -p petrol_system/flask_api petrol_system/web_frontend
cd petrol_system
```
<img width="743" height="53" alt="image" src="https://github.com/user-attachments/assets/b8e4398c-5b83-4157-be69-6a59f7723d4f" /><br>

  Bước 2: Tạo file Backend Flask API<br>
Chạy lệnh mở trình soạn thảo văn bản để tạo file code Flask:<br>

```Bash
nano flask_api/app.py
```
<img width="1106" height="620" alt="image" src="https://github.com/user-attachments/assets/8b5911ea-1973-4cd7-837a-33f264553fc6" /><br>
Bước 3: Tạo file Giao diện Frontend Nginx<br>
Chạy lệnh mở trình soạn thảo văn bản để tạo file giao diện:<br>

```Bash
nano web_frontend/index.html
```
<img width="1105" height="614" alt="image" src="https://github.com/user-attachments/assets/bfdc02ef-5578-4584-9b34-e28ec798e0cc" /><br>
Bước 4: Tạo file cấu hình dịch vụ docker-compose.yml<br>
Chạy lệnh tạo file cấu hình tổng ở ngay thư mục gốc petrol_system:<br>

```Bash
nano docker-compose.yml
```
<img width="1104" height="607" alt="image" src="https://github.com/user-attachments/assets/a80499cc-d20b-498c-8d6f-ae2a64ca8c31" /><br>
Khởi động kiểm tra hệ thống<br>
```Bash
docker compose up -d
```
<img width="1643" height="253" alt="image" src="https://github.com/user-attachments/assets/f285bcf1-9d8c-47d3-a63d-6e6d3ccf24ad" /><br>
BƯỚC 5: Dùng lệnh cli để tạo bảng chứa dữ liệu giá xăng dầu vào mariadb:<br>
```Bash
docker exec -i petrol_db mysql -uroot -prootpassword petrol_vng_db -e "
CREATE TABLE IF NOT EXISTS petrol_table (
    id INT AUTO_INCREMENT PRIMARY KEY,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    ron95_vung1 INT NOT NULL,
    ron95_vung2 INT NOT NULL,
    e5_vung1 INT NOT NULL,
    e5_vung2 INT NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
"
```
BƯỚC 6: Dùng lệnh cli để tạo bảng chứa dữ liệu giá xăng dầu vào influxdb:<br>
```Bash
docker exec -i petrol_influx influx -execute "CREATE DATABASE petrol_history"
```
BƯỚC 7 : Kết nối Grafana tới Influxdb: <br>
- Truy cập grafana, đăng nhập và thêm data source mới:<br>
  <img width="1866" height="688" alt="image" src="https://github.com/user-attachments/assets/c59f36f6-f42a-4b81-befa-a6a9149b775c" /><br>
<img width="1853" height="694" alt="image" src="https://github.com/user-attachments/assets/18fe913e-bd0a-41c8-a4d2-1d93ba418ced" /><br>
<img width="1033" height="706" alt="image" src="https://github.com/user-attachments/assets/43b310e8-5cdb-422c-9d1a-c43415ae7728" /><br>
   BƯỚC 8:   Cấu hình lại file giao diện index.html:<br>
<img width="1842" height="1004" alt="image" src="https://github.com/user-attachments/assets/38b707d0-c85f-445c-ab48-2a1b2bf2b1c2" /><br>
  BƯỚC 9: Cấu hình nodered để lấy api giá xăng đưa vào các database và hiển thị trên web:<br>
<img width="1108" height="430" alt="image" src="https://github.com/user-attachments/assets/3dcf84c4-cf04-4eb8-b319-f390924b9887" /><br>

---> Kết quả: <br>
<img width="1763" height="916" alt="image" src="https://github.com/user-attachments/assets/27a15c4c-b4d5-4804-87ab-77b0f4146e58" /><br>
<img width="1419" height="934" alt="image" src="https://github.com/user-attachments/assets/ae976a5d-3ef0-4ec1-8190-26b701034f54" /><br>

1. Xuất (Export) tất cả các Image ra file nén<br>
Thay vì xuất từng container, hãy xuất tất cả các images mà bạn đang dùng để đảm bảo bạn có thể khôi phục lại cấu hình ban đầu:<br>

```Bash

docker save $(docker images -q) -o backup_images.tar
```
2. Xóa mọi Container đang chạy<br>
Lệnh này sẽ dừng và xóa sạch tất cả các container trên hệ thống của bạn:<br>

```Bash
docker stop $(docker ps -aq)
docker rm $(docker ps -aq)
```
3. Khôi phục (Load) lại từ file nén<br>
Sau khi đã xóa, bạn load lại các images từ file nén đã lưu:<br>

```Bash
docker load -i backup_images.tar+
docker compose up -d
```
