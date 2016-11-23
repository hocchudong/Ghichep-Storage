#Install Ceph-Deploy

Mục Lục:

[A. Install Ceph-Deploy trên ceph-1 bằng apt-get và triển khai Ceph cluster](#A)

[B. Install Ceph-Deploy bằng Package ...tar.gz](#B)

[C. Install Ceph-Deploy bằng pip hoặc easy_install](#C)

[D. Mở rộng Ceph cluster](#D)

Mô hình Lab

<img src=http://i.imgur.com/ZfijguF.png>


- 3 node: ceph-1, ceph-2, ceph-3
- 2 card mạng: 

	- eth0: `172.16.69.0/24`
 	- eth1: `10.10.10.0/24`

- 2 ổ cứng làm osd lưu trữ
- ceph-1 cài đặt dịch vụ ceph-deploy phục vụ cho việc triển khai cụm ceph
- Chạy lệnh `apt-get update` trên các node trước khi cài


**Chú ý:** Cài Ceph-Deploy với `pip`, `easy_install` ta sẽ có phiên bản mới nhất, với `apt-get` sẽ có phiên bản giống repo đã thêm vào. 


<a name="A"></a>
###A. Install Ceph-Deploy trên ceph-1 bằng apt-get và triển khai Ceph cluster

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

- Ad repo Ceph-Jewel

```sh

wget -q -O- 'https://download.ceph.com/keys/release.asc' | sudo apt-key add -
echo deb http://download.ceph.com/debian-jewel/ trusty main | sudo tee /etc/apt/sources.list.d/ceph.list
apt-get update
```

- Cài đặt dịch vụ Ceph-Deploy

`apt-get -y install ceph-deploy ceph-common ceph-mds`

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

<a name="B"></a>
B. Install Ceph-Deploy bằng Package ...tar.gz

- Tải gói cài đặt theo đường link: https://pypi.python.org/pypi/ceph-deploy hoặc dùng lệnh `wget` 

Ví dụ gói cài Ceph-Deploy phiên bản 1.5.36

`wget https://pypi.python.org/packages/23/f0/f144b1b55534a3e10d269dbfbe092e0aaa1c4b826c24f5df9320ae9bdfce/ceph-deploy-1.5.36.tar.gz`

- Giải nén và truy cập vào thư mục vừa giải nén

`tar xvf ceph-deploy-1.5.36.tar.gz`

<img src=http://i.imgur.com/5abWP6D.png>

- Cài đặt Ceph-Deploy

`python setup.py install`

- Các bước cài SSH và triển khai Ceph cluster giống như các bước trên mục A

<a name="C"></a>
###C. Install Ceph-Deploy bằng pip hoặc easy_install

- Cài đặt python-pip`

`apt-get install python-pip`

- Cài Ceph-Deploy

`pip install ceph-deploy`

- Cài đặt  apt-get install python-setuptools cho easy_install

`apt-get install python-setuptools`

- Cài Ceph-Deploy

`easy_install ceph-deploy`

<a name="D"></a>
###D. Mở rộng Ceph cluster

**Thêm Node ceph-4 làm Mon và OSD**

- Thêm ceph-4 vào file `/etc/hosts`

- Copy ssh key

`root@ceph-1:~# ssh-copy-id ceph-4`

- Thêm mon ceph-4 vào file ceph.conf

- Cài đặt ceph lên ceph-4

`root@ceph-1:~# ceph-deploy install ceph-4`

`root@ceph-1:~# ceph-deploy mon create ceph-4`

- Tạo osd

`root@ceph-1:~# ceph-deploy osd prepare ceph-4:/dev/sdb ceph-4:/dev/sdc`

**Chú ý:**
<ul>
<li> Dịch vụ mds đã hoạt động nhưng ko dùng đc FileSystem, check command ko có lệnh.
<li> Dịch vụ mds trên ceph-deploy ký hiệu là **mdsmap** , trên cài đặt từng bước là **fsmap**
</ul>












































