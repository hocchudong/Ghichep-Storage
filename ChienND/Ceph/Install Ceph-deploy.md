#Install Ceph-Deploy

Mô hình Lab

<img src=http://i.imgur.com/ZfijguF.png>

<ul>
<li>2 node: ceph-1, ceph-2, ceph-3
<li>2 card mạng: eth0-172.16.69.0/24	eth1-10.10.10.0/24
<li>2 ổ cứng làm osd lưu trữ
<li>ceph-1 cài đặt dịch vụ ceph-deploy phục vụ cho việc triển khai cụm ceph.
</ul>

###A. Install trên ceph-1

- Sửa file hosts trên các node

vim /etc/hosts
```sh
172.16.69.11    ceph-1
172.16.69.12    ceph-2
172.16.69.13    ceph-3
```

- Đặt địa chỉ mạng tương ứng trên các node


vim /etc/network/interfaces

```sh
auto eth0
iface eth0 inet static
address 172.16.69.11
netmask 255.255.255.0
gateway 172.16.69.1
dns-nameservers 8.8.8.8

auto eth1
iface eth1 inet static
address 10.10.10.11
netmask 255.255.255.0
```

- Cài đặt SSH

```sh
sudo apt-get -y install openssh-server
echo "trusty ALL = (root) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ceph 
sudo chmod 440 /etc/sudoers.d/ceph 
```

- Tạo ssh key và gửi tới các node khác

```sh
ssh-keygen 
ssh-copy-id ceph-2
ssh-copy-id ceph-3
```

- Cài đặt dịch vụ Ceph-Deploy

`sudo apt-get -y install ceph-deploy ceph-common ceph-mds`

- Vào thư mục Ceph-Deploy

```sh
cd /etc/ceph 
```

- Tạo cụm Ceph

`ceph-deploy new ceph-1 ceph-2 ceph-3`

- Cài Ceph trên các node

`ceph-deploy install ceph-1 ceph-2 ceph-3`

- Cài đặt Ceph-Mon và tạo key

`ceph-deploy mon create-initial`

- Prepare ổ cứng

`ceph-deploy osd prepare ceph-1:/dev/sdb ceph-1:/dev/sdc ceph-2:/dev/sdb ceph-2:/dev/sdc ceph-3:/dev/sdb ceph-3:/dev/sdc`

- Check ceph

<img src=http://i.imgur.com/VNnTw7g.png>

- Configure Meta Data Server
```sh
ceph-deploy admin ceph-1 
ceph-deploy mds create ceph-1 
ceph mds stat 
```

- Tắt và bật lại các node. 


- Khi check **ceph -s** có cảnh báo `health HEALTH_WARN clock skew detected ....`

<img src=http://i.imgur.com/b7gZwPe.png>

Ta cài dịch vụ NTP để giải quyết.

- Cài gói chrony trên các node

`apt-get -y install chrony`

- Mở file /etc/chrony/chrony.conf trên ceph-1 và tìm các dòng dưới

```sh
server 0.debian.pool.ntp.org offline minpoll 8
server 1.debian.pool.ntp.org offline minpoll 8
server 2.debian.pool.ntp.org offline minpoll 8
server 3.debian.pool.ntp.org offline minpoll 8
```

- Thay bằng các dòng sau trên node ceph-1

```sh
server 1.vn.pool.ntp.org iburst
server 0.asia.pool.ntp.org iburst
server 3.asia.pool.ntp.org iburst
```

- Thay bằng các dòng sau trên node ceph-2, ceph-3

`server ceph-1 iburst`

- Khởi động lại dịch vụ NTP trên các node

`service chrony restart`



**Chú ý:**
<ul>
<li> Dịch vụ mds đã hoạt động nhưng ko dùng đc FileSystem, check command ko có lệnh.
<li> Ko thể mở rộng cluster với lệnh ceph-deploy. Số node trong cluster ban đầu đã được xác định bằng lệnh `ceph-deploy new ceph-1 ceph-2 ceph-3`. 

<img src=http://i.imgur.com/vIYP36y.jpg>

<li> Dịch vụ mds trên ceph-deploy ký hiệu là **mdsmap** , trên cài đặt từng bước là **fsmap**
</ul>












































