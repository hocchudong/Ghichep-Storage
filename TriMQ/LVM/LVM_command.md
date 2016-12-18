# Ghi chép về LVM

- Trong bài viết này tôi sẽ viết tắt 1 số từ như **pv** là physical volume, **vg** là volume group, **lv** là logical volume

- Tài liệu sẽ update thêm...
# Mục lục:
- [1. Các câu lệnh sử dụng trong LVM](#1)
	- [1.1 Physical volume](#11)
	- [1.2 Volume group](#12)
	- [1.3 Logical volume](#13)
	- [1.4 Sử dụng câu lệnh echo với LVM](#14)
	- [1.5 Mô hình lab] (#15)
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

<a name="14"></a>
### 1.4 Sử dụng câu lệnh echo với LVM
- Ví dụ như sau:
```sh
echo -e "n\np\n1\n\n\t\n8e\nw\n" | fdisk /dev/sdb > /dev/null
```
- echo -e là cú pháp lệnh cho phép diễn giải các tùy chọn sau dấu `\`

- Đây là câu lệnh để tạo ra 1 phân vùng và stdout không in ra màn hình mà sẽ được điều hướng vào `dev/null`. Có 2 dạng được xuất ra màn hình là **stdout** và **stderr**.

==> Với cú pháp câu lệnh như trên thì sẽ đẩy stdout vào trong thư mục `dev/null` (xem như đây là 1 thư mục thùng rác) còn stderr sẽ đẩy ra console

- Để ý tới dòng `n\np\n1\n\n\n\t\n8e\nw\n`. Khi bạn tạo 1 phân vùng bằng tay với lệnh `fdisk` sẽ có những tùy chọn để thực hiện tạo phân vùng ví dụ như: n để tạo 1 phân vùng mới, p là chọn phân vùng primary, t để đổi định dạng của phân vùng, 8e là số hex code để đổi phân vùng thành Linux LVM

==> Với lệnh `fdisk /dev/sdb` và các tùy chọn như trên bằng lệnh `echo -e` ta đã tạo được 1 phân vùng mới

- <b>Chú ý</b>: Để ý với cú pháp trên có `\n` có nghĩa là sau kí hiệu \n sẽ xuống dòng và nhập tùy chọn. Ví dụ `\np` có nghĩa là xuống dòng và gõ tùy chọn p (chọn primary partition)


<a name="15"></a>
### 1.5 Mô hình lab
- Hệ thống lưu trữ LVM của tôi như sau
<img src="http://i.imgur.com/pH1fk9J.png">

==> Do tôi đã sử dụng hết dung lượng 15GB của volume group **vg1** nên không thể tạo ra các logical volume để sử dụng tiếp. Do vậy tôi cần phải add thêm ổ cứng vào vào gán vào volume group để có thể tạo logical volume và sử dụng

- Đầu tiên tôi sẽ gán thêm 1 ổ cứng là **sdb** có dung lượng 10GB vào máy và phân vùng cho ổ cứng đó 

- Bước tiếp theo tạo physical volume từ phân vùng sdb1 vừa tạo
```sh
pvcreate /dev/sdb1
```

- Mở rộng volume group `vg1`
```sh
vgextend /dev/vg1 /dev/sdb
```

- Tạo logical volume `lv3`
```sh
lvcreate -L 10GB -n lv3 vg1
```

- Format và sử dụng
```sh
mkfs -t ext4 /dev/vg1/lv3
```

- Các thao tác trên được mô tả như mô hình sau đây

<img src="http://i.imgur.com/NK4RmOs.png">

- Ngoài việc tạo ra các logical volume để sử dụng, ta cũng có thể mở rộng logical volume để sử dụng
```sh
lvextend -L +1G /dev/vg1/lv2
```
- <b>Chú ý</b>: Trước khi mở rộng hay cắt bớt dung lượng logical volume cần phải xem trạng thái của logical volume có thể thay đổi không
```sh
vgdisplay
```

<img src="http://i.imgur.com/QaiZR2F.png">


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
<img src="http://i.imgur.com/a92CPMT.png">

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

<a name="3"></a>
## Tài liệu tham khảo
- [1]- http://www.computerhope.com/unix/fdisk.htm
- [2]- https://www.cyberciti.biz/faq/linux-viewing-drive-partitions-with-fdisk-parted/

	

 
 



