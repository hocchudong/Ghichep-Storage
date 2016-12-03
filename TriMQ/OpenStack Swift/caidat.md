# Cài đặt Openstack Swift

# Mục lục
- [1. Mô hình cài đặt và các bước chuẩn bị](#1)
	- [1.1 Mô hình cài đặt](#11)
	- [1.2 Các bước chuẩn bị](#12)
- [2. Cài đặt proxy service trên controller](#2)
- [3. Cài đặt Storage node](#3)
- [4. Cài đặt Ring](#4)
	- [4.1 Cài đặt Account Ring](#41)
	- [4.2 Cài đặt Container Ring](#42)
	- [4.3 Cài đặt Object Ring](#43)
- [5. Hoàn thành các bước cài đặt](#5)
- [6. Kiểm tra hoạt động](#6)
- [7. Tài liệu tham khảo](#7)

=================================================

<a name="1"></a>
## 1. Mô hình cài đặt và các bước chuẩn bị

<a name="1"></a>
### 1.1 Mô hình cài đặt

<img src="http://i.imgur.com/z8kwRh0.png">

- Các máy chạy hệ điều hành Ubuntu 14.04

- Chuẩn bị như sau
	- Trên node controller
	```sh
	HĐH: Ubuntu server 14.04
	2 card mạng: eth0(Hostonly):10.10.10.140, eth1(NAT): 172.16.69.140
	2 Ổ cứng: Sda sử dụng để cài OS, Sdb sử dụng để setup LVM (gộp Cinder vào controller)
	```
	- Trên node Compute1
	```sh
	HĐH: Ubuntu server 14.04
	2 card mạng: eth0(Hostonly):10.10.10.141, eth1(NAT): 172.16.69.141
	1 ổ cứng
	```
	- Trên node1
	```sh
	HĐH: Ubuntu server 14.04
	2 card mạng: eth0(Hostonly):10.10.10.150, eth1(NAT): 172.16.69.150
	3 ổ cứng: 1 ổ cài OS, 2 ổ để lưu Object
	```
	- Trên node2
	```sh
	HĐH: Ubuntu server 14.04
	2 card mạng: eth0(Hostonly):10.10.10.151, eth1(NAT): 172.16.69.151
	3 ổ cứng: 1 ổ cài OS, 2 ổ lưu Object
	```

<a name="12"></a>
### 1.2 Các bước chuẩn bị 
#### Bước 1
- Cài đặt OpenStack theo [Script](https://github.com/congto/OpenStack-Mitaka-Scripts). Lựa chọn bản Openstack Mitaka sử dụng Openvswitch trên Ubuntu

- Lưu ý trong script phải sửa lại file `ctl-7-cinder-aio.sh` phải sửa lại ổ `vdb` thành `sdb`

#### Bước 2: 
Sửa lại các file `/etc/hostname` và `etc/hosts`
- Trên node controller sửa file `/etc/hostname` như sau
	```sh
	controller
	```
	
- Tương tự như node controller, các node khác cũng sửa ứng với tên máy, ở đây lần lượt là `compute1`, `node1` và `node2`
	
- Trên node controller sửa file `/etc/hosts` như sau
	```sh
	127.0.0.1       localhost controller
	10.10.10.140    controller
	10.10.10.141	compute1
	10.10.10.150    node1
	10.10.10.151    node2
	```
- Trên node compute1 sửa file `/etc/hosts` như sau
	```sh
	127.0.0.1       localhost compute1
	10.10.10.140    controller
	10.10.10.141	compute1
	10.10.10.150    node1
	10.10.10.151    node2
	```
	
- Trên node1 sửa file `/etc/hosts` như sau
	```sh
	127.0.0.1       localhost node1
	10.10.10.140    controller
	10.10.10.141	compute1
	10.10.10.150    node1
	10.10.10.151    node2
	```
	
- Trên node2 sửa file `/etc/hosts` như sau
	```sh
	127.0.0.1       localhost node2
	10.10.10.140    controller
	10.10.10.141	compute1
	10.10.10.150    node1
	10.10.10.151    node2
	```
- Chú ý: Ping (bằng IP hoặc hostname)qua lại giữa các node để kiểm tra</b>
	
#### Bước 3: Cài đặt dịch vụ NTP
- Do chạy scrip nên trên 2 node controller và compute1 đã chạy sẵn dịch vụ này
	
- Trong mô hình này sẽ chạy dịch vụ NTP trên 2 node lưu trữ Object là `node1` và `node2`

	- Tải gói cài đặt
	```sh
	apt-get install chrony
	```
	- Chỉnh sửa file cấu hình `/etc/chrony/chrony.conf`
	```sh
	server controller iburst
	```
	- Khởi động lại dịch vụ
	```sh
	service chrony restart
	```
### Bước 4: Tải các gói dịch vụ Openstack trên 2 node lưu trữ
- Enable OpenStack Repository

```sh
apt-get install software-properties-common -y 
add-apt-repository cloud-archive:mitaka
```

- Upgrade các gói phần mềm

```sh
apt-get update && apt-get dist-upgrade && init6 -y 
```

- Chạy các gói OpenStack client

```sh
apt-get install python-openstackclient -y
```	


<a name="2"></a>
## 2. Cài đặt proxy service trên controller
Các bước thực hiện với quyền root trên node `controller`

```sh
su -
```

### 2.1 Source the `admin` credentials to gain access to admin-only CLI commands

```sh
. admin-openrc
```
### 2.2 Thực hiện các bước sau để tạo dịch vụ thông tin xác thực
- Tạo 1 user có tên là `swift`

```sh
openstack user create --domain default --password-prompt swift
```

- Gán quyền admin cho user `swift`

```sh
openstack role add --project service --user swift admin
```
Bước này không có output

- Tạo dịch vụ `swift`

```sh
openstack service create --name swift \
  --description "OpenStack Object Storage" object-store
```

- Tạo dịch vụ Object storage API endpoint

```sh
openstack endpoint create --region RegionOne \
  object-store public http://controller:8080/v1/AUTH_%\(tenant_id\)s
```

```sh
openstack endpoint create --region RegionOne \
  object-store internal http://controller:8080/v1/AUTH_%\(tenant_id\)s
```

```sh
openstack endpoint create --region RegionOne \
  object-store admin http://controller:8080/v1
```

### 2.3 Cài đặt và config các thành phần
<b>Chú ý</b>: Các file mặc định khác nhau tùy theo bản phân phối. Bạn nên thêm các thành phần hơn là thay đổi các thành phần

- Cài đặt gói

```sh
apt-get -y install swift swift-proxy python-swiftclient \
  python-keystoneclient python-keystonemiddleware \
  memcached
```

- Tạo thư mục `/etc/swift`

```sh
mkdir /etc/swift
```

- Nhận các file cấu hình dịch vụ proxy từ Object storage source 

```sh
curl -o /etc/swift/proxy-server.conf https://git.openstack.org/cgit/openstack/swift/plain/etc/proxy-server.conf-sample?h=stable/mitaka
```

- <b>Chỉnh sửa file `/etc/swift/proxy-server.conf`</b>

- Trong section `[DEFAULT]` sửa như sau:
```sh
[DEFAULT]
...
bind_port = 8080
user = swift
swift_dir = /etc/swift
```

- Trong section `[pipeline:main]` bỏ các dòng `tempurl` và `tempauth` sau đó thêm `authtoken` và `keystoneauth`

- Trong section `[app:proxy-server]`

```sh
[app:proxy-server]
use = egg:swift#proxy
...
account_autocreate = True
```

- Trong section `[filter:keystoneauth]`

```sh
[filter:keystoneauth]
use = egg:swift#keystoneauth
...
operator_roles = admin,user
```

- Trong section `[filter:authtoken]`

```sh
[filter:authtoken]
paste.filter_factory = keystonemiddleware.auth_token:filter_factory
...
auth_uri = http://controller:5000
auth_url = http://controller:35357
memcached_servers = controller:11211
auth_type = password
project_domain_name = default
user_domain_name = default
project_name = service
username = swift
password = Welcome123
delay_auth_decision = True
```
Comment hoặc xóa các option khác trong `[filter:authtoken]`

- Trong section `[filter:cache]`, sửa vị trí `memcached`

```sh
[filter:cache]
use = egg:swift#memcache
...
memcache_servers = controller:11211
```

<a name="3"></a>
## 3. Cài đặt Storage node
Thực hiện cài đặt trên storage node
- Cài đặt gói bổ trợ

```sh
apt-get install xfsprogs rsync
```

- Format các ổ `sdb` và `sdc` là `XFS`

```sh
mkfs.xfs /dev/sdb
mkfs.xfs /dev/sdc
```

- Tạo thư mục để mount
```sh
mkdir -p /srv/node/sdb
mkdir -p /srv/node/sdc
```

- Sửa file `/etc/fstab` và thêm như dưới đây
```sh
/dev/sdb /srv/node/sdb xfs noatime,nodiratime,nobarrier,logbufs=8 0 2
/dev/sdc /srv/node/sdc xfs noatime,nodiratime,nobarrier,logbufs=8 0 2
```

- Mount các ổ vào
```sh
mount /srv/node/sdb
mount /srv/node/sdc
```

- Tạo hoặc chỉnh sửa file `/etc/rsyncd.conf`

```sh
uid = swift
gid = swift
log file = /var/log/rsyncd.log
pid file = /var/run/rsyncd.pid
address = 10.10.10.150

[account]
max connections = 2
path = /srv/node/
read only = False
lock file = /var/lock/account.lock

[container]
max connections = 2
path = /srv/node/
read only = False
lock file = /var/lock/container.lock

[object]
max connections = 2
path = /srv/node/
read only = False
lock file = /var/lock/object.lock
```

- Chỉnh sửa file `/etc/default/rsync` và enable `rsync` service
<b>Lưu ý:</b> Dịch vụ rsync không yêu cầu xác thực, vì vậy nên sử dụng trong 1 mạng riêng
```sh
RSYNC_ENABLE=true
```

- Restart dịch vụ

```sh
service rsync start
```

### Tải và cài đặt các thành phần

- Tải các gói

```sh
apt-get install swift swift-account swift-container swift-object
```

- Nhận các file cấu hình dịch vụ object storage từ source
```sh
curl -o /etc/swift/account-server.conf https://git.openstack.org/cgit/openstack/swift/plain/etc/account-server.conf-sample?h=stable/mitaka
```

```sh
curl -o /etc/swift/container-server.conf https://git.openstack.org/cgit/openstack/swift/plain/etc/container-server.conf-sample?h=stable/mitaka
```

```sh
curl -o /etc/swift/object-server.conf https://git.openstack.org/cgit/openstack/swift/plain/etc/object-server.conf-sample?h=stable/mitaka
```

- Chỉnh sửa file `/etc/swift/account-server.conf` và cấu hình như sau

	- Trong section `[DEFAULT]` chỉnh sửa như sau
	```sh
	[DEFAULT]
	...
	bind_ip = 10.10.10.150
	bind_port = 6002
	user = swift
	swift_dir = /etc/swift
	devices = /srv/node
	mount_check = True
	```

	- Trong section `[pipeline:main]` enable module
	```sh
	[pipeline:main]
	pipeline = healthcheck recon account-server
	```
	
	- Trong section `[filter:recon]` chỉnh sửa
	```sh
	[filter:recon]
	use = egg:swift#recon
	...
	recon_cache_path = /var/cache/swift
	```
	
- Chỉnh sửa file `/etc/swift/container-server.conf` và thực hiện như sau

	- Trong section `[DEFAULT]` 
	```sh
	[DEFAULT]
	...
	bind_ip = 10.10.10.150
	bind_port = 6001
	user = swift
	swift_dir = /etc/swift
	devices = /srv/node
	mount_check = True
	```
	
	- Trong section `[pipeline:main]` enable module
	```sh
	[pipeline:main]
	pipeline = healthcheck recon container-server
	```
	
	- Trong section `[filter:recon]` chỉnh sửa
	```sh
	[filter:recon]
	use = egg:swift#recon
	...
	recon_cache_path = /var/cache/swift
	```
	
- Chỉnh sửa file `/etc/swift/object-server.conf` và thực hiện như sau

	- Trong section `[DEFAULT]` chỉnh sửa
	```sh
	[DEFAULT]
	...
	bind_ip = 10.10.10.150
	bind_port = 6000
	user = swift
	swift_dir = /etc/swift
	devices = /srv/node
	mount_check = True
	```
	
	- Trong section `[pipeline:main]` enable module
	```sh
	[pipeline:main]
	pipeline = healthcheck recon object-server
	```
	
	- Trong section `[filter:recon]` chỉnh sửa như sau
	```sh
	[filter:recon]
	use = egg:swift#recon
	...
	recon_cache_path = /var/cache/swift
	recon_lock_path = /var/lock
	```
	
- Đảm bảo đúng quyền của thư mục mount
```sh
chown -R swift:swift /srv/node
```

- Tạo thư mục `recon` và phân quyền
```sh
mkdir -p /var/cache/swift
chown -R root:swift /var/cache/swift
chmod -R 775 /var/cache/swift
```

<a name="4"></a>
## 4. Cài đặt Ring
- Trước khi bắt đầu với dịch vụ Object storage, cần phải tạo Account, Container, Object Ring. Cài đặt những bước này trên node controller

- <b>Chú ý</b>: Trong mô hình này của tôi là 1 region, 2 zones và 4 thiết bị lưu trữ

<a name="41"></a>
### 4.1 Cài đặt Account ring

- Thay đổi thư mục `/etc/swift`
```sh
cd /etc/swift
```

- Tạo file cơ sở `account.builder`
```sh
swift-ring-builder account.builder create 10 3 1
```

- Add từng storage node vào Ring, ở đây tôi có 2 nodes lưu trữ và 4 thiết bị lưu trữ

```sh
swift-ring-builder account.builder \
  add --region 1 --zone 1 --ip 10.10.10.150 --port 6002 \
  --device sdb --weight 100
```

```sh
swift-ring-builder account.builder \
  add --region 1 --zone 1 --ip 10.10.10.150 --port 6002 \
  --device sdc --weight 100
```

```sh
swift-ring-builder account.builder \
  add --region 1 --zone 2 --ip 10.10.10.151 --port 6002 \
  --device sdb --weight 100
```

```sh
swift-ring-builder account.builder \
  add --region 1 --zone 2 --ip 10.10.10.151 --port 6002 \
  --device sdc --weight 100
```

- Xác thực nội dung của Ring
```sh
swift-ring-builder account.builder
```
Trong bước này có thể sẽ có output `Ring file account.ring.gz not found, probably it hasn't been written yet`. Đây không phải là lỗi mà do các file này chưa được update, bạn hãy sử dụng câu lệnh cân đối lại Ring (dưới đây) rồi kiểm tra lại
- Cân đối lại Ring
```sh
swift-ring-builder account.builder rebalance
```

<a name="42"></a>
### 4.2 Cài đặt Container ring
- Thay đổi đến thư mục `/etc/swift`
```sh
cd /etc/swift
```

- Tạo file cơ sở `container.builder`
```sh
swift-ring-builder container.builder create 10 3 1
```

- Add từng node lưu trữ vào container ring

```sh
swift-ring-builder container.builder add \
  --region 1 --zone 1 --ip 10.10.10.150 --port 6001 --device sdb --weight 100
```

```sh
swift-ring-builder container.builder add \
  --region 1 --zone 1 --ip 10.10.10.150 --port 6001 --device sdc --weight 100
```

```sh
swift-ring-builder container.builder add \
  --region 1 --zone 2 --ip 10.10.10.151 --port 6001 --device sdb --weight 100
```

```sh
swift-ring-builder container.builder add \
  --region 1 --zone 2 --ip 10.10.10.151 --port 6001 --device sdc --weight 100
```

- Xác thực lại nội dung của container ring

```sh
swift-ring-builder container.builder
```

- Cân đối lại Ring
```sh
swift-ring-builder container.builder rebalance
```

<a name="43"></a>
### 4.3 Tạo object ring
- Thay đổi tới thư mục `/etc/swift`

```sh
cd /etc/swift
```

- Tạo file cơ sở `object.builder`

```sh
swift-ring-builder object.builder create 10 3 1
```

- Add các node vào object ring

```sh
swift-ring-builder object.builder add \
  --region 1 --zone 1 --ip 10.10.10.150 --port 6000 --device sdb --weight 100
```

```sh
swift-ring-builder object.builder add \
  --region 1 --zone 1 --ip 10.10.10.150 --port 6000 --device sdc --weight 100
```

```sh
swift-ring-builder object.builder add \
  --region 1 --zone 2 --ip 10.10.10.151 --port 6000 --device sdb --weight 100
```

```sh
swift-ring-builder object.builder add \
  --region 1 --zone 2 --ip 10.10.10.151 --port 6000 --device sdc --weight 100
```

- Xác thực lại nội dung của object ring

```sh
swift-ring-builder object.builder
```

- Cân đối lại Ring

```sh
swift-ring-builder object.builder rebalance
```

#### Phân tán các file cấu hình ring
Sao chép `account.ring.gz`, `container.ring.gz`, và `object.ring.gz` sang thư mục `etc/swift` của mỗi node lưu trữ
```sh
scp -r account.ring.gz container.ring.gz object.ring.gz root@10.10.10.150:/etc/swift
```
và
```sh
scp -r account.ring.gz container.ring.gz object.ring.gz root@10.10.10.151:/etc/swift
```
<b>Chú ý:</b> Nên vào các node lưu trữ để kiểm tra lại

<a name="5"></a>
### 5. Hoàn thành các bước cài đặt
- Lấy file `/etc/swift/swift.conf ` từ source

```sh
curl -o /etc/swift/swift.conf \
  https://git.openstack.org/cgit/openstack/swift/plain/etc/swift.conf-sample?h=stable/mitaka
```

- Chỉnh sửa file `/etc/swift/swift.conf` theo các bước dưới đây

	- Trong section `[swift-hash]` chỉnh sửa
	
	```sh
	[swift-hash]
	...
	swift_hash_path_suffix = HASH_PATH_SUFFIX
	swift_hash_path_prefix = HASH_PATH_PREFIX
	```
	
	- Trong section `[storage-policy:0]` chỉnh sửa 
	
	```sh
	[storage-policy:0]
	...
	name = Policy-0
	default = yes
	```
	
- Sao chép file `swift.conf` sang thư mục `/etc/swift` trên mỗi node lưu trữ

- Trên tất cả các node, đảm bảo quyền của các thư mục cấu hình

```sh
chown -R root:swift /etc/swift
```

- Trên node controller chạy proxy service, khởi động lại các dịch vụ

```sh
service memcached restart
service swift-proxy restart
```

- Trên các storage node, khởi động các dịch vụ object storage

```sh
swift-init all start
```

<a name="6"></a>
## 6. Kiểm tra hoạt động
- Source the `demo` credentials

```sh
. demo-openrc
```

- Show trạng thái dịch vụ

```sh
swift stat
```

- Tạo container `container1`

```sh
openstack container create container1
```

- Upload 1 file vào `container1`

```sh
openstack object create container1 tri.txt
```

- Liệt kê file trong `container1`

```sh
openstack object list container1
```

- Tải file từ container về

```sh
openstack object save container1 tri.txt
```

<a name="7"></a>
## 7. Tài liệu tham khảo:

- Tài liệu tham khảo: http://docs.openstack.org/mitaka/install-guide-ubuntu/swift-verify.html











