# Chương 4: Swift Basics

# Mục lục:
- [1. Giao tiếp với Cluster: Swift API](#1)
- [2. Gửi yêu cầu](#2)
- [3. Ủy quyền và thực hiện](#3)
- [4. Nhận phản hồi](#4)
- [5. Các tool giao tiếp](#5)
	- [5.1 Câu lệnh (CLI)](#51)
	- [5.2 Ứng dụng client tùy chọn](#52)
- [6. Các ví dụ kịch bản](#6)
	- [6.1 Uploading (PUT)](#61)
	- [6.2 Downloading (GET)](#62)
- [7. Tài liệu tham khảo](#7)

===========================

<a name="1"></a>
## 1. Giao tiếp với Cluster: Swift API
- Swift xử lý rất nhiều hành động, những yêu cầu gửi tới để ghi vào các node, những yêu cầu lấy dữ liệu khỏi cluster. Ngoài ra còn có các tiến trình server hay tiến trình thống nhất ==> đòi hỏi phải giao tiếp phối hợp

- Proxy server là thành phần duy nhất giao tiếp với client bên ngoài ==> chỉ có proxy server thực hiện Swift API

- API Swift là 1 bộ HTTP dựa trên các rules và vocabulary mà proxy server sử dụng để giao tiếp với client bên ngoài

- Sử dụng HTTP nghĩa là giao tiếp được hoàn thành trong 1 form request-respond. Mỗi request là 1 mong muốn cụ thể (ví dụ upload 1 object), được biểu hiện bằng HTTP verb. Mỗi phản hồi trả về có mã ( vd: 200 OK) cho thấy những gì đã xảy ra với yêu cầu

- Vì proxy server giao tiếp trong HTTP, người dùng nên chú ý tới khi giao tiếp: Gửi yêu cầu, ủy quyền và thực hiện, phản hồi


<a name="2"></a>
## 2. Gửi yêu cầu
Nếu 1 yêu cầu được gửi đến cluster, nội dung cần có các thành phần như sau:
<ul>
<li>Storage URL</li>
<li>Thông tin ủy quyền</li>
<li>HTTP Verb</li>
<li>Optional: Dữ liệu hoặc metadata được ghi</li>
</ul>

<a name="21"></a>
### 2.1 Storage URL
Có 2 vai trò:
<ul>
<li>Yêu cầu tới cluster</li>
<li>Nơi các cluster yêu cầu được đặt</li>
</ul>

- Format của storage URL khá giống 1 websites. Ví dụ

```sh
 https://swift.example.com/v1/account/container/object
```

- 2 phần cơ bản của storage URL
<ul>
<li>Vị trí cluster (swift.example.com/v1/): Phần đầu của storage URL là 1 endpoint trong cluster. Nó được sử dụng bởi mạng sẽ định tuyến yêu cầu tới node với 1 proxy process bởi vậy request có thể được kiểm soát</li>
<li>Vị trí lưu trữ: bảo gồm 1 hay nhiều thành phần làm nên sự duy nhất cho vị trí của dữ liệu, có 3 format tùy vào tài nguyên yêu cầu</li>
</ul>

- <b>Chú ý</b>: Trong Swift các object có thể chứa kí tự "/" việc này có nghĩa là các object có thể lồng vào nhau

<a name="22"></a>
### 2.2 Ủy quyền
- Việc đầu tiên Swift thực hiện với 1 request là xác thực người gửi. Ủy quyền được hoàn thành bởi so sánh thông tin người cung cấp và thông tin xác thực mà Swift sử dụng

- Có 2 cách xác thực
<ul>
<li>Thông qua thông tin xác thực với mỗi lần gửi yêu cầu</li>
<li>Thông qua xác thực token bởi thực hiện 1 yêu càu xác thực đặc biệt trước khi thực hiện yêu cầu lưu trữ</li>
</ul>
Điều này cũng giống như việc bạn vào 1 công ty và phải đưa ra 1 ID để kiểm tra mỗi lần bạn vào và việc bạn có 1 huy hiệu xác thực có thể ra vào mà không cần đưa ID

<a name="23"></a>
### 2.3 HTTP verb
- Swift sử dụng HTTP verb cơ bản như sau:
<ul>
<li><b>GET</b>: Download object (với metadata), hoặc list các container, account.</li>
<li><b>PUT</b>: Upload object, tạo container hoặc ghi đè vào metadata headers</li>
<li><b>POST</b>: Update metadata (account hoặc container), ghi đè metadata của object, tạo container nếu nó không tồn tại</li>
<li><b>DELETE</b>: Xóa object hoặc container trống</li>
<li><b>HEAD</b>: Lấy thông tin bao gồm metadata của account, container, object</li>
</ul>

Sau khi xác thực thành công, Swift sẽ xem xét các yêu cầu để xác định vị trí lưu trữ và thực hiện vì vậy có thể kiểm tra các yêu cầu được ủy quyền

<a name="3"></a>
## 3. Ủy quyền và thực hiện
- Mặc dù người dùng có thể có thông tin truy cập vào hệ thống Swift những sẽ có 1 số hành động không được ủy quyền. Nếu người dùng gửi yêu cầu. Các proxy server sẽ cần xác nhận ủy quyền trước khi cho phép yêu cầu

- Ví dụ: Nếu bản gửi 1 yêu cầu với thông tin hợp lệ, nhưng cố gắng thêm dữ liệu vào 1 account khác thì bạn vẫn sẽ được xác thực nhưng sẽ bị từ chối yêu cầu. Nếu các hành động là được phép, các tiến trình máy chỉ proxy sẽ gọi đúng các node được yêu cầu, các node sẽ trả về kết quả của yêu cầu, và proxy sẽ trả về các phản hồi HTTP.

<a name="4"></a>
## 4. Nhận phản hồi
- Các phản hồi từ Swift cluster sẽ chứa 2, hoặc thường là 3 các thành phần sau:
<ul>
<li>Mã phản hồi và mô tả</li>
<li>Header</li>
<li>Data</li>
</ul>

- Mã phản hồi và mô tả sẽ cho bạn biết biết yêu cầu của bạn được hoàn thành. Rộng ra có 5 mẫu mã phản hồi
<ul>
<li>1xx(thông tin): ví dụ 100 continue</li>
<li>2xx(thành công): ví dụ 200 OK hoặc 201 created</li>
<li>3xx(chuyển hướng): ví dụ 300 multiple choices hoặc 301 Moved Permanently</li>
<li>4xx(lỗi người dùng): ví dụ 400 bad request hoặc 401 là unauthorized</li>
<li>5xx(lỗi server): 500  Internal Server Error</li>
</ul>

- Từ danh sách này ta có thể thấy mã 200 là dấu hiệu tốt thể hiện yêu cầu thành công. Các header đi kèm các phản hồi có thể cung cấp thêm thông tin, nếu yêu cầu là để GET dữ liệu, dữ liệu phải được trả về tốt. Như bạn có thể thấy mặc dù định dạng yêu cầu-phản hồi là khá đơn giản, nhưng có rấy nhiều thành phần xung quanh đó để có thể đảm bảo ban giao tiếp được với các cluster.

<a name="5"></a>
## 5. Các tool giao tiếp
- Người dùng và admin thường quan tâm về yêu cầu-phản hồi HTTP sử dụng ứng dụng client.

- Ứng dụng Swift client có thể ở nhiều dạng, từ commandline giao diện đồ họa.

- CLI là tất cả những gì bạn cần để thực hiện hoạt động đơn giản trên Swift cluster. Bởi vì ngôn ngữ tự nhiên của hệ thống Swift là HTTP, commande-lint tool như cURL là cách tốt nhất để giao tiếp vs Swift ở lớp thấp. Tuy nhiên, gửi nhiều yêu cầu HTTP cùng 1 lúc và giải nén các thông tin lên liên quan đến kết quả có thể có 1 chút vướng mắc. Vì lý do này mà mọi người thích sử dụng Swift ở tầng cao hơn. Như sử dụng thư viện client Swift thay vì làm việc với HTTP cơ bản

<a name="51"></a>
### 5.1 Command-Line Interfaces
Chúng ta sẽ xem xét 2 câu lệnh cURL vs Swift. Cả 2 câu lệnh đều cho phép người dùng gửi yêu cầu 1 dòng 1 lần tới các Swift Cluster.

#### Sử dụng cURL
- cURL là công cụ truyền dữ liệu tới hoặc từ server, sử dụng cú pháp câu lệnh là `curl`. Có thể được cài đặt sẵn trên hệ thống hoặc có thể tải về. Truyền dữ liệu thông qua HTTP verb

- Cú pháp câu lệnh:

```sh
curl -X <HTTP-verb> [...] <Storage-URL> <object.ext>
```

<ul>
<li>curl là cú pháp lệnh</li>
<li>-X là option cung cấp các HEAD verb</li>
<li><HTTP-verb là các động từ để yêu cầu vào hệ thống Swift</li>
<li>[...] là các thông tin xác thực, ở đây ghi tắt để câu lệnh có thể được nhìn dễ dàng hơn</li>
<li><Storage-URL địa chỉ nơi dữ liệu được yêu cầu</li>
</ul>

- HTTP-verb có thể là:
<ul>
<li>PUT: tạo vị trí lưu trữ mới</li>
<li>GET: list danh sách object, container có trong container hoặc trong account</li>
</ul>

#### Sử dụng câu lệnh swift
- Câu lệnh `swift` có thể có sẵn hoặc có thể tải về gói python-swiftclient để sử dụng

- Tương tự công cụ cURL sử dụng câu lệnh thì swift cũng sử dụng câu lệnh là `swift`

- Câu lệnh này phổ biến bởi cú pháp dễ nhớ và những HTTP-verb thân thiện hơn cURL. Tuy nhiên swift command có 1 số hạn chế vì  trong hệ thống Swift có 1 số HTTP request câu lệnh swift không thực hiện được

- Cú pháp câu lệnh

```sh
swift HTTP-verb [...] Storage-URL
```

- Các câu lệnh HTTP-verb là:
<ul>
<li>list là liệt kê các container, object có trong account hoặc container</li>
<li>download tải về các object</li>
</ul>

<a name="52"></a>
### 5.2 Ứng dụng client tùy chọn
- Mặc dù các câu lệnh được sử dụng đơn giản nhưng có nhiểu người muốn sử dụng những ứng dụng client phức tạp hơn với những tùy biến

- Các nhà phát triển ứng dụng có thể xây dựng yêu cầu HTTP và phân tích HTTP phản hồi sử dụng thư viện ngôn ngữ lập trình của họ. Hoặc có thể sử dụng thư viện Swift nguồn mở

- Các thư viện client nguồn mở cho Swift có sẵn những ngôn ngữ lập trình phổ biến như:
<ul>
<li>Python</li>
<li>Ruby</li>
<li>PHP</li>
<li>C#/.NET</li>
<li>Java</li>
</ul>

- Các thông tin thêm về phát triển Swift API sẽ được tìm thấy trong chương 6 và chương 7


<a name="6"></a>
## 6. Các ví dụ kịch bản
Xem xét 2 kịch bản tải lên và tải về sau đây sẽ giúp hiểu rõ hơn về cách Swift hoạt động

<a name="61"></a>
### 6.1 Uploading (PUT)
Client sử dụng Swift API để tạo yêu cầu HTTP PUT 1 object vào 1 container. Sau khi nhận yêu cầu PUT, tiến trình proxy server xác định vị trí dữ liệu hướng tới. Tên account, tên container, tên object, tất cả được sử dụng để xác định partion nơi mà object được lưu. 1 tra cứu trong rings thích hợp sẽ được sử dụng để map vị trí lưu trữ (/account/container/object) tới partion và với tập các nodes lưu trữ trong đó có mỗi bản sao của partion được phân công
Dữ liệu được gửi tới từng node lưu trữ, nơi mà có các partion thích hợp. Khi phần lớn dữ liệu được viết thành công, tiến trình máy chủ proxy có thể thông báo tới khách hàng quá trình upload đã thành công, VÍ dụ nếu bạn sử dụng 3 bản sao thì khi viết được 2 bản sao sẽ là thành công. Sau đó database container sẽ được cập nhật không đồng bộ để thể hiện các đối tượng mới trong container

<a name="62"></a>
## 6.2 Downloading (GET)
Một yêu cầu đến tiến trình máy chủ proxy  /account/container/object Sử dụng ring phù hợp để tra cứu, các partion của yêu cầu sẽ được xác định, cùng với tập hợp các node lưu trữ vùng đó. Mỗi yêu cầu sẽ được gửu đến các node lưu trữ để lấy tài nguyên. Sau khi yêu cầu trả về object thì tiến tình proxy sẽ trả về cho client.


<a name="7"></a>
## 7. Tài liệu tham khảo
- [1] Openstack Swift - Joe Arnold&members of the SwiftStack team
