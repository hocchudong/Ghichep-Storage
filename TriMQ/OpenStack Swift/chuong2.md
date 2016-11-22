#Chương 2: Meet Swift

#Mục lục:
**Table of Contents**
- [1. Mở đầu chương ](#1)
	- [1.1 Giới thiệu về Swift](#11)
	- [1.2 Những đặc tính chính của Swift](#12)
	
- [2. Meet SwiftStack](#2)
	- [2.1 Sản phẩm của SwiftStack](#21)
	- [2.2 Lợi ích của SwiftStack](#22)
	- [2.3 Những tính năng khác](#23)
	
- [3. Giải thích những thuật ngữ](#3)

- [4. Tài liệu tham khảo](#4)

=======================================================================

<a name="1"></a>
##1. Mở đầu chương:

<a name="12"></a>
###1.1 Giới thiệu về Swift:
Chương này giới thiệu về những chức năng chính và lợi ích của Swift mang lại, tổng quan về các khái niệm để có thể hiểu rõ hơn về Swift.
 Giới thiệu về công ty SwiftStack cung cấp phần mềm và hỗ trợ cài đặt Swift
 
Swift được thiết kế để lưu trữ 1 lượng lớn dữ liệu unstructured data (như là documents, web content, backups, images, and virtual machine snapshots) với giá thành rẻ, được sử dụng bởi các công ty, tổ chức, các nhà cung cấp dịch vụ, các tổ chức nghiên cứu trên toàn thế giới. 
Được phát triển từ năm 2010 phục vụ cho RackSpace Cloud Files, sử dụng mã nguồn mở và là 1 project trong Openstack

 Swift không được phát triển như những cách lưu trữ truyền thống như block, file, nó sẽ tương tác với metadata trong container thông qua RESTful HTTP API. 
 Các developer có thể viết trực tiếp tới Swift API hoặc các ngôn ngữ lập trình phổ biến khác
 
 
<a name="12"></a>
###1.2 Những đặc tính chính của Swift:
Swift có 11 đặc tính chính:

-<b>Scalablity</b>:
Scalablity là khả năng mở rộng, Swift được thiết kế để có thể mở rộng dựa vào lượng dữ liệu cần được lưu trữ và số người dùng cần được phục vụ. 
Từ 1 hệ thống nhỏ 1 vài nodes có thể mở rộng thành 1 hệ thống lớn hơn với hàng ngàn máy, giúp cho hệ thống luôn hoạt động ổn định

-<b>Durability</b>:<n>
Durability là khả năng mở rộng, Swift cung cấp 1 kiến trúc mới cho phép cung cấp 1 độ đảm bảo cho dữ liệu luôn sẵn sàng và toàn vẹn. 
Để làm việc này, Swift sẽ sao chép dữ liệu và phân tán chúng tới các nodes khác. Sẽ có 1 quá trình được chạy để kiểm tra xem có đủ số bản sao chép của dữ liệu hay 
không hay dữ liệu có được đảm bảo tốt hay không

-<b>Multi-regional capability</b>:
Multi-regional capability là khả năng đa khu vực,
<ul>
<li>Swift có khả năng phân phối dữ liệu tới những khu vực lưu trữ khác nhau ( có thể làm tăng latency). 
Điều này giúp cho tính sẵn sàng của dữ liệu, có thể truy cập ở các vùng khác nhau. Hoặc là có thể định rõ 1 khu vực để khôi phục khi có sự cố</li>
<li>Swift thực hiện việc phân phối dữ liệu dựa trên regions va zones trong 1 cluster. Việc Swift sử dụng regions và zones cho phép sap chép dữ liệu ra toàn cụm cluster, 
khi có hỏng hóc hoặc sự cố tại 1 zones thì cluster vẫn hoạt động bình thường và đảm bảo tính toàn vẹn và sẵn sàng của hệ thống</li>
</ul>

-<b>High concurrency </b>:
High concurrency là tính thống nhất cao, Swift là kiến trúc có thể phân phối các yêu cầu trên nhiều máy chủ, Swift có thể tận dụng năng lực sẵn có của server để xử lý các yêu cầu 
cùng lúc. Điều này làm tăng sự thống nhất của hệ thống và tổng lưu lượng available

-<b>Flexible storage</b>
Flexible storage là khả năng linh hoạt trong lưu trữ. Swift có sự linh hoạt trong kiến trúc và phần cứng, giúp cho nhà khai thác có thể tùy chỉnh được hệ thống lưu trữ phù hợp. 
Swift có những chính sách lưu trữ cho phép các nhà khai thác có thể sử dụng phần cứng tối ưu nhất để có thể kiểm soát được. 

Ví dụ: Swift có chính sách tạo ra 1 hệ thống lưu trữ chỉ bảo gồm các ổ SSDs trong cluster

Về cơ bản phương pháp lưu trữ của Swift là rất linh hoạt. Thông thường thì các thiết bị lưu trữ sẽ bó buộc lại trong 1 cụm lưu trữ nhưng những công nghệ mới, 
các hệ thống mã nguồn mở hay là các hệ thống storage thương mại có adapter có thể là storage target trong 1 cụm lưu trữ của Swift

-<b>Open source </b>
 Swift là mã nguồn mở và là 1 project trong Openstack (theo giấy phép của Apache 2). Cũng như các dự án nguồn mở khác, Swift có thể được review bởi rất nhiều developer do đó khi có lỗi sẽ được phát hiện và xử lý nhanh hơn so với các phần mềm độc quyền.
    
Swift có khoảng 150 nhà phát triển tham gia tính đến đầu năm 2014

-<b>Large ecosystem</b>
Hệ sinh thái lớn đa dạng, Hệ sinh thái của Swift rất lớn bởi đó là 1 dự án mã nguồn mở, nhưng khác với các phần mềm nguồn mở khác thì Swift được rất nhiều công ty test và phát triển, 
do đó tránh bó buộc người dùng vào 1 nhà phát triển 

Do có nhiều nhà phát triển cùng tham gia vào dự án nên tốc độ phát triển sẽ rất tốt như các công cụ, các tiện ích, các ứng dụng. 
Nhiều công cụ, thư viện, clients, và các ứng dụng đã hỗ trợ API của Swift. 

-<b>Runs on commodity hardware</b>
Chạy trên nhiều nền tảng phần cứng thông thường
<ul>
<li>Swift được thiết kế để có thể kiểm soát đưọc sự cố, sự đảm bảo của từng thành phần riêng lẻ không quá quan trọng. 
Swift có thể chạy trên các nền tảng phần cứng khác nhau do vậy các công ty có thể lựa chọn các thiết bị phần cứng phù hợp</li>
<li> Swift có khả năng chạy trên các phần cứng khác nhau do vậy tránh được việc người dùng bị lock-in vào các phần cứng chuyên biệt của nhà phát hành nên có thể tận dụng 
điểm mạnh của các phần cứng vào hệ thống.</li>
</ul>

-<b>Developer-friendliness </b>
Nhà phát triển thân thiện, ngoài các chức năng cốt lõi là sử dụng để lư trữ và đảm bảo tính bền vững của dữ liệu ở quy mô lớn, 
Swift cấp các tính năng hữu ích cho người dùng 

Các tính năng hữu ích như (11 tính năng) :
<ul>
<li>Static website hosting: User có thể host static website, hỗ trợ client-side Javascript và CSS, Swift cũng hỗ trợ tùy chỉnh các lỗi và tự generate ra listing</li>
<li>Automatically expiring objects: Swift có thể đưa ra thời gian hết hạn cho dữ liệu và loại bỏ. Tính năng này rất hữu ích để ngăn chặn dữ liệu không cần thiết di chuyển trong 
hệ thống để đảm bảo chính sách lưu trữ dữ liệu</li>
<li>Time-limited URLs</li>
<li>Quotas: Giới hạn lưu trữ có thể set trên container và account</li>
<li>Upload directly from HTML forms: người dùng có thể tạo ra các web forrm upload dữ liệu trực tiếp qua Swift</li>
<li> Versioned writes: người dùng có thể viết 1 version mới của object trong khi vẫn giữ được các version cũ hơn</li>
<li>Support for chunked transfer encoding: User có thể upload dữ liệu mà không cần biết trước object lớn như nào</li>
<li>Multirange reads: Người dùng có thể đọc 1 hay nhiều section của object mà chỉ cần 1 request</li>
<li>Access control lists: Người dùng có thể configure được truy cập của data</li>
<li>Programmatic access to data locality: Swift có thể tích hợp với Hadoop để tận dụng thông tin cục bộ trong quá trình xử lý dữ liệu</li>
<li>Customizability: middleware có thể phát triển và chạy trực tiếp trên hệ thống</li>
</ul>

-<b>Operator-friendly</b>
Swift có thể lưu trữ và quản lý dư liệu dễ dàng thông qua 1 API, người dùng không cần mất thời gian để quản lý volumes cho từng project. Swift có cấu trúc bền vững giúp hệ thống tránh được những sự cố nghiêm trọng

-<b>Upcoming features</b>
Các phiên bản sau của Swift sẽ cập nhật thêm các tính năng như policys storage hay erasure coding. 


<a name="2"></a>
##2. Meet SwiftStack
SwiftStack là 1 công ty cung cấp phần mềm mang tính sẵn sàng cao và mở rộng lưu object storage dựa trên Openstack Swift và là 1 lựa chọn để cài đặt, tích hợp, điều hành Swift hay cài trực tiếp từ nguồn

SwiftStack được đóng góp bởi 1 số những contributors được chứng nhận, kết hợp với những công ty có kinh nghiệm thực tế về triển khai Swift cho phép SwiftStack đóng góp rất lớn và dẫn ra các sáng kiến lớn cho Swift có thể kết hợp với cộng đồng phát triển

Một gói phần mềm SwiftStack bao gồm 100% phiên bản mã nguồn mở Openstack Swift và thêm các phần mềm để triển khai, tích hợp (với hệ thống xác thực và thanh toán), giám sát và quản lý Swift Cluster. SwiftStack cũng cung cấp đào tạo, tư vấn, và 24 × 7 hỗ trợ cho phần mềm SwiftStack, bao gồm Swift

<a name="21"></a>
###2.1 Sản phẩm của SwiftStack
-<b>SwiftStack Node</b>
<ul>
<li>Để chạy Openstack Swift, phần mềm SwiftStack sẽ tự động cài đặt gói phiên bản cài đặt mới nhất của Swift. Tại bài viết này,sẽ cài đặt cho CentOS / Red Hat server 6.3, 6.4, 
hoặc 6.5 (64-bit), hoặc Ubuntu 12.04 LTS Precise Server (64-bit)</li>
<li>Thêm vào đó, phần mềm SwiftStack Nodes cung cấp một số yếu tố trước khi cấu hình, tích hợp bổ sung và 1 số phương pháp truy cập</li>
</ul>

-<b>SwiftStack Controller</b>
SwiftStack Controller là 1 hệ thống out-of-band controller quản lý 1 hay nhiều cụm Swift và tự động triển khai, tích hợp hoạt động của SwiftStack Nodes. 
SwiftStack controller tách biệt việc điều khiển và quản lý các nodes lưu trữ khỏi các phần cứng vật lý 

<a name="22"></a>
###2.2 Lợi ích của SwiftStack:
-<b>Automated storage provisioning</b>
Tự động lưu trữ dự phòng, thiết bị sẽ được tự động nhận dạng bởi agent chạy trên SwiftStack Node và những nhà khai thác đặt các node đó trên region hoặc zone. SwiftStack Controller sẽ cung cấp 1 giao diện thống nhất cho người dùng có thể thêm hoặc bớt dung lượng lưu trữ

-<b>Automated failure management</b>
Tự động quản lý lỗi, Agent chạy trên SwiftStack Node sẽ phát hiện lỗi xảy ra trên ổ đĩa và sẽ đưa ra cảnh báo tới nhà phát hành. Mặc dù Swift sẽ tự động định tuyến để tránh lỗi tuy nhiên thì tính năng này cho phép nhà khai thác có thể xử lý thất bại 1 cách nhất quán, sẽ có 1 dashboard thống kê cho phép nhà khai thác có thể theo dõi được

-<b>Lifecycle management</b>
Quản lý vòng đời, 1 SwiftStack Controller có thể nâng cấp riêng từ Swift. 

-<b>User and capacity management</b>
Quản lý user và dung lượng lưu trữ, có 1 số modules để xác thực được hỗ trợ bởi SwiftStack, có thể tạo 1 storage group (cung cấp với 1 API) và tích hợp vào đó LDAP và Keystone

-<b>Additional access methods</b>
Những phương thức truy cập, SwiftStack bao gồm 1 số Web UI cho user có thể tùy chỉnh với việc thêm vào CSS cho phù hợp. SwiftStack cũng cung cấp filesystem access qua CIFS hoặc NFS

<a name="23"></a>
###2.3 Những tính năng khác:

- Built-in load balancer ( tích hợp load balancer)
- SSL termination for HTTPS services
- Disk management tools (công cụ quản lý đĩa)
- Swift ring building and ring deployment (xây dựng và triển khai Swift ring)
- Automated gradual capacity adjustments (điều chỉnh tự động dung lượng)
- Health-check and alerting agents (Kiểm tra và đưa ra các cảnh báo agent)
- Node/drive replacement tools
- System monitoring and stats collection
- Capacity monitoring and trending
- Web client / user portal

<a name="3"></a>
##3. Giải thích những thuật ngữ:
####Phần 1:
- multi-tenant
- highly scalable: khả năng mở rộng cao
- durable: độ bền của dữ liệu
- RackSpace Cloud Files: cung cấp onlile object storage cho file và media
- containers: chứa các object, trong Amazone S3 container được gọi là bucket
- Catastrophic failure : sự cố nghiêm trọng
- Erasure Coding: là phương pháp bảo vệ dữ liệu, dữ liệu được chia thành các đoạn, mở rộng và mã hoá với các mảnh dữ liệu và được lưu trữ trên các địa điểm hoặc các phương tiện lưu trữ khác nhau.
- Coexist: cùng tồn tại 
- Standard replica model: mô hình bản sao dữ liệu tiêu chuẩn 

####Phần 2:
- integrating: tích hợp
- heavily upstream
- major initiatives: sáng kiến lớn
- collaboration: sự hợp tác 
- Lightweight Directory Acccess Protocol (LDAP)
- Agents

<a name="4"></a>
##4. Tài liệu tham khảo:
Openstack Swift - Joe Arnold&members of the SwiftStack team














 


 
 
	
	
