#Sử dụng Wireshark để bắt gói tin trong giao thức NFS
NFS (Network File System) là 1 hệ thống cung cấp dịch vụ chia sẻ file trong hệ thống Linux và Unix, hoạt động theo dạng client/server.
####1. Một số đặc điểm của giao thức NFS:
- NFS cho phép người dùng có thể mount từ xa, người dùng có thể mount thư mục từ máy khác về sử dụng.
- NFS hoạt động theo mô hình client/server, sử dụng RPC để định tuyến request từ Client đến Server.
- NFS hiện tại có 3 phiên bản là NFSv2, NFSv3, NFSv4
- Sử dụng mô hình OSI
- Do sử dụng RPC để định tuyến nên NFS sử dụng các port 2049 và 111 cả trên client và server.

##2. Kiếm tra hoạt động của NFS với phần mềm Wireshark:
Để kiểm tra hoạt động của giao thức NFS, tôi sẽ sử dụng phần mềm Wireshark để bắt gói tin từ client đến server hoặc ngược lại
- Bắt gói tin sử dụng giao thức NFS:
<img src="http://i.imgur.com/dWiixNC.png">
<ul>
<li>Gói tin đi từ Server đến client với địa chỉ IP nguồn (Src) là 172.16.69.30 tới địa chỉ IP đích (Dst) 172.16.69.31</li>
<li>Sử dụng giao thức TCP (do NFS sử dụng mô hình truyền tin OSI)</li>
</ul>
<img src="http://i.imgur.com/FDnuk9t.png">
- Kiểm tra phiên bản NFS:
<img src="http://i.imgur.com/KnyryWA.png">
Ở đây là phiên bản NFSv4
- Kiểm tra port:
<img src="http://i.imgur.com/VFmEuNS.png">

##3. Kiếm tra hoạt động của NFS với lệnh tcpdump:
- Sử dụng tcpdump qua port:
```sh
tcpdump -i eth1 port 2049
```


