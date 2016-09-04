#Chia sẻ file từ từ Window cho Linux
##1. Mục tiêu:
Chia sẻ file từ máy Window cho máy Linux

##2. Chuẩn bị
- 1 Máy Window 7 có nhiệm vụ chia sẻ file (IP: 172.16.69.100)
- 1 Máy Ubuntu 14.04 (IP: 172.16.69.50)

##3. Thực hiện:
###Trên máy Window
- Chọn Open Network and Sharing Center > Change advanced sharing settings, sau đó "turn on network discovery" và "Turn on file and printer sharing " rồi Save Changes.
<img src="http://i.imgur.com/uQrBVbD.png">
- Tạo file và share file đó:
Tôi sẽ tạo 1 file trên máy Window 7 có tên là `tri` và tạo 1 file `tri.txt` trong file `tri` rồi Share file đó.
<img src="http://i.imgur.com/QW9UtzZ.png">

- Phân quyền cho người dùng được phép truy cập xem và sửa
<img src="http://i.imgur.com/tT48jMO.png">
Sau đó hoàn tất chia sẻ file.

###Trên máy Ubuntu 14.04
- Cài đặt gói `ciff-utils`
```sh
apt-get install cifs-utils
```
- Tạo 1 thư mục để mount file từ Window về
```sh
mkdir /home/<user_name>/<folder_name>
```
Ví dụ tôi sẽ tạo 1 file như sau:
```sh
mkdir /home/quoctrimai/share
```
- Mount thư mục từ máy Window về:
```sh
mount.cifs //<name_WinPC>/<folder_share> /home/<user_name>/<folder_name> -o user=<user_name_WinPC>
```
Ví dụ tôi sẽ mount thư mục từ máy Window về máy Linux như sau:
```sh
mount.cifs //172.16.69.100/tri /home/quoctrimai/share-o user=Administrator
```
- Sau đó nhập mật khẩu cho người dùng
- Kiểm tra kết quả 
```sh
root@smb:~# cd /home/quoctrimai/share/
root@smb:/home/quoctrimai/share# ls
tri.txt
```
Như vậy là tôi đã mount thành công thư mục `tri` trên máy Window về

