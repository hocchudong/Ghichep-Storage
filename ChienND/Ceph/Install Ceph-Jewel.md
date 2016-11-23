#Install Ceph-Jewel on Ubuntu 14.04
Mục lục:

[1. Mô hình Lab](#1)

[2. Cấu hình Node-I (Mon + Osd + Mds)](#2)

[3. Cấu hình Node-II (Mon + Osd)](#3)

[4. Cấu hình Node-III (Osd)](#4)

========================

<a name="1"></a>
###1. Mô hình Lab

<img src=http://i.imgur.com/qvCcwoy.png>

- Server chạy Ubuntu 14.04
- Máy có 2 card mạng
- Mỗi Server có 3 ổ cứng trống để tạo Osd

Chú ý: Số Node (Mon+Osd) nên cấu hình là 3 node để số lượng Object đc replicate đúng (Replicate mặc định là 3). Có thể cấu hình 1 node, dùng bình thường nhưng check ceph status sẽ báo pgs degrate.

**THỰC HIỆN TRÊN CÁC SERVER CEPH**

**B-1. Sửa file /etc/hosts**
```sh
127.0.0.1	localhost
172.16.69.11	ceph-1
172.16.69.12	ceph-2
172.16.69.13	ceph-3
```

**B-2. Tải trusted key và thêm repo**
```sh
wget -q -O- 'https://download.ceph.com/keys/release.asc' | sudo apt-key add -
echo deb http://download.ceph.com/debian-jewel/ trusty main | sudo tee /etc/apt/sources.list.d/ceph.list
apt-get update
```

- Bước tải trusted key ko hiện thông báo **OK** thì reboot lại máy và tải lại key

<img src=http://i.imgur.com/8RiEhh6.png>

**B-3. Cài đặt ceph**
```sh
apt-get install ceph
```

**B-4. Kiểm tra gói cài đặt**
```sh
dpkg -l |egrep -i "ceph|rados|rdb"
```

<img src=http://i.imgur.com/ZPZTs9k.png>

===================================

<a name="2"></a>
###2. Thiết lập Node-I (Mon+Osd+Mds)

**B-0. Cài đặt SSH**

```sh
apt-get -y install openssh-server
echo "trusty ALL = (root) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ceph 
sudo chmod 440 /etc/sudoers.d/ceph 
```

- Tạo ssh key và gửi tới các node khác
```sh
ssh-keygen 
ssh-copy-id ceph-2
ssh-copy-id ceph-3
```

**B-1. Tạo FSID cho Ceph cluster**
```sh
uuidgen
7b8e6b15-2642-4ecf-b811-feed00b0278c
```

**B-2. Tạo file ceph.conf**
```sh
vim /etc/ceph/ceph.conf
```

```sh
[global]
public network = 172.16.69.0/24
cluster network = 10.10.10.0/24
fsid = 7b8e6b15-2642-4ecf-b811-feed00b0278c

osd pool default min size = 1
osd pool default pg num = 128
osd pool default pgp num = 128
osd journal size = 1024

[mon]
mon host = ceph-1
mon addr = 172.16.69.11
mon initial members = ceph-1

[mon.ceph-1]
host = ceph-1
mon addr = 172.16.69.11

[mds]
mds data = /var/lib/ceph/mds/mds.ceph-1
keyring = /var/lib/ceph/mds/mds.ceph-1/mds.ceph-1.keyring

[mds.ceph-1]
host = ceph-1
```

**B-3. Tạo Keyring và monitor secret key**
```sh
ceph-authtool --create-keyring /tmp/ceph.mon.keyring --gen-key -n mon. --cap mon 'allow *'
```

**B-4. Tạp user client.admin, thêm user vào keyring**
```sh
ceph-authtool --create-keyring /etc/ceph/ceph.client.admin.keyring --gen-key -n client.admin --set-uid=0 --cap mon 'allow *' --cap osd 'allow *' --cap mds 'allow'
```

**B-5. Thêm user client.admin vào ceph.mon.keyring**
```sh
ceph-authtool /tmp/ceph.mon.keyring --import-keyring /etc/ceph/ceph.client.admin.keyring
```

**B-6 Tạo monitor map**
```sh
monmaptool --create --add ceph-1 172.16.69.11 --fsid 7b8e6b15-2642-4ecf-b811-feed00b0278c /tmp/monmap
```

<img src=http://i.imgur.com/ra7q2SG.png>

**B-7. Tạo thư mục cho ceph-mon deamon**

```sh
mkdir /var/lib/ceph/mon/ceph-ceph-1
ceph-mon --mkfs -i ceph-1 --monmap /tmp/monmap --keyring /tmp/ceph.mon.keyring
```

<img src=http://i.imgur.com/wnZIJyP.png>

**B-8. Khởi động dịch vụ, phân quyền thư mục**

```sh
service ceph start 

chown -R ceph:ceph /var/run/ceph
chown -R ceph:ceph /var/lib/ceph/mon/ceph-ceph-1

service ceph restart
```

- Kiểm tra ceph
```sh
ceph status
```

<img src=http://i.imgur.com/BRCMsN9.png>

**B-10 Tạo OSD**

- Tạo Osd từ 3 ổ đĩa trống sdb, sdc, sdd

<img src=http://i.imgur.com/UcoJAo3.png>

- Prepare OSD với cluster và định dạng FileSystem
```sh
for i in sdb sdc sdd;do ceph-disk prepare --cluster ceph --cluster-uuid 7b8e6b15-2642-4ecf-b811-feed00b0278c --fs-type xfs /dev/$i;done
```

<img src=http://i.imgur.com/v30rtth.png>

- Khởi động lại ceph

- Kiểm tra Osd
```sh
ceph -s
```

<img src=http://i.imgur.com/RfJR0Al.png>

**B-11. Cấu hình Ceph-Mds**

- Thêm các dòng sau vào ceph.conf
```sh
[mds]
mds data = /var/lib/ceph/mds/mds.ceph-1
keyring = /var/lib/ceph/mds/mds.ceph-1/mds.ceph-1.keyring

[mds.ceph-1]
host = ceph-1
```

- Tạo thư mục cho ceph-mds deamon, xác thực với CephX

```sh
mkdir /var/lib/ceph/mds/mds.ceph-1
ceph auth get-or-create mds.ceph-1 mds 'allow ' osd 'allow *' mon 'allow rwx' > /var/lib/ceph/mds/mds.ceph-1/mds.ceph-1.keyring
```

- Tạo FileSystem Pool

```sh
ceph osd pool create cephfs_data 128
ceph osd pool create cephfs_metadata 128
```

- Create FileSystem
```sh
ceph fs new cephfs cephfs_metadata cephfs_data
ceph fs ls
```

- Khởi động lại ceph và kiểm tra mds thông qua ceph status

<img src=http://i.imgur.com/jIjSmVk.png>

<a name="3"></a>
###3. Cấu hình Node-II (Mon+Osd)

=================================

**B-1. Copy file cấu hình và key từ Note-I sang Node-II***
```sh
root@ceph-1:/etc/ceph# scp /etc/ceph/ceph.conf root@ceph-2:/etc/ceph
root@ceph-1:/etc/ceph# scp /etc/ceph/ceph.client.admin.keyring  root@ceph-2:/etc/ceph
```

**B-2. Sửa ceph.conf trên Note-I**

Thêm:
```sh
[mon.ceph-2]
host = ceph-2
mon addr = 172.16.69.12
```

**B-3. Tạo thư mục và monmap trên Node-II**
```sh
mkdir -p /var/lib/ceph/mon/ceph-ceph-2 /tmp/ceph-2
ceph auth get mon. -o /tmp/ceph-2/monkeyring
ceph mon getmap -o /tmp/ceph-2/monmap
ceph-mon -i ceph-2 --mkfs --monmap /tmp/ceph-2/monmap --keyring /tmp/ceph-2/monkeyring
ceph mon add ceph-2 172.16.69.12
```

**B-4. Khởi động lại dịch vụ**
```sh
service ceph -a restart
```

- Sẽ có thông báo 

<img src=http://i.imgur.com/wV3D3qN.png>

- Phân quyền thư mục
```sh
chown -R ceph:ceph /var/run/ceph
chown -R ceph:ceph /var/lib/ceph/mon/ceph-ceph-2
```

- Khởi động lại dịch vụ 1 lần nữa
```sh
service ceph -a restart
```

**B-5 Tạo OSD**

- Tạo Osd từ 3 ổ đĩa trống sdb, sdc, sdd

<img src=http://i.imgur.com/UcoJAo3.png>

- Prepare OSD với cluster và định dạng FileSystem
```sh
for i in sdb sdc sdd;do ceph-disk prepare --cluster ceph --cluster-uuid 7b8e6b15-2642-4ecf-b811-feed00b0278c --fs-type xfs /dev/$i;done
```

- Khởi động lại Ceph

- Kiểm tra Ceph

<img src=http://i.imgur.com/eamfbri.png>

<a name="4"></a>
###4. Cấu hình Note-III (Osd)

**B-1. Copy từ Note-I sang Node-III**

```sh
root@ceph-1:/etc/ceph# scp /etc/ceph/ceph.conf root@ceph-3:/etc/ceph
root@ceph-1:/etc/ceph# scp /var/lib/ceph/bootstrap-osd/ceph.keyring root@ceph-3:/var/lib/ceph/bootstrap-osd
```

**B-2. Tạo OSD**

- Prepare OSD với cluster và định dạng FileSystem
```sh
for i in sdb sdc sdd;do ceph-disk prepare --cluster ceph --cluster-uuid 7b8e6b15-2642-4ecf-b811-feed00b0278c --fs-type xfs /dev/$i;done
```

- Activate Osd
```sh
for i in sdb1 sdc1 sdd1;do ceph-disk activate /dev/$i;done
```

- Kiểm tra OSD

<img src=http://i.imgur.com/LyNknZ9.png>

Tham khảo:

[1]- http://www.upforfree.com/dl3.php?name=Learning-Ceph%5Bebooksfeed.com%5D.pdf&size=19.00&n=ebooks

[2]- https://www.sebastien-han.fr/blog/2013/05/13/deploy-a-ceph-mds-server/ 

[3]- http://docs.ceph.com/docs/master/install/