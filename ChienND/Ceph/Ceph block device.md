#CEPH BLOCK DEVICE
Mục lục:

[1. Snapshot](#1)

[2. Layering](#2)

====================

Ceph Block Device (RBD) là một chuỗi các byte. Nó là thin-provisioned, resizable và data đc lưu trên nhiều OSDs. RBD tương tác với OSD thông qua kernel modules hoặc librbd library.. RBD có tính năng snapshot, nhân bản, nhất quán. RBD cung cấp hiệu suất cao với khả năng mở rộng 

Note: Kernel modules can use Linux page caching. For librbd-based applications, Ceph supports RBD Caching.

<a name="1"></a>
1. Snapshot 

Ceph hỗ trợ snapshot layering, nó cho phép clone image nhanh chóng và dễ dàng. Ceph hỗ trợ block device snapshots bằng `rbd` command và interfaces. 

Để sử dụng RBD snapshots, ta cần chạy Ceph cluster. Khuyến cáo nên dừng I/O trước khi snapshot.  we recommend to stop I/O before taking a snapshot of an image. If the image contains a filesystem, the filesystem must be in a consistent state before taking a snapshot. To stop I/O you can use fsfreeze command.

<img src=http://i.imgur.com/khK02Ly.png>
Command:

`rbd snap create {pool-name}/{image-name}@{snap-name}`
`rbd snap rollback {pool-name}/{image-name}@{snap-name}`

<a name="2"></a>
2. Layering

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

