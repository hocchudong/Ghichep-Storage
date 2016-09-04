#Hoạt động của SMB/CIFS
##Mục lục:
###[1. Tổng quan về SMB/CIFS](#tongquan)

###[2. Hoạt động của SMB/CIFS](#hoatdong)

[2.1 Đặc điểm của giao thức](#dacdiem)

[2.2 User/share level security](#security)

[2.3 Package Header](#header)

[2.4 Package sequence walk throught](#work)

###[3. Ứng dụng của SMB/CIFS](#ungdung)

###[4. Tài liệu tham khảo](#thamkhao)

==============================================

<a name="tongquan">
##1. Tổng quan về SMB/CIFS
Là 1 giao thức mạng cho phép chia sẻ file giữa các network nodes. Giao thức dựa trên mô hình client/server, ở đó client sẽ các package đến server và server sẽ respond lại package. Mỗi package được gửi đi sẽ có header chứa thông tin về package đó. Mỗi package cũng chứa command field biểu thị mục đích của package đó như: login, openfile, read from a file, write file.

<a name="hoatdong">
##2. Hoạt động của SMB/CIFS:

<img src="https://www.samba.org/cifs/docs/images/img00002-new.jpg">

<a name="dacdiem">
###2.1 Đặc điểm của giao thức:
<img src="http://i.imgur.com/47BBrkX.jpg">
- <b>Client/Server</b> (request/respond):
Kiến trúc của SMB/CIFS dựa vào mô hình client/server. Về cơ bản, client sẽ gửi yêu cầu đến server, sau đó server sẽ respond lại yêu cầu đó.

Giao thức cho phép gửi nhiều request 1 lúc được thực hiện thông qua multiplex ID (MID). Client đảm bảo rằng mội request đều có 1 MID riêng, dựa vào MID mà client sẽ biết được request có được phản hồi lại hay không

- <b>Command based</b>: Mỗi package chứa 1 byte command field, các phản hồi từ server sẽ có các command code giống như các request.

- <b>Protocol negotiation</b>: Khi 1 client muốn truy cập 1 file trên server từ xa, đầu tiên client sẽ gửi 1 package yêu cầu 1 phiên làm việc. Package này chứa các thông tin về phương thức làm việc. Khi server phản hồi lại rằng nó hiểu thông tin được đưa ra và bắt đầu phiên làm việc.

<a name="security">
###2.2 User/share level security:
Khi 1 file được chia sẻ hoặc người dùng muốn truy cập vào file thì sẽ có những chính sách bảo mật

- <b>User security</b>: Người dùng muốn truy cập vào file cần cung cấp username, password để server có thể kiểm soát truy cập
- <b>Share security</b>: File được chia sẻ sẽ có những mức độ bảo mật, phân quyền riêng do admin cài đặt, người dùng có thể xem, đọc, sửa xóa file.
- <b>Encryption</b>: Mật khẩu được gửi tới ser ver sẽ được mã hóa theo 2 cách:
<ul>
<li>NT style</li>
<li>LAN style</li>
</ul>

<a name="header">
###2.3 Package header
<img src="http://i.imgur.com/DWXFWWk.png">

- <b>Header</b>: Mỗi package chứa 4 byte header
- <b>Command</b>: Chứa 1 byte thể hiện loại package như:
```sh
SMB_COM_TREE_CONNECT
SMB_COM_NEGOTIATE
```
- <b>Erro class</b>: Server sẽ thể hiện rằng request có yêu cầu thành công hay không. Nếu có lỗi thì sẽ có các dạng lỗi sau:
<ul>
<li>ERR DOS(0x01): Lỗi từ Core DOS</li>
<li>ERR SRV(0x02): Lỗi quản lý file</li>
<li>ERR HRD(0x03): Lỗi phần cứng</li>
<li>ERR CMD(0xFF): Command không phải dạng SMB</li>
</ul>

<a name"work">
###2.4 Package sequence walk throught:
Thể hiện gói tin được gửi từ client đến server và ngược lại.
<img src="https://richardkok.files.wordpress.com/2011/02/01-ntlm1.jpg?w=595">

Bảng thể hiện 1 phiên làm việc giữa client/server:
<img src="http://i.imgur.com/0ZwLwPs.png">
```sh
CL: client
FS: Server
```

<a name="ungdung">
##3. Ứng dụng của SMB/CIFS:
- SMB/CIFS được sử dụng để chia sẻ file giữa Window và Linux, người dùng có thể thao tác với file từ xa như trên local
- CIFS hỗ trợ thêm các tính năng như in qua mạng, xử lý các thuộc tính mở rộng của file, hỗ trợ unicode, bổ sung Hypertext Transfer Protocol(HTTP)...
- CIFS thường sử dụng trong các công ty lớn, cần xử lí nhiều dữ liệu, nhiều người truy cập sử dụng

<a name="thamkhao">
##4. Tài liệu tham khảo:

####Cấu hình chia sẽ file giữa Linux và Window tham khảo tại đây:
- https://github.com/hocchudong/Ghichep-Storage/blob/master/TriMQ/share%20file%20window-linux.md
- https://github.com/trimq/vmwareee/blob/master/labsmb.md

####Tài liệu:
- https://en.wikipedia.org/wiki/Server_Message_Block#SMB_3.0
- https://www.samba.org/cifs/docs/what-is-smb.html
- http://media.server276.com/codefx/CIFS_Explained.pdf






