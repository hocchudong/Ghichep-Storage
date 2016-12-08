# Mô tả cài đặt Swift

Tài liệu này sẽ ghi chú những câu lệnh và ý nghĩa những đoạn cấu hình trong Openstack Swift. Tham khảo tài liệu cài đặt tại [đây](https://github.com/hocchudong/Ghichep-Storage/blob/master/TriMQ/OpenStack%20Swift/caidat.md)

Tài liệu sẽ update thêm...
# Mục lục:
- [1. Các bước chuẩn bị](#1)
- [2. Cài đặt Proxy](#2)
- [3. Cài đặt Storage node](#3)
- [4. Cài đặt Ring](#4)
- [5. Hoàn thành các bước cài đặt](#5)

========================

<a name="1"></a>
## 1. Các bước chuẩn bị:
Các bước chuẩn bị để cài đặt Swift

- Trên các node trong mô hình triển khai, sửa lại các file `/etc/hosts` ví dụ như sau:
```sh
127.0.0.1       localhost controller
10.10.10.140    controller
10.10.10.141    compute1
10.10.10.150    node1
10.10.10.151    node2
```
Ví dụ trên đây nghĩa là localhost là máy có tên controller và có địa chỉ là 127.0.0.1. Những địa chỉ IP còn lại ứng với những tên máy còn lại. Mục đích của việc này là giúp cho các máy có thể ping với nhau bằng cách sử dụng địa chỉ IP hoặc hostname.

- Cài đặt dịch vụ NTP:
Dịch vụ NTP (Network Time Protocol) là dịch vụ đồng bộ về thời gian giữa các máy trong hệ thống. Dịch vụ sẽ chạy trên node controller và các máy còn lại sẽ chọc vào máy controller để đồng bộ thời gian trong mạng
```sh
server controller iburst
```

- Tải các gói phần mềm
```sh
add-apt-repository cloud-archive:mitaka
```
Thực hiện lệnh trên để thêm các gói phần mềm vào kho phần mềm, sau đó update lại các gói phần mềm và khởi động lại máy
```sh
apt-get update && apt-get dist-upgrade && init 6 -y
```

<a name="2"></a>
## 2. Cài đặt Proxy
Proxy là 1 dịch vụ chạy trên node controller (hoặc bất kì 1 node nào đó ngoài node lưu trữ) được sử dụng để kiểm soát các yêu cầu về object trên các node lưu trữ

- Khai báo môi trường để cho phép duy nhất admin lấy quyền truy cập vào CLI (command line interface) command
```sh
. admin-openrc
```

- Tạo user trong openstack có tên là `swift`, domain để mặc định, có sử dụng password với cú pháp lệnh như sau:
```sh
openstack user create --domain default --password-prompt swift
```

- Gán quyền admin cho user `swift` vừa tạo với câu lệnh:
```sh
openstack role add --project service --user swift admin
```
Nghĩa là user swift đã được gán quyền admin, --project service nghĩa là user này được gán vào các project dịch vụ

- Tạo dịch vụ `swift`
```sh
openstack service create --name swift \
  --description "OpenStack Object Storage" object-store
```
Nghĩa là tạo 1 dịch vụ có tên là swift, được mô tả là OpenStack Object Storage, kiểu lưu trữ là objec (khác với block hay file storage)

- Tạo các dịch vụ API endpoint
```sh
openstack endpoint create --region RegionOne \
  object-store public http://controller:8080/v1/AUTH_%\(tenant_id\)s
```
Tạo endpoint là region có tên RegionOne, kiểu lưu trữ object, interface là public, có url là http://controller:8080/v1/AUTH_%\(tenant_id\)s

```sh
openstack endpoint create --region RegionOne \
  object-store internal http://controller:8080/v1/AUTH_%\(tenant_id\)s
```
Interface ở đây là internal

```sh
openstack endpoint create --region RegionOne \
  object-store admin http://controller:8080/v1
```
Interface là admin, Url là  http://controller:8080/v1

- Nhận file cấu hình dịch vụ proxy
```sh
curl -o /etc/swift/proxy-server.conf https://git.openstack.org/cgit/openstack/swift/plain/etc/proxy-server.conf-sample?h=stable/mitaka
```
curl là 1 công cụ cho phép gửi các yêu cầu và nhận lại các phản hồi qua http. Ở đây là nhận về file cấu hình proxy về thư mục `/etc/swift`

<a name="3"></a>
## 3. Cài đặt Storage node
- Định dạng các ổ cứng, ở đây có 2 ổ cứng định dạng là xfs
```sh
mkfs.xfs /dev/sdb
mkfs.xfs /dev/sdc
```

- Sửa file `/etc/fstab` với mục đích mount cứng các ổ vào thư mục để khi reboot không bị mất
```sh
/dev/sdb /srv/node/sdb xfs noatime,nodiratime,nobarrier,logbufs=8 0 2
/dev/sdc /srv/node/sdc xfs noatime,nodiratime,nobarrier,logbufs=8 0 2
```

- Thực hiện mount vào thư mục `/srv/node` đây là thư mục chứa dữ liệu của các dịch vụ

```sh
mount /srv/node/sdb
mount /srv/node/sdc
```

- Config file `/etc/rsyncd.conf` là dịch vụ đồng bộ dữ liệu giữa các node lưu trữ. Khi cấu hình, dịch vụ này đã bị ẩn đi. Để enable dịch vụ này thì chỉnh sửa như sau

```sh
RSYNC_ENABLE=true
```

	- uid (user identification) là swift
	- gid (group id) là swift
	- Kết nối tối đa ở đây là 2 thiết bị lưu trữ
	- Địa chỉ file log /var/log/rsyncd
	- Bộ điều khiển PID nằm ở file /var/run/rsyncd.pid
	- IP node lưu trữ: 10.10.10.150

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

Các thiết bị lưu trữ nằm ở thư mục `/srv/node/`, lock file nằm ở `/var/lock/account.lock`

- Cấu hình file `/etc/swift/account-server.conf`
	- bind_ip là địa chỉ IP được gán theo MAC, ở đây là 10.10.10.150
	- bind_port là 6002 để xác định tiến trình account chạy trên server
	- user là swift
	- thư mục của swift là /etc/swift
	- thiết bị được lưu ở /srv/node
	- có kiểm tra thiết bị mount

```sh
[DEFAULT]
bind_ip = 10.10.10.150
bind_port = 6002
user = swift
swift_dir = /etc/swift
devices = /srv/node
mount_check = True
```

- Cấu hình trong section dưới đây để thu thập dữ liệu và theo dõi từ xa các tiến trình account-server theo pipeline

```sh
[pipeline:main]
pipeline = healthcheck recon account-server
```
- Tạo bộ lọc cho recon, thư mục chứa các thông tin mà recon lấy được từ các tiến trình account-server :

```sh
[filter:recon]
use = egg:swift#recon
recon_cache_path = /var/cache/swift
```
Khi kiểm tra sẽ có những thông tin như sau

```sh
{"replication_stats": {"no_change": 0, "rsync": 0, "success": 0, "start": 1481098962.310588, "attempted": 2, "ts_repl": 0, "remove": 0, "remote_merge": 0, "diff_capped": 0, "failure": 4, "hashmatch": 0, "failure_nodes": {"10.10.10.151": {"sdb": 2, "sdc": 2}}, "diff": 0, "empty": 0}, "account_audits_since": 1481092457.164096, "replication_last": 1481098967.30939, "account_audits_passed": 5, "account_audits_failed": 0, "account_auditor_pass_completed": 0.075653076171875, "replication_time": 4.998802185058594}
```

<a name="4"></a>
## 4. Cài đặt Ring:
- Thay đổi sang thư mục `/etc/swift` để thực hiện những bước cài đặt tiếp theo
```sh
cd /etc/swift
```

- Tạo file cơ sở account.builder. Đây là các file được Swift sử dụng để xây dựng nên Ring
```sh
swift-ring-builder account.builder create 10 3 1
```
Câu lệnh trên đã tạo 1 file cơ sở là account.builder với các thông số:
	- part_power = 10. Chỉ số này là số partition được tạo ra trong cluster
	- replicas = 3. Chỉ số này là số replicas được lưu trong cluster
	- min_part_hours 1. Chỉ số này là tần suất các bản replicas được di chuyển trong cluster

- Thêm các thiết bị vào file cơ sở, mục đích là để các file cơ sở này kiểm soát được các thiết bị để xây dựng Ring
```sh
swift-ring-builder account.builder \
  add --region 1 --zone 1 --ip 10.10.10.150 --port 6002 \
  --device sdb --weight 100
```
Câu lệnh này có nghĩa là add các thông tin về thiết bị lưu trữ vào file cơ sở
	- Thêm vào region là 1
	- Thêm vào zone là 2
	- Địa chỉ IP của node là 10.10.10.150
	- Sử dụng port 6002
	- Thiết bị lưu trữ là sdb
	- Chỉ số weight là 100. Chỉ số này nhằm xác định số partition được gán vào drivers

Trong mô hình này là 1 region, 2 zones, và 4 thiết bị lưu trữ nên phải thực hiện câu lệnh trên 4 lần và phải thay các chỉ số tương ứng với thiết bị gán vào file cơ sở

- Kiểm chứng lại nội dung của file cơ sở
```sh
swift-ring-builder account.builder
```
Câu lệnh này có mục đích là để liệt kê lại các thiết bị được gán vào.

- Cân đối lại Ring
```sh
swift-ring-builder account.builder rebalance
```
