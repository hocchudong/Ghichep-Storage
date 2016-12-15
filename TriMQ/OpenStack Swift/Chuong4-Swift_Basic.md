# Chương 4: Swift Basics

# Mục lục:
- [1. Giao tiếp với Cluster: Swift API](#1)
- [2. Gửi yêu cầu](#2)
- [3. Ủy quyền và thực hiện](#3)
- [4. Nhận phản hồi](#4)
- [5. Các tool giao tiếp](#5)
- [6. Các ví dụ](#6)
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

- Command-Line Interfaces
Chúng ta sẽ xem xét 2 câu lệnh cURL vs Swift. Cả 2 câu lệnh đều cho phép người dùng gửi yêu cầu 1 dòng 1 lần tới các Swift Cluster. 





	
	
	



