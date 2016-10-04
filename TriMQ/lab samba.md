#Lab giao thức SMB
##1. Mục tiêu:
Chia sẻ file từ máy Linux cho máy Window
##2. Chuẩn bị:
- 1 Máy Ubuntu server 14.04, IP:172.16.69.50
- 1 Máy Window 7

##3. Thực hiện

####Trên máy Ubuntu server 14.04
- Tải gói `samba`:
```sh
apt-get install samba -y
```
- Tạo tài khoản sử dụng dịch vụ samba:
```sh
smbpasswd -a <user_name>
```
- Tạo 1 thư mục để chia sẻ file:
```sh
mkdir /home/<user_name>/<folder_name>/<file_name>
```
Trong đó:
<ul>
<li>user_name: là tài khoản mà máy window sử dụng để truy cập vào máy Linux</li>
<li>folder_name: là thư mục mà máy Linux chia sẻ</li>
<li>folder_name: là tên file mà máy Linux chia sẻ</li>
</ul>
Ví dụ ở đây tôi sẽ tạo thư mục như sau:
```sh
mkdir /home/quoctrimai/tri/tri.txt
```
- Sửa file `smb.conf`
```sh
vi /etc/samba/smb.conf
```
Thêm vào những thông tin như sau
```sh
[Share]
path = /home/quoctrimai/share
available = yes
valid users = quoctrimai
read only = no
browsable = yes
public = yes
writable = yes
```
Trong đó:
<ul>
<li>path: tên người dùng và thư mục chia sẻ</li>
<li>valid user: tên người dùng</li>
<li>read only: Chế độ chỉ được đọc</li>
<li>writable: Được phép chỉnh sửa file</li>
</ul>
- Khởi động lại dịch vụ `samba`:
```sh
service smbd restart
```
####Trên máy Window:
- Chọn `run` sau đó nhập vào địa chỉ
```sh
\\IP address\<folder_name>
```
Trong đó
<ul>
<li>IP address: là địa chỉ của máy Ubuntu</li>
<li>folder_name: là thư mục chia sẻ</li>
</ul>
- Kết quả:
Trên máy Ubuntu server tôi sẽ tạo 1 file `vdc` với nội dung
```sh
vnpt cloud
```
Kiểm tra trên máy Window:
<img src="http://i.imgur.com/vFoEyLj.png">








