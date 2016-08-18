#Các giao thức lưu trữ

##1. FC (Fiber channel):
####1.1 Khái niệm:
Là công nghệ để truyền dữ liệu giữa các port trên cable serial, I/O ở tốc độ cao
####1.2 Đặc điểm của Fiber channel:
- Phù hợp kết nối server để share thiết bị lưu trữ, kết nối các thiết bị lưu trữ đến server.
- Fiber Channel được sử dụng phần lớn là ở trên cáp quang.
- Tốc độ của FC cable có thể lên tới 16GB/serial.
- Fiber Channel Protocol  là giao thức truyền tải SCSI command thông qua FC network.
- Fiber Channel không sử dụng mô hình OST mà sử dụng mô hình riêng chia thành 5 layer.
<img src="http://www.hill2dot0.com/wiki/index.php?title=Image:Fibre_Channel_Protocol_Stack.JPG">

##2. SMB/CIFS:
####2.1 Khái niệm:
Ban đầu SMB là 1 giao thức mạng dùng để naming và browsing, sau đó được Microsoft phát triển và gọi là CIFS, và được sử dụng để chia sẻ file phổ biến trong phiên bản hệ điều hành của Microsoft
####2.2 Đặc điểm:
- CIFS là 1 giao thức phổ biến trên Window.
- Người dùng có thể phân quyền cho thư mục hoặc file được chia sẻ.
- CIFS sử dụng giao thức TCP/IP của Internet. Được coi là giao thức ở tầng Application (L7) hoặc Presentation (L6). Chạy trên cổng 445(TCP)
- Hoạt động theo mô hình client-server. Trong mô hình này, khi client gửi 1 request đến server (request là yêu cầu như open file, close file, read file...), sau đó server sẽ xác thực yêu cầu và gửi lại thông tin mà client yêu cầu.
- CIFS có 1 số tính năng bổ sung như:
<ul>
<li>In qua mạng</li>
<li>Xác thực truy cập file, thư mục chia sẻ</li>
<li>Thông báo sự thay đổi file và thư mục</li>
<li>Hỗ trợ Unicode</li>
</ul>

##3. NFS (network file system):
####3.1 Khái niệm:
Là 1 giao thức phổ biến trên Linux, là 1 giao thức để chia sẻ dữ liệu. NFS cho phép chia sẻ file với nhiều người dùng trên cùng mạng và người dùng có thể thao tác với file trên đĩa cứng của mình bằng lệnh mount.
####3.2 Đặc điểm:
- NFS cho phép người dùng xem, lưu trữ, cập nhật file từ xa thông qua máy của họ bằng cách mount từ máy khác về. Người dùng có thể phân quyền cho thư mục được chia sẻ.
- NFS sử dụng RPC (Remote Procedule call) để định tuyến request giữa client và server, sử dụng portmap 111

##4. ISCSI (Internet small computer system interface):
####4.1 Khái niệm:
ISCSI là 1 công nghệ dùng để truyền tải truyền tải các lệnh SCSI, sử dụng giao thức TCP/IP. SCSI là 1 giao thức dùng để kết nối các thiết bị phần cứng có thể trong hoặc ngoài máy tính.
####4.2 Đặc điểm:
- Thường được sử dụng trong các server lưu trữ và truyền dữ liệu với tốc độ cao.
- Là chuẩn giao tiếp dùng để kết nối ổ cứng, cd-rom..
- SCSI sử dụng Bus PCI hoặc ISAA để truyền tín hiệu dữ liệu.






