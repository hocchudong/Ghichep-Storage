#Sử dụng công cụ tar để backup dữ liệu

#Mục lục
**Table of Content**

- [1. Các dạng Backup](#1)
	- [1.1 Full Backup](#1.1)
	- [1.2 Incremental Backup](#1.2)
	- [1.3 Diffrential Backup](#1.3)

- [2. Sử dụng công cụ tar để back up](#2)
	- [2.1 Tạo archive để lưu trữ tất cả các file cần backup trong 1 thư mục](#2.1)
	- [2.2 Di chuyển file archive sang 1 server khác](#2.2)
	- [2.3 Backup Database](#2.3)
	- [2.4 Incremental Backup](#2.4)
	
- [3. Tài liệu tham khảo](#3)
	
========================================================

<a name="1"></a>
##1. Các dạng Backup:

Ngày nay, backup là phương thức hữu hiệu nhất để bảo vệ dữ liệu trước những rủi ro và sự phá hoại. Nhưng việc backup cũng cần có những kiến thức tổng quan để chọn ra cách backup dữ liệu vừa an toàn vừa tiết kiệm được tài nguyên lưu trữ. Bài viết này sẽ giới thiệu về 3 dạng Backup là Full backup, Incremental backup, Diffrential backup.

<a name="1.1">
###1.1 Full Backup:
Full Backup là backup toàn bộ nguyên khối dữ liệu đang có.

- Ưu điểm:
<ul>
<li>Dễ dàng phục hồi lại dữ liệu. Khi cần phục hồi lại thì sẽ phục hồi lại toàn bộ dữ liệu của ngày Backup Full.</li>
<li>Tính an toàn cao cho dữ liệu</li>
</ul>

-Nhược điểm:
<ul>
<li>Thời gian backup lâu. Dữ liệu càng nhiều thì thời gian backup càng lâu</li>
<li>Tốn dung lượng lưu trữ</li>
<li>Chi phí đầu tư thiết bị lưu trữ lớn.</li>
</ul>

<a name="1.2"></a>
###1.2 Incremental Backup:
Là Backup những gì thay đổi so với bản backup gần nhất

<img src="http://www.techsupportalert.com/files/images/pc_freeware/disk_and_file_utilities/incrementalbackup.jpg">

- Ưu điểm:
<ul>
<li>Thời gian backup nhanh nhất</li>
<li>Dung lượng backup bé nhất</li>
</ul>

-Nhược điểm:
<ul>
<li>Khi cần khôi phục lại phải cần có đủ tất cả các bản backup, nếu 1 bản bị hỏng thì dữ liệu đó cũng sẽ mất</li>
<li>Thời gian Restore lâu hơn so với Differential Backup</li>
</ul>

<a name="1.3">
##1.3 Differential Backup:
Differential Backup là backup những gì thay đổi so với lần Full Backup gần nhất

<img src="http://www.techsupportalert.com/files/images/pc_freeware/disk_and_file_utilities/differentialbackup.jpg">

-Ưu điểm:
<ul>
<li>Thời gian backup nhanh hơn</li>
<li>Dung lượng backup nhỏ hơn so với Full Backup. Tiết kiệm dung lượng lưu trữ</li>
<li>Tốc độ phục hồi dữ liệu sẽ nhanh hơn so với Incremental Backup</li>
</ul>

-Nhược điểm:
<ul>
<li>Khi cần khôi phục dự liệu cần có 2 bản backup, 1 file full backup gần nhất và 1 file differential backup</li>
</ul>

<a name="2"></a>
##2. Sử dụng công cụ tar để backup

####Chuẩn bị 2 máy Ubuntu 14.04
- Server 1 IP: 172.16.69.100
- Server 2 IP: 172.16.69.99


<a name="2.1"></a>
###2.1 Tạo archive để lưu trữ tất cả các file cần backup trong 1 thư mục

####Trên Server 1:

- Tạo 1 thư mục để chứa các file backup;
```sh
mkdir /backup
```
Đây là thư mục chứa các archive dùng để backup full

- Sử dụng tar để đóng gói toàn bộ file cần backup
```sh
tar -czf /backup/initial_backup.tar.gz -P /home/quoctrimai
```
Trong đó:
<li><b>tar -czf</b>: là lệnh để nén file</li>
<li><b>initial_backup.tar.gz</b>: là tên file được nén</li>
<li><b>/home/quoctrimai</b>: là thư mục được backup</li>

- Sử dụng công cụ tar để thêm ngày mà backup được thực hiện:
```sh
tar -czf /backup/-`date '+%m%d%y'`.tar.gz -P /home/quoctrimai
```
- Xem các file full backup vừa tạo;
```sh
ls -l /backup
```
```sh
OUTPUT
-rw-r--r-- 1 root root 2432 Sep 30 09:17 -093016.tar.gz
-rw-r--r-- 1 root root 2430 Sep 30 09:12 initial_backup.tar.gz
```
Ta sẽ thấy 2 file tar

- Chỉnh file crontab để lên lịch backup cho dữ liệu
```sh
EDITOR=nano crontab -e
```
Thêm lịch làm việc
```sh
30 3 * * * /bin/tar -czf /backup/-`date +\%m\%d\%y`.tar.gz -P /home/quoctrimai
```
Trong đó: 30 3 thời gian thực hiện backup là 3h30p

<a name="2.2"></a>
###2.2 Di chuyển archive sang 1 server khác:

- Sử dụng `scp` để di chuyển archive sang server khác:
```sh
scp -r -P22 /backup/initial_backup.tar.gz root@172.16.69.99: /home/quoctrimai/
```
Trong đó
<li><b>scp</b>: là lệnh để chuyển archive sang server khác</li>
<li><b>/home/quoctrimai</b>: Là thư mục chứa file backup tại node 2</li>
<li><b>root@172.16.69.99</b>: Là địa chỉ IP của node 2</li>

<a name="2.3"></a>
###2.3 Backup Database

- Tạo archive chứa file backup database:
```sh
mysqldump < mysql -u username -pPassword | gzip > /backup/initial.sql.gz
```
Trong đó:
<li><b>mysqldump</b> Là lệnh backup dữ liệu</li>
<li><b>mysql</b>: là tên Database</li>
```sh
OUTPUT
-rw-r--r-- 1 root root  130 Aug 16 21:57 initial.sql.gz
```

- Kiểm tra trên Node 2
```sh
OUTPUT
root@client:/home/quoctrimai# ls -l
total 8
-rw-r--r-- 1 root root 2394 Sep 29 16:38 initial_backup.tar.gz
-rw-r--r-- 1 root root  130 Sep 29 16:53 initial.sql.gz
```

<a name="2.4"></a>
###2.4 Incremental Backup

- Để tạo Incremental Backup sử dụng lệnh `tar`:
```sh
tar --create --file=archive.1.tar --listed-incremental=/var/log/urs.snar-1 -P /home/quoctrimai/
```
Là bản backup level 0
- Trong đó:
<ul>
<li><b>file=archive.1.tar</b>: Là Archive được tạo ra</li>
<li><b>/var/log/urs.snar-1</b>: Là nơi chứa Archive</li>
<li><b>/home/quoctrimai/</b>: Là thư mục backup</li>
</ul>

- Để tạo các Incremental backup khác:
```sh
tar --create --file=archive.2.tar --listed-incremental=/var/log/urs.snar-2 -P /home/quoctrimai/
```
Đây được gọi là bản backup level 1

- Để kiểm chứng Incremental backup tôi sẽ tạo 1 file và backup với `tar`
```sh
mkdir /home/quoctrimai/abc.txt
```
Sau khi tạo xong sẽ backup thư mục /home/quoctrimai
```sh
tar --create --file=archive.3.tar --listed-incremental=/var/log/urs.snar-3 -P /home/quoctrimai/
```

- Kiểm tra dung lượng của thư mục chứa archive sẽ thấy update của bản backup thay đổi
```sh
cd /var/log
du -ah
```
Ta sẽ thấy phần thay đổi
```sh
OUTPUT
4.0K	./urs.snar-2
4.0K	./urs.snar-3
```
- Để có thể backup lại dữ liệu đã thay đổi, phải backup lần lượt từng bản thay đổi:
Ví dụ có 2 bản thay đổi
```sh
tar --extract --listed-incremental=/dev/null --file archive.1.tar -P
```
```sh
tar --extract --listed-incremental=/dev/null --file archive.2.tar -P
```
- Để xem thông tin và liệt kê nội dung trong archive:
```sh
tar --list --incremental --verbose --verbose --file archive.3.tar
```

<a name="3"></a>
##3. Tài liệu tham khảo
- http://ntssi.vn/2016/04/tong-quan-ve-full-backup-differential-backup-va-incremental-backup/
- https://www.digitalocean.com/community/tutorials/how-to-create-an-off-site-backup-of-your-site-with-rsync-on-centos-6
- http://www.gnu.org/savannah-checkouts/gnu/tar/manual/html_node/Incremental-Dumps.html










 






