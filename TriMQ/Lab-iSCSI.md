#Lab ISCSI

Mô hình lab:
- 1 máy iSCSI Target chạy Ubuntu 14.04
- 1 máy iSCSI Initiator chạy Ubuntu 14.04

##Trên máy iSCSI Target:

- Cài đặt gói `iSCSI Target`:
```sh
root@lab2:~# apt-get install iscsitarget iscsitarget-dkms -y
```
- Tạo thư mục `iscsi_disks`:
```sh
root@lab2:~# mkdir /iscsi_disks
```
- Sử dụng lệnh `dd` để kiểm tra tốc độ của ổ cứng:
```sh
root@lab2:~# dd if=/dev/zero of=/iscsi_disks/disk01.img count=0 bs=1 seek=80G
```

```sh
if= tốc độ đầu vào
of= tốc độ đầu ra
bs= kích thước 1 block
```
- Sửa file `iscsitarget`:
```sh
root@lab2:~# vi /etc/default/iscsitarget
```
####Thay đổi dòng:
```sh
ISCSITARGET_ENABLE=true
```
- Sửa file `ietd.conf`:
```sh
vi /etc/iet/ietd.conf
```
####Thêm vào những dòng sau:
```sh
- Target iqn.2015-05.hanu.vn:target00
```
- Cung cấp device cho iSCSI target:
```sh
Lun 0 Path=/iscsi_disks/disk01.img,Type=fileio
```
- Cho phép IP của máy ISCSI Initiator:
```sh
initiator-address 172.16.69.30
```
- Cung cấp tài khoản xác thực:
```sh
incominguser quoctrimai password
```
- Khởi động lại dịch vụ:
```sh
/etc/init.d/iscsitarget restart
```

##Trên máy ISCSI Initiator:
- Tải gói `open-iscsi`:
```sh
root@lab1:~# apt-get install open-iscsi -y
```
- Sửa file `iscsid.conf`:
```sh
Bỏ comment dòng 53: node.session.auth.authmethod = CHAP
Bỏ comment dòng 56,57 và sửa node.session.auth.username = quoctrimai, node.session.auth.password = password
```
- Khởi động lại dịch vụ
```sh
/etc/init.d/open-iscsi restart
```
- Discover target:
```sh
iscsiadm -m discovery -t sendtargets -p 172.16.69.21
```
- Login vào Target:
```sh
root@lab1:~#  iscsiadm -m node --login
```
- Chạy gói `parted` để phân vùng ổ cứng:
```sh
apt-get install parted -y
```
- Tạo partition table
```sh
parted --script /dev/sdb "mklabel msdos"
```
- Tạo partition:
```sh
parted --script /dev/sdb "mkpart primary 0% 100%"
```
- Format phân vùng:
```sh
mkfs.ext4 /dev/sdb1
```
- Mount vào thư mục `/mnt`:
```sh
mount /dev/sdb1 /mnt`
```
- Kiểm tra bằng lệnh `df`:
```sh
df -hT
```

```sh
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  478M  4.0K  478M   1% /dev
tmpfs          tmpfs      98M 1000K   97M   2% /run
/dev/sda1      ext4       19G  1.2G   17G   7% /
none           tmpfs     4.0K     0  4.0K   0% /sys/fs/cgroup
none           tmpfs     5.0M     0  5.0M   0% /run/lock
none           tmpfs     488M     0  488M   0% /run/shm
none           tmpfs     100M     0  100M   0% /run/user
/dev/sdb1      ext4       79G   56M   75G   1% /mnt
```













