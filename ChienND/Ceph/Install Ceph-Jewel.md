#Install Ceph-Jewel on Ubuntu 14.04
Mục lục:

[1. Mô hình Lab](#1)

[2. Cấu hình Node-I (Mon+Osd+Mds)](#2)

[3. Cấu hình Note-II (Osd)](#3)

[4. Cấu hình Node-III (Mon+Osd)](#4)
========================

<a name="1"></a>
###1. Mô hình Lab

<img src=http://i.imgur.com/aEGR37o.png>

- Server chạy Ubuntu 14.04
- Máy có 2 card mạng
- Có 4 ổ cứng riêng để tạo osd

Chú ý: Số Node (Mon+Osd) nên cấu hình là 3 node để số lượng Object đc replicate đúng. Có thể cấu hình 1 node, dùng bình thường nhưng check ceph status sẽ báo pgs degrate.

**THỰC HIỆN TRÊN CÁC SERVER CEPH**

**B-1. Sửa file /etc/hosts**

```sh
127.0.0.1	localhost
172.16.69.128	Ceph-1
172.16.69.129	Ceph-2
172.16.69.130	Ceph-3
```

**B-2. Tải trusted key và thêm repo**

```sh
wget -q -O- 'https://download.ceph.com/keys/release.asc' | sudo apt-key add -
echo deb http://download.ceph.com/debian-jewel/ trusty main | sudo tee /etc/apt/sources.list.d/ceph.list
apt-get update
```

**B-3. Cài đặt**

`apt-get install ceph -y`

Kiểm tra gói cài đặt

`dpkg -l |egrep -i "ceph|rados|rdb"`

<a name="2"></a>
###2. Thiết lập Node-I (Mon+Osd+Mds)

**B-1. Tạo fSID cho Ceph cluster**

```sh
uuidgen
805c4e53-52d1-4394-99bc-11b907ebbaa4
```

**B-2. Tạo file ceph.conf**

`vim /etc/ceph/ceph.conf`

```sh
[global]
public network = 172.16.69.0/24
cluster network = 10.10.10.0/24
fsid = 805c4e53-52d1-4394-99bc-11b907ebbaa4

osd pool default min size = 1
osd pool default pg num = 128
osd pool default pgp num = 128
osd journal size = 5000

[mon]
mon host = Ceph-1
mon addr = 172.16.69.128
mon initial members = Ceph-1

[mon.Ceph-1]
host = Ceph-1
mon addr = 172.16.69.128

[mds]
mds data = /var/lib/ceph/mds/mds.Ceph-1
keyring = /var/lib/ceph/mds/mds.Ceph-1/mds.Ceph-1.keyring

[mds.Ceph-1]
host = Ceph-1
```

**B-3. Tạo Keyring và monitor secret key**

`ceph-authtool --create-keyring /tmp/ceph.mon.keyring --gen-key -n mon. --cap mon 'allow *'`

**B-4. Tạp user client.admin, thêm user vào keyring**

`ceph-authtool --create-keyring /etc/ceph/ceph.client.admin.keyring --gen-key -n client.admin --set-uid=0 --cap mon 'allow *' --cap osd 'allow *' --cap mds 'allow'`

**B-5. Thêm user client.admin vào ceph.mon.keyring**

`ceph-authtool /tmp/ceph.mon.keyring --import-keyring /etc/ceph/ceph.client.admin.keyring`

**B-6 Tạo monitor map**

`monmaptool --create --add Ceph-1 172.16.69.128 --fsid 805c4e53-52d1-4394-99bc-11b907ebbaa4 /tmp/monmap`

<img src=http://i.imgur.com/ECeA7Ay.png>

**B-7. Tạo thư mục cho ceph-mon deamon**

```sh
mkdir /var/lib/ceph/mon/ceph-Ceph-1

`ceph-mon --mkfs -i Ceph-1 --monmap /tmp/monmap --keyring /tmp/ceph.mon.keyring`
```

<img src=http://i.imgur.com/4Fr6cTT.png>

**B-8. Khởi động dịch vụ, phân quyền thư mục**

```sh
service ceph start 

chown -R ceph:ceph /var/run/ceph
chown -R ceph:ceph /var/lib/ceph/mon/ceph-Ceph-1

service ceph restart
```

**B-9. Tạo thư mục cho ceph-mds deamon, xác thực với cephX**

```sh
mkdir /var/lib/ceph/mds/mds.Ceph-1
ceph auth get-or-create mds.Ceph-1 mds 'allow ' osd 'allow *' mon 'allow rwx' > /var/lib/ceph/mds/mds.Ceph-1/mds.Ceph-1.keyring
```

- Check ceph
`ceph status`

<img src=http://i.imgur.com/oJSFtiI.png>

**B-10 Tạo OSD**

- Tạo Osd từ 4 ổ đĩa

<img src=http://i.imgur.com/JUmcTw5.png>

- Prepare OSD với cluster và định dạng FileSystem

`for i in sdb sdc sdd sde;do ceph-disk prepare --cluster ceph --cluster-uuid 805c4e53-52d1-4394-99bc-11b907ebbaa4 --fs-type xfs /dev/$i;done`

<img src=http://i.imgur.com/v30rtth.png>

- Activate OSD

`for i in sdb1 sdc1 sdd1 sde1; do ceph-disk activate /dev/$i;done`

- Check disk

<img src=http://i.imgur.com/PnZecen.png>

- Check OSD

<img src=http://i.imgur.com/JOnEOAz.png>

**B-11. Tạo Ceph Filesystem**

- Create FileSystem Pool

```sh
ceph osd pool create cephfs_data <pg_num>
ceph osd pool create cephfs_metadata <pg_num>
```

- Create FileSystem
```sh
ceph fs new <fs_name> <metadata> <data>
ceph fs ls
```

Khởi động lại ceph

- Check mds

<im src=http://i.imgur.com/Wq0NSj2.png>


<a name="3"></a>
###3. Cấu hình Note-II (Osd)

**B-1. Copy từ Note-I sang Node-II**

```sh
root@Ceph-1:/etc/ceph# scp /etc/ceph/ceph.conf root@Ceph-2:/etc/ceph
root@Ceph-1:/etc/ceph# scp /var/lib/ceph/bootstrap-osd/ceph.keyring root@Ceph-2:/var/lib/ceph/bootstrap-osd`
```

<img src=http://i.imgur.com/YzPXiiO.png>

**B-2. Tạo OSD**

- Prepare OSD với cluster và định dạng FileSystem

`for i in sdb sdc sdd sde;do ceph-disk prepare --cluster ceph --cluster-uuid 805c4e53-52d1-4394-99bc-11b907ebbaa4 --fs-type xfs /dev/$i;done`

- Activate OSD

`for i in sdb1 sdc1 sdd1 sde1; do ceph-disk activate /dev/$i;done`

<img src=http://i.imgur.com/UKI33XL.png>

**B-3. Check Osd**

- Khởi động lại ceph

`service ceph -a restart`

<img src=http://i.imgur.com/IciLdGp.png>

<a name="4"></a>
###4. Cấu hình Node-III (Mon+Osd)

**B-1. Copy từ Note-I sang Node-III***
```sh
root@Ceph-1:/etc/ceph# scp /etc/ceph/ceph.conf root@Ceph-3:/etc/ceph
root@Ceph-1:/etc/ceph# scp /etc/ceph/ceph.client.admin.keyring  root@Ceph-3:/etc/ceph
```

**B-2. Sửa ceph.conf trên Note-I**

Thêm:
```sh
[mon.Ceph-3]
host = Ceph-3
mon addr = 172.16.69.130
```

**B-3. Tạo thư mục và monmap trên Node-III**
```sh
mkdir -p /var/lib/ceph/mon/ceph-Ceph-3 /tmp/Ceph-3
ceph auth get mon. -o /tmp/Ceph-3/monkeyring
ceph mon getmap -o /tmp/Ceph-3/monmap
ceph-mon -i Ceph-3 --mkfs --monmap /tmp/Ceph-3/monmap --keyring /tmp/Ceph-3/monkeyring
ceph mon add Ceph-3 172.16.69.130
```

**B-4. Khởi động lại dịch vụ trên Node-I**

`service ceph -a restart`

- Sẽ có thông báo 

<img src=http://i.imgur.com/1zmgoN7.png>

- Ta quay lại Node-III để phân quyền thư mục:
```sh
chown -R ceph:ceph /var/run/ceph
chown -R ceph:ceph /var/lib/ceph/mon/ceph-Ceph-3
```

- Khởi động lại dịch vụ 1 lần nữa

`service ceph -a restart`


Tham khảo:

[1]- http://www.upforfree.com/dl3.php?name=Learning-Ceph%5Bebooksfeed.com%5D.pdf&size=19.00&n=ebooks

[2]- https://www.sebastien-han.fr/blog/2013/05/13/deploy-a-ceph-mds-server/ 

[3]- http://docs.ceph.com/docs/master/install/