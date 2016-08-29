#CEPH FILESYSTEM

Ceph Filesystem (Ceph FS) là một [POSIX-compliant filesystem](https://en.wikipedia.org/wiki/POSIX) sử dụng Ceph Storage Cluster để lưu data. Ceph filesystem sử dụng Ceph Storage Cluster giống như Ceph Block Devices, Ceph Object Storage with its S3 and Swift APIs, or native bindings (librados).
 
<img src=http://i.imgur.com/tWbUod4.png>
 
Để dùng Ceph FS ta cần có `Ceph Metadata Server`. 

**Enable the filesystem**

`$ ceph fs new <fs_name> <metadata> <data>`

`$ ceph fs new cephfs cephfs_metadata cephfs_data` 

**Mount CEPH FS** 

```sh
sudo mkdir /mnt/mycephfs
sudo mount -t ceph 192.168.0.1:6789:/ /mnt/mycephfs
```

**Mount authentication**

`sudo mount -t ceph 192.168.0.1:6789:/ /mnt/mycephfs -o name=admin,secret=AQATSKdNGBnwLhAAnNDKnH65FmVKpXZJVasUeQ==`

Hoặc

`sudo mount -t ceph 192.168.0.1:6789:/ /mnt/mycephfs -o name=admin,secretfile=/etc/ceph/admin.secret`

**MOUNT CEPH FS IN FILE SYSTEMS TABLE**

Ví dụ:
`10.10.10.10:6789:/     /mnt/ceph    ceph    name=admin,secretfile=/etc/ceph/secret.key,noatime,_netdev    0       2`


**MOUNT CEPH FS AS A FUSE**

**Các tính năng thử nghiệm**
<ul>
<li>Directory Fragmentation: Mặc định CephFS đc lưu trữ trong một object RADOS duy nhất. Nó sẽ gây ra vấn đề khi kích thước lớn lên. Với kích thước lớn thì các  filesystem sẽ đc phân mảnh vào nhiều object để lưu trữ.
<li>Inline data: Tính năng  inline data hỗ trợ các file nhỏ <2KB đc chứa ở inode. Nó giúp tăng hiệu suất.
<li>MULTI-MDS FILESYSTEM CLUSTERS
<li>SNAPSHOTS 
<li>MULTIPLE FILESYSTEMS WITHIN A CEPH CLUSTER
</ul>




