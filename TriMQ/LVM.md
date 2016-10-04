#Giới thiệu về LVM, khái niệm, các thuật ngữ và hoạt động

#Mục lục:
**Table of Contents**

- [1. Giới thiệu LVM](#gioithieu)
	- [1.1 Khái niệm LVM](#khainiem)
	- [1.2 Tính năng của LVM](#tinhnang)

- [2. Kiến trúc và các thuật ngữ cơ trong LVM](#2)
	- [2.1 Kiến trúc](#kientruc)
	- [2.2 Các thuật ngữ](#thuatngu)

- [3. Tiến hành Lab](#lab)
	
- [4. Tài liệu tham khảo](#tailieu)


===============================================================

Tài liệu này thảo luận về cách làm việc, kiến trúc của LVM và các thao tác cơ bản của LVM
	
<a name="gioithieu"></a>
#1. Giới thiệu LVM:
<img src="http://www.howtogeek.com/wp-content/uploads/2011/02/652x202xbanner-1.png.pagespeed.gp+jp+jw+pj+js+rj+rp+rw+ri+cp+md.ic.VGSxDeVS9P.png">

<a name="khainiem"></a>
##1.1 Khái niệm LVM:
LVM (Linux Volume Management) là 1 công nghệ quản lý thiết bị lưu trữ cho phép người dùng có thể dễ dàng sử dụng và linh hoạt trong quản lý bằng cách thu thập các thiết bị lưu trữ thành nhóm và có thể phân chia thành các đơn vị lưu trữ 1 cách hợp lý

<a name="tinhnang"></a>
##1.2 Tính năng của LVM:
Các ưu điểm của LVM là rất linh hoạt trong việc sử dụng tài nguyên lưu trữ và điều khiển. Volume có thể đáp ứng được các yêu cầu thay đổi về kích cỡ dung lượng và dễ dang di chuyển các thiết bị vật lý ở trong pool. LVM cung cấp các tính năng nâng cao như snapshot, striping, mirroring


<a name"2"></a>
#2. LVM, kiến trúc và các thuật ngữ:

<img src="https://camo.githubusercontent.com/713a3058b8a31f2686108f71d0ba494fc8317adb/687474703a2f2f692e696d6775722e636f6d2f556154617475622e706e67">

<a name="kientruc"></a>
##2.1 Kiến trúc quản lý lưu trữ của LVM:

Chức năng chính của LVM được thực hiện bởi các thành phần nằm phía trên của các thiết bị lưu trữ vật lý. Tầng dưới của LVM là các thiết bị lưu trữ

<a name="thuatngu"></a>
##2.2 Các thuật ngữ:

###Physical Volumes: 

Khối thiết bị vật lý hoặc các thiết khác bị như đĩa (RAID) được LVM sử dụng như là vật liệu để xây dựng lên các tầng cao hơn. Physical volumes là các thiết bị lưu trữ thông thường

<img src="https://docs.fedoraproject.org/en-US/Fedora/14/html/Storage_Administration_Guide/images/lvg.png">

###Volume Groups: 

LVM kết hợp các Physical volume thành pool lưu trữ được hiểu như các volume group. Volume group có các đặc tính như là thiết bị nền tảng và có chức năng như 1 đơn vị logical thống nhất với khả năng lưu trữ và kết hợp các khối vật lý thành phần.


###Logical Volumes:

 1 Volume group có thể chia thành các Logical volumes. Logical có chức năng tương đương với các phân vùng trên ổ đĩa nhưng có sự linh hoạt hơn nhiều. Logical volume là thành phần chính mà người dùng và các ứng dụng tương tác với nó.
<img src="https://docs.fedoraproject.org/en-US/Fedora/14/html/Storage_Administration_Guide/images/lvols.png">

Tóm lại, LVM là công nghệ cho phép kết hợp các Physical volume thành các Volume group để từ đó có thể phân chia thành Logical volume có thể sử dụng 1 cách linh hoạt hơn so với các phân vùng trên ổ đĩa.

###Extent:

Mỗi volume trong phía bên trong volume group là 1 phần nhỏ, có thể fixed-size các phần đó. Khối kích thước được ấn định gọi là Extent. Kích thước của extent phụ thuộc vào volume group (tất cả volume bên trong group có thể làm cho có cùng extent size)

Extent trên Physical volume gọi là Physical extent, trong khi Extent của logical volume gọi là logical extent. Một logical volume đơn giản chỉ là 1 mapping LVM duy trì liên kết giữa logical và physical extent. Extent size thể hiện không gian nhỏ nhất mà có thể được phân bổ bởi LVM.

LVM có thể sao chép và sắp xếp các physical extent mà không gây gián đoạn cho người dùng. Logical volume có thể dễ dàng mở rộng hoặc thu nhỏ bằng cách thêm extent hoặc giảm extent
 

#3. Tiến hành lab:

####Thiết lập 1 hệ thống lưu trữ sử dụng LVM

- Kiểm tra những block devices mà LVM có thể thấy và quản lý:
```sh
lvmdiskscan
```
Các thành phần như `/dev/ram` được sử dụng để cải thiện hiệu suất

- <b>Tạo các Physical volume</b>:
```sh
pvcreate /dev/sdb /dev/sdc
```
```sh
OUTPUT
Physical volume "/dev/sdb" successfully created
```
- Kiểm tra các Physical volume:
```sh
pvs
```

- <b>Tạo Volume group</b>
```sh
vgcreate <name_group> /dev/sdb /dev/sdc
```
```sh
OUTPUT
 Volume group "vg3" successfully created
```
- Kiểm tra các Volume group đã tạo
```sh
vgs
```

- <b>Tạo Logical volume</b>
```sh
lvcreate -L 10G -n <name_lv> <name_vg>
```
<ul>
<li>-L 10G là tùy chỉnh dung lượng của Logical volume</li>
<li>- n tên của Logical volume</li>
<li> hoặc có thể tùy chỉnh là 100%FREE để tạo tất cả dung lượng còn lại trong volume group thành 1 Logical volume</li>
</ul>
- Kiểm tra các Logical volume đã tạo
```sh
lvs
```
- Để kiểm tra logical volume chi tiết trong Volume group:
```sh
vgs -o +lv_size,lv_name
```
 Output
```sh
VG   #PV #LV #SN Attr   VSize VFree LSize LV  
  vg1    3   1   0 wz--n- 4.99g 3.50g 1.49g lv1
```

####Format và Mounting các Logical volume:

- Format Logical volume:
```sh
mkfs.ext4 /dev/<vg_name>/<lv_name>
```
- Mount vào thư mục:
```sh
mount /dev/<vg_name>/<lv_name> /mnt/demo1/
```
Trong đó `/mnt/demo1` là thư mục để mount Logical volume vào

- Mount với fstab:
Chỉnh sửa trong file `fstab` như sau
```sh
/dev/<vg_name>/<lv_name> /mnt/demo2 ext4 defaults,nofail 0 0
```

<a name="tailieu"></a>
#4. Tài liệu tham khảo:

- https://www.digitalocean.com/community/tutorials/an-introduction-to-lvm-concepts-terminology-and-operations







