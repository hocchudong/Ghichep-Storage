# Ghi chép về LVM

- Trong bài viết này tôi sẽ viết tắt 1 số từ như **pv** là physical volume, **vg** là volume group, **lv** là logical volume

- Tài liệu sẽ update thêm...
# Mục lục:
- [1. Các câu lệnh sử dụng trong LVM](#1)
	- [1.1 Physical volume](#11)
	- [1.2 Volume group](#12)
	- [1.3 Logical volume](#13)
- [2. Phân vùng 1 ổ đĩa](#2)
- [3. Tài liệu tham khảo](#3)

==============================

<a name="1"></a>
## 1. Các câu lệnh sử dụng trong LVM
Đây là những câu lệnh thông dụng để làm việc với LVM


<a name="11"></a>
### 1.1 Physical volume:
- Câu lệnh với cú pháp `pv...` đây là những câu lệnh làm việc với physical volume

- Tạo 1 physical volume
```sh
pvcreate
```

- Hiển thị thuộc tính của physical volume
```sh
pvdisplay
```

- Điều chỉnh dụng lượng của physical volume
```sh
pvresize
```

- Xóa physical volume
```sh
pvremove
```

- Quét tất cả ổ đĩa là physical volume
```sh
pvscan
```

- Hiển thị các thông số của physical volume
```sh
pvs
```

<a name="12"></a>
### 1.2 Volume group
- Các câu lệnh làm việc với volume group với cú pháp `vg`.

- Tạo volume:
```sh
vgcreate [tên các pv để tạo volume group]
```

- Hiển thị các thuộc tính của volume group
```sh
vgdisplay
```

- Mở rộng volume group
```sh
vgextend [tên pv]
```

- Xóa các physical volume ra khỏi volume group
```sh
vgreduce [tên pv]
```

- Xóa volume group
```sh
vgremove
```

- Xem các thông số của volume group
```sh
vgs
```

<a name="13"></a>
### 1.3 Logical volume
- Các câu lệnh làm việc với logical volume với cú pháp `lv`

- Tạo logical volume
```sh
lvcreate
```

- Hiển thị các thuộc tính của logical volume
```sh
lvdisplay
```

- Mở rộng logical volume. Lưu ý: chỉ mở rộng khi volume group còn dung lượng trống
```sh
lvextend
```

- Xóa logical volume khỏi volume group
```sh
lvremove
```

-Thay đổi dung lượng logical volume
```sh
lvresize
```

<a name="2"></a>
## 2. Phân vùng 1 ổ đĩa
Các thao tác làm việc với LVM đều liên quan đến ổ đĩa, sau đây là những thao tác làm việc với ổ đĩa cần biết

- Xem các ổ đĩa có trong máy
```sh
lsblk
```

- Phân vùng 1 ổ đĩa, sử dụng lệnh `fdisk`
```sh
fdisk /dev/sdX/
```
	- Với X là tham số tên ổ đĩa ví dụ: sdb, sdc..
	- Với câu lệnh này sẽ cho chúng ta các option như sau
	
	```sh
	n: Tạo partition mới
	d: xóa phân vùng
	t: thay đổi số id của hệ thống phân vùng
	w: ghi lại bảng parttion
	```
	
- Xem thông số partition
```sh
fdisk -l
```

- Các thông số quan trọng của ổ đĩa như:
<img src="">

<ul>
</li>Dung lượng</li>
<li>Số sector</li>
</ul>

- Có thể dựa vào số sector để tính được dụng lượng của ổ đĩa
```sh
dung lượng (bytes)= số sector*521
```

- Khi phân vùng ổ đĩa cần chú ý những thông số sau
<ul>
<li>Chia làm 2 loại là primary và extended</li>
<li>Với ổ đĩa chỉ chia đc tối đa là 4 partition chính (primary)</li>
<li>2 giá trị first sector và last sector sẽ quyết định xem phân vùng mà bạn chia có dung lượng là bao nhiêu, tương đương với số lượng sector mà phân vùng có</li>
</ul>

	

 
 



