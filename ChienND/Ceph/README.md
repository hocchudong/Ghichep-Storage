#Ceph

Ceph là giải pháp mã nguồn mở để xây dựng hạ tầng lưu trữ phân tán, ổn định, hiệu năng cao. Nền tảng của Ceph là Object từ đó hình thành nên các dạng lưu trữ khối (Block), tệp dữ liệu (File). Định dạng dữ liệu block, file đều đc lưu dưới dạng object trong placement group của Ceph cluster.

Tính năng:
<ul>
<li>Thay thế lưu trữ trên ổ đĩa server thông thường
<li>Backup, dự phòng
<li>Triển khai các dịch vụ High Avaibility như Load Balancing for Web Server, DataBase Replication…
<li>Giải quyết bài toán lưu trữ cho điện toán đám mây
<li>Khả năng mở rộng, sử dụng nhiều phần cứng khác nhau
<li>Ko tồn tại "single point of failure"
</ul>

Công nghệ Ceph:
	
<img src=http://i.imgur.com/ih0lt0e.png>

Kiến trúc Ceph:

<img src=http://i.imgur.com/1qQeFnI.png>

Mô hình mạng:

<img src=http://i.imgur.com/8peefH6.png>

[1. Thành phần:]()

- [Ceph RADOS](https://github.com/hocchudong/Ghichep-Storage/blob/master/ChienND/Ceph/Ceph%20RADOS.md)

- [Librados](https://github.com/hocchudong/Ghichep-Storage/blob/master/ChienND/Ceph/Librados.md)

[2.Khái niệm trong Ceph Cluster](https://github.com/hocchudong/Ghichep-Storage/blob/master/ChienND/Ceph/Ceph%20Storage%20Cluster.md)

[3. WorkFlow](https://github.com/hocchudong/Ghichep-Storage/blob/master/ChienND/Ceph/WorkFlow.md)

[4. Install Ceph](https://github.com/chiennd/Ghichep-Storage/blob/master/ChienND/Ceph/Install%20Ceph-Jewel.md)

[5. Install Ceph-Deploy](https://github.com/chiennd/Ghichep-Storage/blob/master/ChienND/Ceph/Install%20Ceph-deploy.md)

[6. Configure Block Ceph with OpendStack](https://github.com/chiennd/Ghichep-Storage/blob/master/ChienND/Ceph/Configure%20Block%20Ceph%20with%20OpenStack.md)

Tham khảo:

[1]- http://docs.ceph.com/docs/hammer/

[2]- https://drive.google.com/file/d/0B3maQ7zepTSLRmtScUd5YnUtczA/view?usp=sharing

[3]- http://www.ssrc.ucsc.edu/Papers/weil-osdi06.pdf





