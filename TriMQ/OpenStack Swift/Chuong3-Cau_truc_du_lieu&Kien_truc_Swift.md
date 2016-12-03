# Chương 3: Swift’s Data Model and Architecture

# Mục lục:
**Table of Contents**
- [1. Mở đầu chương](#1)
- [2. Cấu trúc dữ liệu của Swift](#2)
	- [2.1 Account](#21)
	- [2.2 Container](#22)
	- [2.3 Object](#23)
- [3. Kiến trúc của Swift](#3)
	- [3.1 Regions](#31)
	- [3.2 Zones](#32)
	- [3.3 Node](#33)
	- [3.4 Storage policies](#34)
- [4. Các tiến trình của Server](#4)
	- [4.1 Proxy layer](#41)
	- [4.2 Account layer](#42)
	- [4.3 Container layer](#43)
	- [4.4 Object layer](#44)
- [5. Các tiến trình thống nhất](#5)
	- [5.1 Auditor](#51)
	- [5.2 Replicator](#52)
	- [5.3 Account reaper](#53)
	- [5.4 Container and object updaters](#54)
	- [5.5 Object expirer](#55)
- [6. Locating the Data](#6)
	- [6.1 Ring Basics: Hash Functions](#61)
	- [6.2 Ring Basics: Consistent Hashing Ring](#62)
	- [6.3 The Rings: Modified Consistent Hashing Ring](#63)
		- [6.3.1 Partition](#631)
		- [6.3.2 Partition power](#632)
		- [6.3.3 Replica count](#633)
		- [6.3.4 Replica lock](#634)
	- [6.4 Distribution of Data](#64)
- [7. Tạo và cập nhật the Rings](#7)
	- [7.1 Tạo hoặc cập nhật Builder Files](#71)
	- [7.2 Rebalancing the Rings](#72)
	- [7.3 Inside the Rings](#73)
- [8. Tổng kết](#8)
- [9. Tài liệu tham khảo](#9)

================================================

<a name="1"></a>
## 1. Mở đầu chương:
Mục tiêu của chương này giúp cho người đọc có thể làm quen với các thành phần khác nhau của Swift, hiểu được những thành phần đó làm việc như nào để cung cấp 1 hệ thống lưu trữ và những thứ khác như độ bền, khả năng mở rộng và tính thống nhất cao của Swift
 
Bắt đầu với những khái niệm tổng quan về cấu trúc dữ liệu của Swift và sau đó tìm hiểu về các layer khác nhau trong cấu trúc của Swift (clusters, regions, zones, nodes). Sau đó sẽ học về những loại server xử lý trong Swift (proxy, account, container, object) cũng như những tiến trình thống nhất
 
Sau khi bàn về việc dữ liệu được lưu trữ và tổ chức như nào thì vấn đề tiếp theo là Swift’s consistent hashing rings, và làm cách nào để xác định được vị trí của dữ liệu được đặt trên các cluster. Tóm lại của chapter này sẽ chỉ ra làm cách nào để quản lý Swift 1 cách đơn giản với phần mềm SwiftStack


<a name="2"></a>
## 2. Cấu trúc dữ liệu của Swift
Swift cho phép người dùng lưu trữ unstructured data với tên tiêu chuẩn chứa 3 phần account, container, object. Sử dụng 1 hay nhiều hơn những phần này cho phép hệ thống tạo thành 1 vị trí lưu trữ duy nhất cho dữ liệu

<img src="http://i.imgur.com/2AjF6yH.png">
#### /account
Account storage location là tên duy nhất của khu vực lưu trữ nó sẽ chứa metadata (mô tả thông tin) của chính account đó, cũng như danh sách container trong account. Chú ý rằng trong Swift account không phải là tên xác thực người dùng mà là chỉ khu vực lưu trữ

#### /account/container
Container storage location là khu vực lưu trữ tên người dùng đã xác định bên trong 1 account, là nơi lưu trữ metadata của chính container đó và danh sách các object bên trong container

#### /account/container/object
Object storage location là nơi lưu trữ dữ liệu của object đó và metadata. Các phần này được nối với nhau để tạo nên các location, tên của container và object không cần phải là duy nhất trong cluster. 

<a name="21"></a>
### 2.1 Account:
- Account là nơi lưu trữ gốc của dữ liệu. Đôi khi được so sánh với filesystem volume, account được đặt tên duy nhất trong hệ thống. Mỗi account có 1 database để lưu trữ metadata của account và danh sách tất cả các container trong account. Header của metadata được lưu trữ như là key-value (name và value) được đảm bảo như tất cả dữ liệu của Swift
 
- Các database của account không chứa object hoặc object metadata. Account database cho phép khi người dùng yêu cầu vào metadata của account và danh sách các container trong account
 
- Account được thiết kể để cho phép 1 người dùng đơn lẻ hay đa người dùng có thể truy cập dựa vào quyền hạn được đặt. Đó là 1 cái chung để nhìn được single-user truy cập bởi 1 người dùng cá nhân hoặc dịch vụ của account như "backup" và những muilti-access được yêu cầu truy cập vào account. Người dùng được phân quyền có thể tạo, chỉnh sửa, xóa container hoặc object trong account 
 
- Nhớ rằng trong Swift, 1 account không phải là tên xác thực người dùng mà là account storage location. Người dùng cần phải phân biệt được rõ Swift account (storage) và Swift user (identity)
 
<a name="22"></a>
### 2.2 Container:
- Container là đoạn chứa thông tin người dùng đã được xác định của account namespace cung cấp nơi lưu trữ object.Các container không thể lồng vào nhau (container không nằm trong container kia được), container được định nghĩa tương tự như kiểu 1 thư mục trong filesystem. Nó cũng là 1 cấp lưu trữ trong storage location (/account/container). Container không cần phải là 1 tên duy nhất, có thể có các container giống nhau tại các account khác nhau (/AccountA/Container1 và /AccountB/Container1). Không có giới hạn về số lượng các container bên trong 1 account 
 
- Tương tự như với account thì container cũng có database. Database của container chứa metadata của container và bản ghi của mỗi object chứa trong container. Header metadata của container được lưu trữ như là key-value với sự đảm bảo tương tự như là dữ liệu của Swift. Ví dụ của metadata container là X-Container-Read header => nó cũng cấp thông tin những người có quyền đọc object trong container
 
- Tương tự như database của account thì database của container không chứa object hoặc object của metadata. Database container cho phép người dùng truy cập vào để liệt kê danh sách object và metadata của container

- Bên trong 1 account, ta có thể tạo ra và sử dụng các container để nhóm dữ liệu lại theo 1 cách logic. Người dùng có thể đính kèm các metadata vào container và sử dụng 1 số tính năng của Swift để cung cấp cho container các đặc tính khác nhau để sử dụng cho mục đích khác nhau

<a name="23"></a>
- Objects là các dữ liệu được lưu trữ như là ảnh, videos, documents, log files, database backups, filesystem snapshots, hoặc bất kì 1 loại dữ liệu gì. Mỗi object thường chứa dữ liệu của nó và metadata

- Metadata header được lưu trữ như là key-value với sự đảm bảo tương tự như dữ liệu của object và yêu cầu latency không được tăng khi lấy ra. Metadata có khả năng cũng cấp những thông tin hữu ích của object. Ví dụ nhà sản xuất video có thể lưu định dạng video, thời gian, nội dung của video.

- Mọi object đều thuộc về container nghĩa là object luôn nằm trong các container. Khi object được lưu trong cluster, người dùng phải quy chiếu vào nơi lưu trữ ví dụ như account/container/object. Không có giới hạn về object trong container

- Theo quan điểm của người dùng, object storage location là nơi có thể thấy object. Swift lưu trữ nhiều bản copies của mỗi object trên cluster để đảm bảo độ bền và tính sẵn sàng của dữ liệu. Swift thực hiện đặt các object vào 1 logical group gọi là partition, partition này không phải là phân vùng ổ đĩa cứng. Một partition sẽ map tới nhiều ổ cứng, mỗi ổ cứng đó sẽ có 1 bản ghi object.

- Chương sau sẽ thảo luận 1 cách chi tiết hơn về các cơ chế ở bên trong. Quan trọng của chương này là người dùng truy cập vào object thông qua vị trí lưu trữ.

<a name="3"></a>
## 3. Kiến trúc của Swift:
- Swift data (/account/container/object) là tài nguyên lưu trữ sau cùng trên phần cứng. Những máy chạy các tiến trình của Swift được xem như là 1 node. Một cluster là tập hợp các nodes chạy đầy đủ các tiến trình và dịch vụ để tạo thành 1 hệ thống máy tính phân tán

- Để đảm bảo độ bền và tách được những hỏng hóc xảy ra cần phải tổ chức các nodes trong cluster thành các regions và zones

<a name="31"></a>
### 3.1 Regions

<img src="http://i.imgur.com/0llUYyY.png">
- Swift cho phép những phần của cluster được tách ra riêng biệt và được định nghĩa là Regions, các regions được định rõ bởi giới hạn địa lý. Ví dụ, một regions mới có thể được miêu tả như 1 nơi để đặt các nodes trong cluster.

- Mỗi cluster có tối thiểu là 1 regions nghĩa là tất cả các nodes trong cluster đều thuộc 1 region. Gọi là single-region cluster

- Cluster có 2 hay nhiều region gọi là multi-region cluster (MRC)

- MRC có những các xử lý nhất định để đọc và viết. Khi nhận yêu cầu đọc, Swift sẽ ưu tiên bản sao của dữ liệu ở gần nhất cho tốc độ đọc nhanh hơn (được đo bằng latency). Điều này được gọi là *read affinity*

- Swift cũng cung cấp *write affinity*, theo mặc định thì Swift sẽ cố gắng ghi đồng thời dữ liệu vào MRC. Write affinity cho phép Swift ưu tiên ghi dữ liệu vào những region có latency thấp do đó ghi và chuyển dữ liệu sẽ có phàn hồi nhanh hơn. Trường hợp mà kết nối có latency cao thì Swift bật write affinity cho phép ghi vào những lựa chọn quen thuộc, mỗi yêu cầu ghi tạo ra số lượng cần thiết những bản sao lưu dữ liệu

<a name="32"></a>
### 3.2 Zones
- Bên trong region là các zones, Swift cho phép các zones sẵn có được cấu hình để cô lập lỗi, zones sẵn có nên được xác định bởi các phần cứng riêng biệt để cô lập lỗi từ các zones khác. Trong mô hình triển khai lớn, zone có thể được xác định là các cơ sở vật chất riêng biệt trong các data center, được ngăn cách bởi các firewall và được cung cấp bởi các nhà cung cấp khác nhau

- Trong mô hình triển khai 1 data center, zone là các tủ đựng server khác nhau, 1 cluster thường có nhiều zones

<a name="33"></a>
### 3.3 Nodes
- Là các server vật lý chạy 1 hay nhiều tiến trình của Swift

- Tiến trình của Swift server là proxy, account, container, object. Các node chạy account, container sẽ lưu trữ account, container data và metadata. Node chạy object sẽ lưu trữ object và metadata của nó

- Các nodes chạy tất cả những tiến trình cần thiết của Swift để tạo thành hệ thống máy tính phân tán gọi là cluster

- Có nhiều cách để group các nodes trong cluster 1 cách logic như là dựa vào tiêu chí phần cứng hoặc storage policies

<a name="34"></a>
### 3.4 Storage policies:

<img src="http://i.imgur.com/wDNPyEB.png">
- Là cách mà nhà triển khai Swift xác định không gian bên trong cluster để tùy biến theo nhiều cách. Bao gồm về việc xác định vị trí, replication, phần cứng, partition để đáp ứng nhu cầu lưu trữ cụ thể

- Là 1 công cụ mạnh mẽ và linh hoạt, storage policies thiết lập ở mức độ object và thực hiện ở mức độ container

- Storage policies làm tăng tính linh hoạt của Swift object storage, nhà vận hành có thẻ tạo ra các policies

- Ví dụ, 1 policies có thể phân phối dữ liệu thông qua các region, có thể phân phối bản sao trên 1 zone duy nhất. Có thể triển khai chỉ trên phương tiện lưu trữ nhanh như ổ SSD

<a name="4"></a>
## 4. Các tiến trình của Server
- Swift chạy nhiều tiến trình để xử lý dữ liệu và mỗi node chạy 1 hoặc nhiều tiến trình. Swift muốn có nhiều nodes chạy mỗi tiến trình để đảm bảo độ bền và khả năng dự phòng.

- Các tiến trình chính như proxy, account, container, object
<ul>
<li>Quá trình chạy proxy trên 1 node gọi là proxy node</li>
<li>Node chạy account, container, object lưu trữ các loại dữ liệu thường gọi là storage node</li>
<li>Storage node chạy 1 số dịch vụ trên đó để duy trì tính nhất quán của dữ liệu</li>
</ul>

- Các tiến trình chia ra các layer như proxy layer, account layer, container layer, object layer

<a name="41"></a>
### 4.1 Proxy layer
- Là thành phần duy nhất của Swift cluster giao tiếp với các client bên ngoài

- Nhiệm vụ của layer này là xác định tọa độ các yêu cầu đọc và ghi từ client và thực hiện đọc ghi 1 cách đảm bảo cho hệ thống

- Ví dụ: Client gửi yêu cầu ghi cho object tới Swift. Proxy server xác định nodes lưu trữ object và gửi dữ liệu đến tiến trình object trên các nodes kiêm nghiệm. Nếu những node đó unvaliable thì tiến trình proxy server sẽ chọn 1 node thích hợp nhất để ghi tới đó. Khi hầu hết các phản hồi lại từ tiến trình object server là thành công, proxy server sẽ phản hồi lại đoạn code báo việc ghi thành công tới client.

- Hầu hết các bản tin đến hoặc từ tiến trình proxy đều sử dụng chuẩn HTTP verb (GET, PUT, POST, DELETE) và code phản hồi. Chương 4 sẽ thảo luận chi tiết hơn, tiến trình proxy cũng kiểm soát lỗi điều phối về mặt thời gian. Nó sử dụng kiến trúc share-nothing vì vậy có thể mở rộng quy mô tùy vào lượng công việc cần giải quyết.

<a name="42"></a>
### 4.2 Account layer
- Tiến trình account server cung cấp metadata cho các account cá nhân và danh sách container trong một account.

- Nó lưu trữ các dữ liệu đó trong SQLite database trên các ổ đĩa. 

- Tương tự dữ liệu trong Swift, các bản sao dữ liệu của account được tạo và phân phối trên cluster

<a name="43"></a>
### 4.3 Container layer
- Tiến trình container ser quản lý container metadata và danh sách  object trong mỗi container. Server sẽ không biết được object đó nằm ở đâu, chỉ biết object đang nằm trong container

- Danh sách object đó được lưu trong SQLite database

- Tương tự với dữ liệu của Swift, bản sao dữ liệu của container được thực hiện và phân phối trên cụm cluster

- Tiến trình container sẽ thống kê và theo dõi tổng số object và thổng dung lượng đã sử dụng của container

<a name="44"></a>
### 4.4 Object layer
- Tiến trình object cung cấp các dịch vụ lưu trữ có thể lưu, xóa, truy xuất các object trên ổ cứng của node

- Object được lưu thành các file nhị phân trên ổ cứng và sử dụng 1 đường dẫn chứa partition, cũng như mốc thời gian của từng dữ liệu, điều này cho phép tiến trình object server lưu trữ nhiều phiên bản của object trong khi phục vụ phiên bản mới nhất

- Metadata của 1 object được lưu trong phần thuộc tính mở rộng của tập tin (xattrs), hỗ trợ hầu hết filesystem hiện nay. Nó được thiết kế cho phép dữ liệu object và metadata được lưu trữ cạnh nhau và được sao chép như 1 đơn vị duy nhất

<a name="5"></a>
## 5. Các tiến trình thống nhất
- Lưu trữ dữ liệu trên đĩa và cung cấp API không khó, việc khó là quản lý lỗi. Các tiến trình thống nhất (Consistent process) đảm bảo việc tìm và sửa lỗi khi dữ liệu mất mát và phần cứng bị hỏng. Đây là lí do dữ liệu trong Swift bền vững.

- Nhiều tiến trình thống nhất chạy ngầm để đảm bảo tính toàn vẹn và sẵn sàng của dữ liệu. Các tiến trình thống nhất chạy ngầm trên các Storage nodes, tiến trình proxy không liên kết với các tiến trình thống nhất do không phải lưu trữ dữ liệu.

- Đặc trưng của các tiến trình thống nhất là chạy dựa trên các server process như account process, container process, object process.
<ul>
<li>Account server process được hỗ trợ bởi các tiến trình thống nhất như account auditor, replicator, reaper</li>
<li>Container server process được hỗ trợ bởi các thành phần container auditor, container replicator, container updater và container sync</li>
<li>Object server process hỗ trợ bởi object auditor, object replicator, object updater và object expirer</li>
</ul>

- Auditor và replicator là 2 tiến trình chính sử dụng cho 3 server process (account, container, object). Updater được thể hiện cho container hoặc object server process. Các thành phần còn lại của tiến trình thống nhất là đặc trưng cho 1 server process

<a name="51"></a>
### 5.1 Auditor
- Auditor chạy ngầm trên tất cả các storage nodes

- Nếu account, container, object server chạy trên 1 node thì sẽ có auditor tương ứng

- Account, container, object auditor liên tục quét trên đĩa để đảm bảo dữ liệu không bị hỏng. Nếu có lỗi, auditor chuyển dữ liệu hỏng đó sang khu vực khác

<a name="52"></a>
- Replicator đảm bảo đủ các bản sao mới nhất của dữ liệu trong cluster. Cho phép cluster ở trong trạng thái nhất quán khi đối mặt với các lỗi như mất mạng, hỏng ổ đĩa

- Các tiến trình account, container, object replicator chạy các dịch vụ tương ứng

- Replicator liên tục kiểm tra với các nodes local của mình và so sánh account, container, object với bản sao trên *remote nodes* trong cluster. Nếu 1 trong các remote nodes bị hỏng hoặc dữ liệu ở đó cũ, replicator sẽ đẩy bản local copy sang để thay thế hoặc update dữ liệu gọi là *replication update*. Lưu ý là replicator chỉ đẩy dữ liệu local tới remote nodes, nó sẽ không lấy bản sao dữ liệu từ remote nodes khi dữ liệu local của nó mất hoặc quá cũ

- Replicator kiểm soát xóa object và container bằng việc tạo ra các *tombstone file* (1 zero-byte file với tên có đuôi .ts) là phiên bản mới nhất của object đó. Replicator sẽ đẩy tombstone file tới các bản sao khác và object sẽ được xóa khỏi toàn bộ hệ thống.

- Việc xóa container yêu cầu container đó không được chứa object nào ở trong. Container database sau đó sẽ được đánh dấu là đã xóa và replicator đẩy phiên bản đó ra, đến đây là container đã bị xóa khỏi cluster

<a name="53"></a>
### 5.3 Account reaper
- Một yêu cầu xóa account sẽ đặt account đó trong trạng thái xóa khi nó không còn được sử dụng nữa. Khi reaper xác định account được đánh dấu là đã xóa, nó sẽ loại bỏ hết container object thuộc về account đó và cuối cùng là xóa bản ghi account đó

- Nó cung cấp 1 buffer chống lỗi, reaper được cấu hình để chờ đợi trong 1 khoảng thời gian nhất định trước khi xóa dữ liệu

<a name="54"></a>
### 5.4 Container và object updater
- Tiến trình thống nhất container updater chịu trách nhiệm giữ danh sách các container của account trong 1 kì hạn nhất định. Nó cập nhất số lượng container, số lượng object và những bytes đã sử dụng trong metadata của account

- Tiến trình thống nhất object updater cũng chịu trách nhiệm đảm bảo danh sách object trong container. Tuy nhiên object updater được chạy như là phần thừa. Object updater chịu trách nhiệm cập nhật danh sách object trong container. Nếu không làm như vậy, updater sẽ cố gắng thực hiện cập nhật danh sách container như là số lượng object và bytes sử dụng trong container metadata

<a name="55"></a>
### 5.5 Object expirer
Object expiration được thiết kế để object tự động xóa trong 1 khoảng thời gian xác định. Process này được sử dụng để thanh lọc dữ liệu đã được chỉ định

<a name="6"></a>
## 6. Locating the data:
- Để các thành phần và dịch vụ tìm được các mảnh dữ liệu trên cluster, các tiến trình sẽ tới bản sao local của Rings và tìm vị trí của dữ liệu đó trên account rings, container rings, object rings. Swift lưu trữ các bản sao của dữ liệu được lưu trữ ở đâu là kết quả của việc tìm kiếm nhiều vị trí

- Ring là gì? Ring là 1 bảng tra cứu phân phối cho các node trong cluster. Swift sử dụng phiên bản hashing (băm) thích hợp cho Ring. Bắt đầu tìm hiểu với hash function

- Một ví dụ thực tế của hàm băm khi ta sử dụng từ điển bách khoa toàn thư, khi tìm thông tin trong từ điển bạn chỉ cần rút ngắn thông tin bạn cần tìm trong từ điển chỉ trong vài từ (form cơ bản của hàm băm)

<a name-"61"></a>
### 6.1 Ring Basics: Hash Functions
- Hàm băm cơ bản được sử dụng với chức năng xác định nơi lưu trữ các object. Ví dụ, khi ta tìm thông tin trong từ điển bách khoa toàn thư, cần chọn ra các cuốn cần tìm, tìm chữ liên quan. Ta cần những hàm băm cụ thể hơn để tìm dữ liệu trên nhiều ổ cứng

- Hàm băm có thể được xem như là phương pháp tham chiếu ngắn hơn với dữ liệu, thay vì phải mất 1 đoạn dài  và điều quan trọng của phương pháp này là nó trả về các kết quả tương tự

- Một phương pháp tương đối đơn giản trong việc xác định nơi để lưu trữ object sử dụng thuật toán MD5 để băm vị trí của object được lưu trữ sau đó lấy giá trị băm chia cho giá trị băm của số lượng ổ đĩa lưu trữ để lấy ra phần dư. Ví dụ, vị trí lưu trữ object là /account/container/object và sử dụng 4 ổ cứng để lưu trữ đặt tên từ driver 0 đến driver 3. 

- Sử dụng thuật toán MD5 để lấy hàm băm của vị trí lưu trữ
```sh
md5 -s /account/container/object
MD5 ("/account/container/object") = f9db0f833f1545be2e40f387d6c271de
```
Sau đó convert dãy số hexadecimal sang decimal rồi chia cho giá trị băm số lượng ổ
```sh
332115198597019796159838990710599741918 % 4 = 2
```
Được phần dư là 2 vậy object sẽ nằm ở driver 2
```sh
CHÚ Ý: Nếu thuật toán chia hết và phần dư bằng 0 thì object sẽ nằm trên driver 0
```

- Hạn chế lớn nhất của hàm băm là tính toán phụ thuộc vào chia số lượng ổ đĩa. Khi thêm hoặc bớt ổ cứng, object sẽ map tới các driver khác. Ví dụ dưới đây sẽ chứng minh:
```sh
Total drive count Remainder Maps to
6 (hash) % 6 = 4 Drive 4
7 (hash) % 7 = 6 Drive 6
8 (hash) % 8 = 6 Drive 6
9 (hash) % 9 = 1 Drive 1
10 (hash) % 10 = 8 Drive 8
```

- Hầu hết object sẽ chuyển tới ổ cứng khác khi 1 ổ cứng mới thêm vào hoặc bỏ đi, do đó cần tính toán lại tất cả dữ liệu trong cluster dẫn tới cluster sẽ cần phải dành nhiều tài nguyên cho việc di chuyển của object tạo gánh nặng về đường truyền mạng và những dữ liệu sẽ không khả dụng

<a name="62"></a>
### 6.2 Ring Basic: Hàm băm phù hợp
<img src="http://i.imgur.com/WQILEiu.png">
- Hàm băm phù hợp giúp cho số lượng object di chuyenr khi thêm hoặc bớt ổ cứng là nhỏ nhất. Thay vì mapping trực tiếp mỗi giá trị cho 1 ổ cứng, sẽ có 1 loạt giá trị liên kết với 1 ổ cứng. Điều này thực hiện bằng cách mapping các giá trị băm có thể xảy ra xung quanh 1 vòng tròn. Mỗi ổ cứng sau đó được gán tới 1 điểm trên vòng tròn dựa vào 1 giá trị băm ổ cứng

- Các phương pháp khác nhau có thể sử dụng (băm IP, băm tên ổ) kết quả là tất cả ổ đĩa được đặt theo 1 trật tự ngẫu nhiên trên vòng tròn

- Khi object cần được lưu trữ, hàm băm của object được xác định và định vị trên vòng tròn. Hệ thống tìm theo chiều kim đồng hồ để xác định ổ đĩa gần nhất, đây là ổ đĩa gần nhất mà object được đặt

- Với tròng tròn băm, có thể thêm hoặc bớt ổ cứng mà chỉ 1 vài object bị di chuyển. Khi thêm 1 ổ cứng, ổ cứng kế tiếp theo chiều kim đồng hồ sẽ mất đi bất kì object nào ở ổ cũ sang ổ mới, những phần còn lại sẽ được giữ nguyên => hiệu quả hơn việc hầu hết object bị di chuyển
<img src="http://i.imgur.com/JCfSvgQ.png">

- Thực tế có nhiều điểm được đánh dấu trên vòng tròn cho mỗi ổ đĩa. Hầu hết vòng tròn băm phù hợp sẽ có nhiều điểm được đánh dấu. Có thể là hàng trăm. Có nhiều điểm đánh dấu nghĩa là ổ đĩa được map tới nhiều khoảng giá trị băm nhỏ thay vì 1 cái lớn hơn
<img src="http://i.imgur.com/QTQLFYd.png">

<a name="63"></a>
### 6.3 The Rings: Modified Consistent Hashing Ring
- Swift sử dụng hàm băm sửa đổi phù hợp. Sự sửa đổi tạo ra 1 partition băm thống nhất sử dụng replica count, replica lock và các cơ chế phân phối dữ liệu như weight và vị trí duy nhất có thể sử dụng để xác định Swift rings cá nhân

- Một account ring sẽ được tạo ra cho 1 cluster và được sử dụng để xác định vị trí account, tương tự với container. Một storage policies object ring sẽ được tạo cho mỗi storage policies được sử dụng để xác định nơi lưu dữ liệu object

<a name="631"></a>
#### 6.3.1 Partition: 
- Với hàm băm thống nhất có nhiều khoảng băm nhỏ có thể nhỏ hơn hoặc lớn hơn khi thêm hoặc bớt ổ, việc đó dẫn đến các object không sẵn có nữa khi nó bị di chuyển. Để ngăn chặn điều này Swift tiếp cận vòng băm khác nhau. Mặc dù ring vẫn chia thành các khoảng băm sẽ có kích thước giống nhau và không thay đổi về số lượng

- Những partition được fix cố định sau đó gán cho ổ đĩa sử dụng 1 thuật toán sắp xếp

- Cần có cái nhìn sâu sắc về việc làm cách nào partition được tính toán và làm thế nào để ổ đĩa đc gán 

<a name="632"></a>
#### 6.3.2 Partition power 
- Tổng số partition tồn tại trong cluster được tính toán sử dụng partition power. Một số nguyên ngẫu nhiên được chọn trong quá trình tạo cluster
```sh
Tổng số partition trong cluster=2^partition power
```
- Ví dụ: Partition power là 15, số lượng partition sẽ là 2^15=32768. Vậy 32768 partition sẽ được map tới các drivers khả dụng. Số lượng ổ có thể thay đổi nhưng partition thì không bị thay đổi

<a name="633"></a>
#### 6.3.3 Replica count
- Khi built Swift ring, replica count là giá trị có bao nhiêu bản sao của partition trong cluster. Ví dụ, bạn có replica count=3, nghĩa là mỗi partition được nhân bản lên tổng là 3 bản sao. Mỗi bản sao được lưu trong các thiết bị khác nhau trong cluster. Khi 1 object được đặt vào trong cluster, giá trị băm sẽ match vào 1 partition và được sao chép vào 3 vị trí của partition

- Càng nhiều bản replicates thì dữ liệu sẽ được bảo vệ tốt hơn khi có lỗi (đặc biệt là khi phân tán các bản sao ra các DC khác hoặc region khác). Replica count là số thực, hầu hết là các số nguyên như 3.0. Trong trường hợp ngoại lệ như 3.15 nghĩa là 15% các ổ sẽ có thêm 1 bản sao trên tổng số 4. Lý do chính là bạn có thể thực hiện thay đổi dần từ bản sao số nguyên này sang 1 bản sao số nguyên khác. Điều này cho phép thời gian Swift thực hiện sao chép thêm dữ liệu mà không gây bão mạng

- Partition nhân rộng định rõ hand-off drivers. Nghĩa là khi ổ đĩa hỏng, các tiến trình replication/auditing sẽ thông báo và đẩy dữ liệu vào hand-off location, làm giảm đáng kể thời gian sửa chữa so với RAID và three-way mirror

- Xác suất tất cả các partition nhân rộng đều bị hỏng và dữ liệu bị đẩy ra là rất thấp. Đó là lí do Swift bền vững. Tùy thuộc vào 

















 