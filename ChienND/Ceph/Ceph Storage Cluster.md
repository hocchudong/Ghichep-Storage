#Ceph Storage Cluster

Mục lục:

[1. Ceph objects](#1)

[2. The CRUSH algorithm](#2)

[3. Placement groups](#3)

[4. Ceph pools](#4)

[5. Erasure Code](#5)

[6. Cache Tiering](#6)

[7. Data Striping](#7)

[8. Ceph data management](#8)

====================

<a name="1"></a>
###1. Ceph objects

Một object thường bao gồm dữ liệu và metadata được gói lại với nhau và được cung cấp một định danh duy nhất, đảm bảo ko có object khác có cùng ID. 

<img src=http://i.imgur.com/8ckL7u0.png>

Một object không giới hạn bất kỳ loại hay lượng metadata, nó mang lại cho bạn sự linh hoạt để thêm một kiểu tùy chỉnh trong metadata.

Nó không sử dụng hệ thống phân cấp thư mục hoặc cấy trúc dạng cây để lưu trữ. Các object lưu trữ trên cùng một không gian.

**Locating objects**

Mỗi đơn vị data trong Ceph được lưu trữ dưới dạng object trong một pool. Một Ceph pool là một phân vùng lưu trữ.

Khi một cụm Ceph đc triển khai nó tạo ra một số pool mặc định như data, metadata, and RBD pools.

Sau khi MDS triển khai, nó tạo ra các objects trong metadata pool do yêu cầu của CephFS để giúp hoạt động chính xác. 


Command:

`# rados lspools`

`# rados -p metadata ls`

<a name="2"></a>
###2. The CRUSH algorithm (Controlled Replication Under Scalable Hashing)

Thuật toán CRUSH là cốt lõi trong cơ chế lưu trữ của Ceph. Ceph sử dụng CRUSH để xác định nơi dữ liệu sẽ ghi hoặc đọc. Thay vì lưu trữ metadata, CRUSH tính toán metadata theo yêu cầu.

**The CRUSH lookup** 

Công việc tính toán metadata được coi như là CRUSH lookup và được thực hiện khi cần. 

Khi đọc và ghi, data được chuyển thành các objects với object và pool names/ID. Object được băm với number of placement groups to generate a final placement group within the required Ceph pool. Các tính toán về placement group được chuyển tới CRUSH lookup để xác định vị trí OSD lưu trữ hoặc lấy dữ liệu. Các tính toán này đc thực hiện bên phía client nên ko ảnh hưởng tới cluster. 

Ví dụ:

<img src=http://i.imgur.com/TSV85XE.png>

**The CRUSH hierarchy**

CRUSH là một cơ sở hạ tầng đầy đủ, nó duy trì một hệ thống phân cấp lồng nhau cho các thành phần của cơ sở hạ tầng. Danh sách thiết bị bao gồm disk, node, rack, row, switch, power circuit, room, data center. Các thành phần này đc gọi là failure zones or CRUSH buckets. CRUSH map chứa danh sách các buckets có sẵn để tổng hợp vào physical locations. NÓ cũng bao gồm danh sách các rules giúp cho CRUSH biết làm như nào để replicate data giữa các Ceph pool.

Mô hình:

<img src=http://i.imgur.com/WI1z8y2.png>

Tùy thuộc vào infrastructure, CRUSH ghi và nhân bản data trên các failure zones vì vậy nó đảm bảo an toàn và sẵn sàng ngay cả khi một số thành phần fail. CRUSH ghi dữ liệu đồng đều lên các disk cluster. Đảm bảo các cluster disk được sử dụng như nhau.

**Recovery and rebalancing**

Khi thành phần failure zones bị lỗi. Ceph chò 300s trước khi nó ghi nhận OSD down, out và bắt đầu quá trình khôi phục. CRUSH sao chép data lên một số disk, những bản sao chép này đc sử dụng để khôi phục.

Khi một host hoặc disk mới đc thêm vào Ceph cluster. CRUSH bắt đầu replicate đảm bảo các disk đều đc sử dụng từ đó cải thiện hiệu suất của cluster

**Editing a CRUSH map**

Khi triển khai Ceph với Ceph-deploy, nó tạo ra một CRUSH map mặc định. CRUSH map chứa danh sách các OSD, các thành phần vật lý và các rule.

Ta có thể cấu hình lại CRUSH map theo mô hình mong muốn. 

1. Extract your existing CRUSH map. With -o, Ceph will output a compiled
CRUSH map to the file you specify:

`# ceph osd getcrushmap -o crushmap.txt`

2. Decompile your CRUSH map. With -d, Ceph will decompile the CRUSH
map to the file specified by -o:

`# crushtool -d crushmap.txt -o crushmap-decompile`

3. Edit the CRUSH map with any editor:

`# vi crushmap-decompile`

4. Recompile the new CRUSH map:

`# crushtool -c crushmap-decompile -o crushmap-compiled`

5. Set the new CRUSH map into the Ceph cluster:

`# ceph osd setcrushmap -i crushmap-compiled`




**Customizing a cluster layout**

1. Execute ceph osd tree to get the current cluster layout:

<img src=http://i.imgur.com/EQ3HvaZ.png>

2. Add a few racks in your Ceph cluster layout:
```sh
# ceph osd crush add-bucket rack01 rack
# ceph osd crush add-bucket rack02 rack
# ceph osd crush add-bucket rack03 rack
```
3. Move each host under specific racks:
```sh
# ceph osd crush move ceph-node1 rack=rack01
# ceph osd crush move ceph-node2 rack=rack02
# ceph osd crush move ceph-node3 rack=rack03
```
4. Now, move each rack under the default root:
```sh
# ceph osd crush move rack03 root=default
# ceph osd crush move rack02 root=default
# ceph osd crush move rack01 root=default
```
5. Check your new layout.

<img src=http://i.imgur.com/qhNfPII.png>

<a name="3"></a>
###3. Placement groups

Khi Ceph cluster nhận các requests data, nó tách thành các sections placement group (PG). Một PG là một tập hợp logic của các objects được replicate trên các OSDs. 

<img src=http://i.imgur.com/F5HAXyv.png>

Khuyến cáo 50 to 100 placement groups trên mỗi OSD. 

Less than 5 OSDs set pg_num to 128

Between 5 and 10 OSDs set pg_num to 512

Between 10 and 50 OSDs set pg_num to 1024

**Calculating PG numbers**

PG trên một cluster. 

`Total PGs = (Total_number_of_OSD * 100) / max_replication_count`

This result must be rounded up to the nearest power of 2. For example, if a Ceph
cluster has 160 OSDs and the replication count is 3, the total number of placement
groups will come as 5333.3, and rounding up this value to the nearest power of 2
will give the final value as 8192 PGs.

http://docs.ceph.com/docs/master/rados/operations/placement-groups/




<a name="4"></a>
###4. Ceph pools

Ceph pool là một phân vùng lưu trữ objects. Ceph pool giữ một số PG và đc phân phối trên các cluster. Pool đảm bảo dữ liệu bằng cách tạo một số object copies, đó là các bản replicate hoặc erasure codes. Khi tạo pool, ta có thể tùy chọn replica size. Mặc định là 2.

Pool cung cấp cùng với tùy chọn:
<ul>
<li>Resilience: Có thể tùy chọn số lượng OSD cho phép lỗi mà ko làm mất data. Với replicated pools ta có tỉ lệ copies/replicas của một object. Với erasure coded pools thì tỉ lệ là khối mã hóa ví dụ  m=2 in the erasure code profile.
<li>Placement Groups: Bạn có thể tùy chọn số PG cho pool.
<li>CRUSH Rules: Khi lưu data xuống pool CRUSH Rules ánh xạ tới các pool CRUSH để xác định các luật cho PG của object và  bản replicate của nó. Có thể tạo các CRUSH rule cho pool.
<li>Snapshots
<li>Set Ownership: Có thể cấu hình quyền sở hữu cho pool.
</ul>

<a name="5"></a>
###5. Erasure Code

Erasure Code pool là một loại thay cho pool replica nhằm tiết kiệm không gian.

Ví dụ:
`ceph osd pool create ecpool 12 12 erasurepool 'ecpool' created`

Nó giống với RAID5 và cần 3 hosts. Mặc định các erasure code profile duy trì cho 1 OSD và chỉ cần 1.5TB thay vì 2TB để lưu 1TB data. Các erasure code profile ko thể sửa đổi sau khi đc tạo.

<img src=http://i.imgur.com/h2594LV.png>

Các thông số quan trọng k, m, ruleset-failure-domain nó xác định chi phí lưu trữ và độ bền dữ liệu.

ví dụ:
```sh
$ ceph osd erasure-code-profile set myprofile \
   k=3 \
   m=2 \
   ruleset-failure-domain=rack
```

Các object sẽ đc chia làm 3 và m xác định bao nhiêu OSD có thể hỏng cùng lúc mà ko mất dữ liệu. Số OSD dùng là m+k.

<a name="6"></a>
###6. Cache Tiering

Cache tier cung cấp I/O tốt hơn với việc tối ưu các lớp data đc lưu ở backing storage tier. Cache đc sắp xếp với nhau để tạo thành một pool với các storage device có tốc độ nhanh ví dụ SSD. Chúng đc cấu hình tạo thành `cache tier` và `backing pool` cho erasure-coded. Ceph objecter handles nơi chứa Objects và tiering agent xác định khi chuyển Objects từ cache sang backing storage tier, quá trình này trong suốt với người dùng. 

<img src=http://i.imgur.com/4txRkbR.png>

Cache tiering agent tiến hành migration data giữa cache tier and the backing storage tier tự động. 

2 chế độ migration

- Writeback Mode: Ceph client ghi data lên cache tier và nhận ACK từ cache tier. Lúc này, data đang đc ghi vào cache tier migrates tới storage tier. Cache tier đc đặt trước backing storage tier. Khi Ceph client cần data ở storage tier, cache tiering agent migrates the data to the cache tier. Sau đó nó chuyển tới Ceph client với I/O tối ưu. 

- Read-proxy Mode: Chế độ này sẽ dùng các Objects đang có trong cache tier, nếu Objects ko có trong cache thì sẽ chuyển yêu cầu xuống bên dưới.

http://docs.ceph.com/docs/master/rados/operations/cache-tiering/

<a name="7"></a>
###7. Data Striping

Thiết bị lưu trữ có giới hạn về lưu lượng, nó tác động tới hiệu suất và khả năng tính toán. Vì vậy hệ thống hỗ trợ **Striping** trên nhiều thiết bị để tăng hiệu suất. 

Ceph striping giống như chế độ RAID 0

Ví dụ:

<img src=http://i.imgur.com/UPpWgx3.png>

Trên hình ví dụ có 1 đoạn dữ liệu 123456. 123 được chia cho `Object set` 1. 456 được chia cho `Object set` 2. Các Object set tiếp tục phân chia thành các `Objects` theo số OSD. Sau đấy Striping nhỏ ra `strip unit` lưu trữ lên OSD.

Với tính năng này thay vì xử lý lần lượt từ đầu đến cuối dữ liệu, ta xử lý các đoạn dữ liệu nhỏ cùng 1 lúc. 

<a name="8"></a>
###8. Ceph data management

<img src=http://i.imgur.com/29nO9V3.png>

<ul>
<li>osdmap e566: This is the OSD map version ID or OSD epoch 556.
<li>pool 'HPC_Pool' (10): This is a Ceph pool name and pool ID.
<li>object 'object1': This is an object name.
<li>pg 10.bac5debc (10.3c): This is the placement group number.
<li>up [0,6,3]: This is the OSD up set that contains osd.0, osd.6, and
osd.3. Since the pool has a replication level set to 3.
<li>acting [0,6,3]: osd.0, osd.6, and osd.3 are in the acting set
where osd.0 is the primary OSD, osd.6 is the secondary OSD, and
osd.3 is the tertiary OSD.
</ul>

<img src=http://i.imgur.com/MR4JqL9.png>

Tham khảo:

[1]- http://docs.ceph.com/docs/master/rados/

