#Giao thức lưu trữ
Mục lục:

[1 CIFS(Common Internet File Sharing)](#1)

[2 SMB (SAMBA)](#2)

[3 NFS](#3)

[4 iSCSI](#4)

[5 Fibre Channel](#5)

===================

<a name="1"></a>
###1 CIFS(Common Internet File Sharing)
<ul>
<li>CIFS được phát triển dựa trên SMB thành một giao thức chia sẻ file phổ biến trên Windows và IBM.
<li>Giao thức này cho phép một máy khách (client) thực hiện các thao tác với các file như là chúng nằm trên máy tính đó.
<li>CIFS thiết kế với tiêu chí đơn giản và khả năng đáp ứng số lượng lớn người dùng. Người dùng quyết định quyền Read-Only hoặc Read-Write và đặt password đối với dữ liệu được chia sẻ (thư mục , ổ đĩa…)
<li>CIFS phù hợp với mô hình mạng LAN một server tập trung, mọi dữ liệu được xử lý từ client đều được khuyến nghị là lưu trữ tại server.
<li>CIFS vận hành trên TCP/IP và sử dụng dịch vụ DNS của Internet.
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

Hầu hết các hệ thống lưu trữ không còn sử dụng CIFS mà sử dụng SMB 2 và SMB 3. 

Cài đặt CIFS trên linux để share file với Windows.

http://genk.vn/thu-thuat/huong-dan-cach-chia-se-du-lieu-giua-windows-va-linux-20131203103930552.chn

<a name="2"></a>
###2 SMB (SAMBA)

<ul>
<li>SAMBA là một dịch vụ chạy trên hệ thống Unix, nó cho phép Windows để chia sẻ file và máy in trên các máy chủ Unix, và nó cũng cho phép người sử dụng Unix để truy cập tài nguyên được chia sẻ bởi các hệ thống Windows.</li>
<li>Microsoft sử dụng SMB cùng với giao thức xác thực NTLM để cung cấp dịch vụ gọi là chia sẻ file và máy in ở mức user. Khi một user đã đăng nhập thực hiện kết nối tới tài nguyên chia sẻ trên máy tính khác (\\server\share), Windows tự động gửi thông tin đăng nhập của user đó tới SMB server trước khi hỏi username hoặc password.</li>
<li>Phiên bản SMB2 và SMB3 là phiên bản nâng cấp của CIFS</li>
<li>SMB 2.0 được giới thiệu năm 2006. Nó cải thiện hiệu suất, khả năng mở rộng tốt hơn bằng cách tăng số lượng người sử dụng, tăng kích thước lưu trữ... </li>
<li>SMB 3.0 được giới thiệu trên Windows 8 và Windows server 2012. Hỗ trợ nhiều kết nối, cải thiện bảo mật với thuật toán AES.</li>
<li>Phiên bản mới nhất SMB 3.1.1 được giới thiệu trên Windows 10, Windows Server 2016.
</ul>

Samba gồm 3 thành phần chính đó là smbd, nmbd và winbindd.

smbd: Quản lý việc chia sẻ file và dịch vụ in cho các client, đồng thời cũng chịu trách nhiệm chứng thực người dùng bằng cách sử dụng port 139 và 445 để lắng nghe các yêu cầu đến thư mục chia sẻ trên Linux.
Khi một client kết nối, smbd sẽ tạo ra một tiến trình mới, phục vụ cho kết nối này.
nmdb: Lắng nghe trên port 137, chịu trách nhiệm cung cấp tên NetBIOS của samba server cho các request kết nối.
winbindd: Dùng khi Samba là 1 phần của domain, dùng để truy vấn server Windows thông tin nhóm và người dùng.


<a name="3"></a>
###3 NFS

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

<a name="4"></a>
###4 iSCSI
 
iSCSI (Internet Small Computer System Interface) là một giao thức chạy trên nền TCP/IP, cho phép kết nối tới Storage bằng đường Network (LAN/WAN) mà không cần một Fiber Chennel mạng riêng biệt. Giao thức cho phép các lệnh SCSI truyền trên đường Network, giúp ta có thể truy xuất và truyền tải dữ liệu từ khoảng cách xa. Hỗ trợ trợ các ứng dụng yêu cầu chạy trên nền tảng Block Storage.

<img src=http://i.imgur.com/Z3pIATQ.png>

Không như Fiber Channel (FC) SAN là phải xây dựng hạ tầng mạng mới, iSCSI SAN tận dụng hạ tầng LAN sẵn có (các thiết bị mạng, Swich... trên nền IP).

Cách thức hoạt động:
<ul>
<li>Khi một người dùng hoặc một ứng dụng gửi một request yêu cầu truy xuất dữ liệu trong Storage.</li>
<li>Hệ thống sẽ tạo ra một số lệnh SCSI tương ứng với yêu cầu.</li>
<li>Sau đó đóng gói (Encapsulate) và mã hóa (Encrypt) và gửi đi trên đường Network.</li>
<li>Khi Server nhận được, nó sẽ tháo (De-Encapsulate) và giải mã (Decrypt) để cuối cùng nhận được các lệnh SCSI.</li>
<li>Các lệnh SCSI sẽ được đưa vào SCSI Controller để thực thi và xử lý theo yêu cầu.</li>
</ul>

Các ổ đĩa ISCSI được tạo ra từ các Server chạy các hệ điều hành như Windows/Linux.

Hệ thống disk ISCSI 

<img src=http://i.imgur.com/cNx3MtW.png>

**LUN** (Logical Unit Number), là một con số logic dùng để tập hợp các ổ đĩa chạy bằng các loại giao thức SCSI, iSCSI và Fibre Channel. LUN là nơi quản lý tập trung các các ổ đĩa trong hệ thống Storage Network (Storage Area Network – SAN).
LUNS sẽ gắn cho iSCSI một con số logic và gọi là “Target”.

ISCSI gồm 2 thành phần ISCSI Target và ISCSI Initiator

<img src=http://i.imgur.com/8vhwU66.png>

Khi một server hoặc một thiết bị nào đó muốn kết nối tới hệ thống iSCSI SAN, chúng sẽ dùng một software gọi là iSCSI Initiator để kết nối tới con số “Target” này. Và con số này sẽ quản lý kết nối giữa iSCSI Target và iSCSI Initiator.

Từ Windows Server 2008 trở về sau, Microsoft hỗ trợ tính năng tạo ra hệ thống lưu trữ iSCSI SAN và chúng được gọi là “iSCSI Targets Server”

<img src=http://i.imgur.com/lceampM.png>

Lợi ích:
```sh
iSCSI Target Server cho phép kết nối tới iSCSI SAN mà không cần dùng software iSCSI Initiator của Microsoft (Ví dụ như nền tảng Linux)
Được xem là nền tảng Block Storage cho nên nó hỗ trợ trợ các ứng dụng yêu cầu chạy trên nền tảng Block Storage, tương thích và tích hợp với tính năng Failover Clustering để tăng độ sẵn sàng cho các ứng dụng.
Đối với sử dụng tính năng Hyper-V, iSCSI SAN cho phép lưu trữ các máy ảo (Virtual Machine), đồng thời hỗ trợ một số tính năng High Availability như (Live Migration, Storage Migration, Failover Clustering).
```

<a name="5"></a>
###5 Fibre Channel
<ul>
<li>Fibre Channel là 1 giao thức tầng transport tốc độ cao được sử dụng trong SAN.</li>
<li>Việc truyền dữ liệu từ Server đến hệ thống lưu trữ SAN được sử dụng dựa trên các cổng quang để truyền dữ liệu : 1 GBb/s Fiber Channel , 2 GBb/s Fiber Channel , 4 GBb/s Fiber Channer , 8 GBb/s Fiber Channer , 1 GBb/s iSCSI ,.....</li>
<li>Chi phí triển khai hệ thống SAN cực kỳ đắt, nó đòi hỏi phải dùng các thiết bị Fiber Chennel Networking, Fiber Channel Swich...</li>
<li>Được thiết kế để tương thích với SCSI </li>
</ul>

FC có 3 topo mạng chính:
**Point to point**: 2 thiết bị kết nối trực tiếp với nhau.
**Arbitrated Loop**: Các thiết bị kết nối thành một vòng. Khi thêm hoặc loại bỏ một thiết bị trên vòng gây nên gián đoạn cho hoạt động. Chỉ có 2 cổng giao tiếp đồng thời.
**Switched Fabric**: Các thiết bị kết nối tới Fibre Channel switches. Nhiều cặp cổng có thể giao tiếp cùng lúc. Gián đoạn của 1 port ko ảnh hưởng tới hoạt động của port khác. 

<img src=http://i.imgur.com/OdDsURo.png>

Port tương ứng với các topo

<img src=http://i.imgur.com/Vl4A32Z.png>

Fibre Channel không theo OSI mà được phân chia thành 5 Layer

**FC-4**: Protocol-mapping layer, trong đó giao thức SCSI, IP được đóng gói vào IU gửi tới FC-2.
**FC-3**: Common services layer, thực hiện mã hóa, RAID, kết nối multiport.
**FC-2**: Signaling Protocol, được xác định bởi Fibre Channel Framing và chuẩn Signaling 4 (FC-FS-4), bao gồm các giao thức FC mức thấp, kết nối các Port.
**FC-1**: Transmission Protocol, thực hiện mã hóa đường tín hiệu.
**FC-0**: PHY, các cáp kết nối.

<img src=http://i.imgur.com/3dJupOO.png>

Tính năng :
```sh
​Lưu trữ được truy cập theo Block qua SCSI
Khả năng I/O với tốc độ cao
Tách biệt thiết bị lưu trữ và Server
Topology Views: Hiển thị dữ liệu hoạt động
Logical and Physical Topologies: cho phép quản lý nền tảng thiết bị phụ thuộc lẫn nhau, bất kể kết nối. topology vật lý cho phép đường dẫn thiết bị khác nhau cho hiệu suất và tính sẵn sàng.
Path Tuples: Fabric names and endpoint associators, like platforms, nodes and ports
Logical components: Extents, data movers, platforms, fabric, nodes, FC and aggregators
```
**Benchmark NFS vs CIFS**

http://www.anandtech.com/show/7071/synology-ds1812-8bay-smb-soho-nas-review/4

<img src=http://i.imgur.com/csbStvJ.png>

**Benchmark NFS vs SMB**

https://ferhatakgun.com/network-share-performance-differences-between-nfs-smb/

<img src=http://i.imgur.com/ireVx5X.png>

**Benchmark iSCSI vs SMB**

https://blogs.technet.microsoft.com/larryexchange/2016/01/10/iscsi-or-smb-direct-which-one-is-better/

<img src=http://i.imgur.com/8XaVsff.png>

**Connecting to storage Systems using iSCSI, NFS, and CIFS (SMB)**

http://www.everythingvm.com/content/connecting-storage-systems-using-iscsi-nfs-and-cifs-smb

Tham khảo:

[1]- http://searchstorage.techtarget.com/definition/Common-Internet-File-System-CIFS

[2]- https://www.techopedia.com/definition/1867/common-internet-file-system-cifs

[3]- http://netone.com.vn/Trangchu/Hotrokythuat/Kienthuccanban/tabid/366/arid/1450/Default.aspx

[4]- https://en.wikipedia.org/wiki/Server_Message_Block

[5]- https://en.wikipedia.org/wiki/ISCSI

[6]- http://svuit.vn/threads/ly-thuyet-he-thong-luu-tru-iscsi-san-938/

[7]- http://searchstorage.techtarget.com/definition/iSCSI

[8]- http://searchstorage.techtarget.com/definition/Fibre-Channel

[9]- https://www.techopedia.com/definition/1081/fiber-channel-storage-area-network-fc-san

[10]- https://ferhatakgun.com/network-share-performance-differences-between-nfs-smb/
















