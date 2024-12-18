# Báo cáo: Các bước triển khai trang web `ktpm.nguyengocquoctrung.id.vn`

## **1. Giới thiệu**
Trang web `ktpm.nguyengocquoctrung.id.vn` được triển khai với mục đích MỤC ĐÍCH TRIỂN KHAI, vì vậy trang web có thể có các chức năng chưa hoàn thiện, hoặc code sẽ không được tối ưu. Báo cáo này mô tả chi tiết các bước thực hiện triển khai, cấu hình, test và kiểm tra hệ thống.

---

## **2. Mô tả hệ thống**
### **2.1. Công nghệ sử dụng**
- **Hệ điều hành**: [Ubuntu 20.04]
- **Web server**: [Nginx]
- **Ngôn ngữ lập trình**: [Backend: .NET, Frontend: React.js]
- **Quản lý mã nguồn**: Git
- **Database**: SQL server 
### **2.2. Cấu hình server**
| Thông số          | Giá trị                |
|-------------------|------------------------|
| Domain           | `ktpm.nguyengocquoctrung.id.vn` |
| Địa chỉ IP        | `35.232.88.45`        |
| Thư mục root     | `/var/www/html/`        |

---

## **3. Các bước triển khai**

### **3.1 Máy chủ/VPS:**
   - Có thể dùng Google Cloud, vì sẽ cho sử dụng 300$ credit, thích hợp làm lab
   - Có thể cấu hình tùy theo nhu cầu
   - (Recommend) Tạo 2 servers bao gồm development và database để dễ dàng trực quan
   - Vào firewall mở port 80, 443 
   - **Lưu ý** Nên reserve ip nếu không lần sau vào ip sẽ tự đổi thành ip khác

### **3.2 Domain và DNS**
   - Có thể mua domain hoặc lựa chọn DNS tùy theo ý muốn, ở đây mình chọn bên INET, tạo doamin là ktpm.nguyengocquoctrung.id.vn
   - Tạo bản ghi trỏ tới public ip của server development

### **3.3. Chuẩn bị môi trường**
1. Cài đặt các công cụ cần thiết:
   ```bash
   sudo apt update
   sudo apt install -y nginx nodejs npm 
   ```


### **3.4. Lấy mã nguồn**
1. Clone mã nguồn từ repository gốc:
   ```
   git clone https://github.com/NguyenNgocQuocTrung/Web
   ```
2. Kiểm tra mã nguồn và cấu trúc thư mục:
   ```
   Kiểm tra bao gồm 2 folder gồm backend và frontend, dữ liệu test được đặt trong 2 file .table 
   ```
3. Chạy thử dữ án:
   - Run .net và react
   - Có thể kiểm tra api thông qua ip:5214//swagger/index.html
   - Kiểm tra frontend thông qua port 3000 (default của react)

### **3.5. Cấu hình Web Server**
1. Tạo file cấu hình Nginx:
   ```
   sudo nano /etc/nginx/sites-available/myproject
   ```
2. Nội dung file cấu hình:
   ```
      server {
      server_name ktpm.nguyenngocquoctrung.id.vn;

      root /var/www/html/;
      index index.html loaderio-417307441a8d400389437380e5957e8b.html;

      location / {
        proxy_pass http://35.232.88.45:3000;  # Reverse proxy đến cổng 3000
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
         }
      location /api/ {
        proxy_pass http://35.232.88.45:5214;  # Reverse proxy đến backend
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
                  }
      location = /loaderio-417307441a8d400389437380e5957e8b.html {
        root /var/www/html;
               }
         }
   ```

   ** loaderio là file .html dùng để test performance bên loader.io **

3. Kích hoạt cấu hình:
   ```
   sudo ln -s /etc/nginx/sites-available/myproject /etc/nginx/sites-enabled/myproject
   sudo systemctl restart nginx
   ```

### **3.6. Cài đặt SSL bằng certbot**
   ```
   sudo apt install certbot python3-certbot-nginx
   sudo certbot --nginx -d ktpm.nguyengocquoctrung.id.vn
   ```
   **Có thể chọn redirect để khi truy cập vào port 80 http vẫn tự truy cập vào port 443 là https**

### **3.5. Kiểm tra hoạt động**
1. Truy cập trang web tại: [http://ktpm.nguyengocquoctrung.id.vn](http://ktpm.nguyengocquoctrung.id.vn) hoặc [https://ktpm.nguyengocquoctrung.id.vn](https://ktpm.nguyengocquoctrung.id.vn).
2. Kiểm tra log Nginx nếu xảy ra lỗi:
   ```bash
   sudo tail -f /var/log/nginx/error.log
   ```

---

## **4. Kết luận**
Triển khai trang web `ktpm.nguyengocquoctrung.id.vn` đã hoàn thành với các bước như trên. Trang web hoạt động ổn định, đáp ứng các yêu cầu đặt ra ban đầu. Folder tests dùng để test trang web, có thể sử dụng nếu muốn

---

## **5. Tài liệu tham khảo**
- [Hướng dẫn sử dụng Nginx](https://nginx.org/en/docs/)
- [Cách sử dụng Git](https://git-scm.com/doc)
- [Tài liệu React.js](https://reactjs.org/docs/getting-started.html)
- [Tài liệu .NET](https://learn.microsoft.com/vi-vn/dotnet/framework/)
- [Cài đặt certbot](https://www.inmotionhosting.com/support/website/ssl/lets-encrypt-ssl-ubuntu-with-certbot/)