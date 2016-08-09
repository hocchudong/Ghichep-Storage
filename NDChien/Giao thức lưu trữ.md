#Giao thức lưu trữ
Mục lục:

[1 CIFS(Common Internet File Sharing)](#1)

[2 NFS](#2)

[3 iSCSI](#3)

[4 Fibre Channel](#4)

===================

<a name="1"></a>
###1 CIFS(Common Internet File Sharing)
<ul>
<li>CIFS được phát triển dựa trên SMB thành một giao thức chia sẻ file phổ biến trên Windows và IBM.
<li>Giao thức này cho phép một máy khách (client) thực hiện các thao tác với các file như là chúng nằm trên máy tính đó.
<li>CIFS thiết kế với tiêu chí đơn giản và khả năng đáp ứng số lượng lớn người dùng. Người dùng quyết định quyền Read-Only hoặc Read-Write và đặt password đối với dữ liệu được chia sẻ (thư mục , ổ đĩa…)
<li>CIFS phù hợp với mô hình mạng LAN một server tập trung, mọi dữ liệu được xử lý từ client đều được khuyến nghị là lưu trữ tại server.
<li>CIFS vận hành trên TCP/IP và sử dụng hệ DNS toàn cầu của Internet.
<li>CIFS hỗ trợ đặc điểm chấp nhận lỗi (fault tolerance) và có thể phục hồi tự động các kết nối và mở lại tập tin trước đó bị gián đoạn.
<li>Tính năng an toàn trong CIFS bao gồm hỗ trợ truyền nặc danh và truy cập an toàn, có kiểm tra tính hợp lệ. 
<li>Chính sách bảo mật tập tin và thư mục là dễ quản lý, và dùng cùng một mô hình như các chính sách bảo mật mức chia sẻ (share level) và mức người dùng (user-level) trong các môi trường Window.
</ul>

**CIFS thường được sử dụng trong các công ty lớn và các công ty có nhân viên làm việc với rất nhiều dữ liệu cần phải được truy cập bởi nhiều người sử dụng**

Các chức năng bổ sung so với SMB:
```sh
CIFS bổ sung Hypertext Transfer Protocol (HTTP)
Đàm phán, dàn xếp để tương thích giữa các hình thái SMB
Phát hiện các máy chủ sử dụng SMB trên mạng (browse network)
In qua mạng
Xác thực truy cập file, thư mục chia sẻ
Thông báo sự thay đổi file và thư mục
Xử lý các thuộc tính mở rộng của file
Hỗ trợ Unicode
Khóa file đang truy cập tùy theo cơ hội (oplock)
```
Hạn chế: Tất cả dữ liệu chuyển giao không được mã hóa khi nó được gửi qua mạng.

Cài đặt CIFS trên linux để share file với Windows.

http://genk.vn/thu-thuat/huong-dan-cach-chia-se-du-lieu-giua-windows-va-linux-20131203103930552.chn

<a name="2"></a>
###2 NFS

<ul>
<li>NFS là hệ thống cung cấp dịch vụ chia sẻ file phổ biến trong hệ thống mạng Linux và Unix.</li>
<li>NFS cho phép các máy tính kết nối tới 1 phân vùng đĩa trên 1 máy từ xa giống như là local disk. Cho phép việc truyền file qua mạng được nhanh và trơn tru hơn.</li>
<li>NFS sử dụng mô hình Client/Server. Trên server có các disk chứa các file hệ thống được chia sẻ và một số dịnh vụ chạy ngầm (daemon) phục vụ cho việc chia sẻ với Client.</li>
<li>Phục vụ cả trên internet là mạng LAN.</li>
<li>Cung cấp chức năng bảo mật file và quản lý lưu lượng sử dụng (file system quota).</li>
<li>Các Client muốn sử dụng các file system được chia sẻ thì sử dụng giao thức NFS để mount các file đó về.</li>
<li>Khi triển khai hệ thống lớn hoặc chuyên biệt cần áp dụng 3NFS, còn người dùng ngẫu nhiên hoặc nhỏ lẻ thì áp dụng 2NFS, 4NFS.</li>
<li>Với NFSv4, yêu cầu hệ thống phải có kernel phiên bản từ 2.6 trở lên
<li>Xử lý được những file lớn hơn 2GB,  đòi hỏi hệ thống phải có phiên bản kernel lớn hơn hoặc bằng 2.4x và glibc từ 2.2.x trở lên
<li>Client từ phiên bản kernel 2.2.18 trở đi đều hỗ trợ NFS trên nền TCP
</ul>

Tham khảo https://github.com/chiennd/ghichep-nfs/blob/master/NDChien_Baocao_NFS.md#1

<a name="3"></a>
###3 iSCSI
 
iSCSI (Internet Small Computer System Interface) là một giao thức chạy trên nền TCP/IP, cho phép kết nối tới Storage bằng đường Network (LAN/WAN) mà không cần một Fiber Chennel mạng riêng biệt. Giao thức cho phép các lệnh SCSI truyền trên đường Network, giúp ta có thể truy xuất và truyền tải dữ liệu từ khoảng cách xa.

Không như Fiber Channel (FC) SAN là phải xây dựng hạ tầng mạng mới, iSCSI SAN tận dụng hạ tầng LAN sẵn có (các thiết bị mạng, Swich... trên nền IP).

Cách thức hoạt động:
<ul>
<li>Khi một người dùng hoặc một ứng dụng gửi một request yêu cầu truy xuất dữ liệu trong Storage.</li>
<li>Hệ thống sẽ tạo ra một số lệnh SCSI tương ứng với yêu cầu.</li>
<li>Sau đó đóng gói (Encapsulate) và mã hóa (Encrypt) và gửi đi trên đường Network.</li>
<li>Khi Server nhận được, nó sẽ tháo (De-Encapsulate) và giải mã (Decrypt) để cuối cùng nhận được các lệnh SCSI.</li>
<li>Các lệnh SCSI sẽ được đưa vào SCSI Controller để thực thi và xử lý theo yêu cầu.</li>
</ul>

**LUN** (Logical Unit Number), là một con số logic dùng để tập hợp các ổ đĩa chạy bằng các loại giao thức SCSI, iSCSI và Fibre Channel. LUN là nơi quản lý tập trung các các ổ đĩa trong hệ thống Storage Network (Storage Area Network – SAN).
LUNS sẽ gắn cho iSCSI một con số logic và gọi là “Target”.

Các ổ đĩa iSCSI được tạo ra từ các Server chạy các hệ điều hành như Windows/Linux.

Khi một server hoặc một thiết bị nào đó muốn kết nối tới hệ thống iSCSI SAN, chúng sẽ dùng một software gọi là iSCSI Initiator để kết nối tới con số “Target” này. Và con số này sẽ quản lý kết nối giữa iSCSI Target và iSCSI Initiator

Từ Windows Server 2008 trở về sau, Microsoft hỗ trợ tính năng tạo ra hệ thống lưu trữ iSCSI SAN và chúng được gọi là “iSCSI Targets Server”

Lợi ích:
<ul>
<li>iSCSI Target Server cho phép kết nối tới iSCSI SAN mà không cần dùng software iSCSI Initiator của Microsoft (Ví dụ như nền tảng Linux)</li>
<li>Được xem là nền tảng Block Storage cho nên nó hỗ trợ trợ các ứng dụng yêu cầu chạy trên nền tảng Block Storage, tương thích và tích hợp với tính năng Failover Clustering để tăng độ sẵn sàng cho các ứng dụng.</li>
<li>Đối với sử dụng tính năng Hyper-V, iSCSI SAN cho phép lưu trữ các máy ảo (Virtual Machine), đồng thời hỗ trợ một số tính năng High Availability như (Live Migration, Storage Migration, Failover Clustering).</li>
</ul>

<a name="4"></a>
###4 Fibre Channel
<ul>
<li>Fibre Channel là 1 giao thức tầng transport tốc độ cao được sử dụng trong SAN.</li>
<li>Việc truyền dữ liệu từ Server đến hệ thống lưu trữ SAN được sử dụng dựa trên các cổng quang để truyền dữ liệu : 1 GBb/s Fiber Channel , 2 GBb/s Fiber Channel , 4 GBb/s Fiber Channer , 8 GBb/s Fiber Channer , 1 GBb/s iSCSI ,.....</li>
<li>Chi phí triển khai hệ thống SAN cực kỳ đắt, nó đòi hỏi phải dùng các thiết bị Fiber Chennel Networking, Fiber Channel Swich...</li>
<li>Các ổ đĩa chạy trong hệ thống lưu trữ SAN thường được dùng: FIBRE CHANNEL , SAS , SATA,... </li>
</ul>

Tính năng :
​Lưu trữ được truy cập theo Block qua SCSI
Khả năng I/O với tốc độ cao
Tách biệt thiết bị lưu trữ và Server






















