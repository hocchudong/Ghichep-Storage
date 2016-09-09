#CEPH BLOCK DEVICE

Ceph Block Devices, you must have access to a running Ceph cluster.

Mục lục:

[1. Snapshot](#1)

[2. Layering](#2)

[3. RBD MIRRORING](#3)

[4. CACHE SETTINGS](#4)

[5. Block Devices and OpenStack](#5)
====================

Ceph Block Device (RBD) là một chuỗi các byte. Nó là thin-provisioned, resizable và data đc lưu trên nhiều OSDs. RBD tương tác với OSD thông qua kernel modules hoặc librbd library.. RBD có tính năng snapshot, nhân bản, nhất quán. RBD cung cấp hiệu suất cao với khả năng mở rộng 

Note: Kernel modules can use Linux page caching. For librbd-based applications, Ceph supports RBD Caching.

<a name="1"></a>
##1. Snapshot 

Ceph hỗ trợ snapshot layering, nó cho phép clone image nhanh chóng và dễ dàng. Ceph hỗ trợ block device snapshots bằng `rbd` command và interfaces. 

Để sử dụng RBD snapshots, ta cần chạy Ceph cluster. Khuyến cáo nên dừng I/O trước khi snapshot.  we recommend to stop I/O before taking a snapshot of an image. If the image contains a filesystem, the filesystem must be in a consistent state before taking a snapshot. To stop I/O you can use fsfreeze command.

<img src=http://i.imgur.com/khK02Ly.png>
Command:

`rbd snap create {pool-name}/{image-name}@{snap-name}`
`rbd snap rollback {pool-name}/{image-name}@{snap-name}`

<a name="2"></a>
##2. Layering

Ceph hỗ trợ khả năng copy-on-write(COW) clone of a block device snapshot.

<img src=http://i.imgur.com/eFDiGDl.png>

Mỗi cloned image(child) tham chiếu tới parent image, nó cho phép mở parent snapshot và đọc snapshot.

Note: Ceph chỉ hỗ trợ cloning định dạng 2 images ( rbd create --image-format 2)

Các bản cloned image có tham chiếu tới parent shapshot, bao gồm pool ID, image ID, snapshot ID. Có thể clone snapshot từ pool này sang pool khác. 
<ul>
<li>**Image Template**: Sử dụng block device layering để tạo master image và snapshot mẫu phục vụ cho việc clone.
<li>**Extended Template**: Cung cấp khả năng mở rộng cho image mẫu để cung cấp nhiều thông tin hơn base image. 
<li>**Tempate Pool**: Sử dụng block device layering để tạo pool chứa master images và snapshot mẫu. 
<li>**Image Migration/Recovery**: Sử dụng block device layering để migrate or recover data từ pool này sang pool khác.
</ul> 

<a name="3"></a>
##3. RBD Mirroring

RBD images có thể mirrored trên 2 cụm Ceph cluster thông qua RBD journaling image. Mirroring đc cấu hình trên các pool trong peer cluster và có thể cấu hình tự động mirror tất cả các images trong 1 pool hoặc 1 nhóm images. 

`RBD mirroring requires the Ceph Jewel release, must have two Ceph clusters, each running the rbd-mirror daemon`


http://docs.ceph.com/docs/master/rbd/rbd-mirroring/

<a name="4"></a>
##4. Cache Settings

Ceph block device ko thể sử dụng page cache của linux vì vậy nó có 1 thành phần là `RBD caching` hoạt động giống như là ổ đĩa caching. 

<a name="5"></a>
##5. Block Devices and OpenStack

Ta có thể sử dụng Ceph Block Device images với OpenStack thông qua libvirt

<img src=http://i.imgur.com/CLMLPBq.png>

Ba thành phần của OpenStack tích hợp với Ceph block device:
<ul>
<li>Image
<li>Volume
<li>Guest disk: là một operating system disk. Trước bản Havana, ta khởi động một máy ảo thông qua volume của Cinder. Hiện nay có thể khởi động máy ảo bên trong Ceph mà ko cần Cinder. 
</ul>

Ceph doesn’t support QCOW2. Image format must be RAW.

http://docs.ceph.com/docs/master/rbd/rbd-openstack/











