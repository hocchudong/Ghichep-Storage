#Block Ceph with OpenStack

Mục lục:

[A. CẤU HÌNH CEPH](#A)

[B. CẤU HÌNH OPENSTACK VỚI BACKEND CEPH](#B)

[C. TEST BACKEND](#C)

<img src=http://i.imgur.com/cSSCBUc.png>

**Chú ý:** Phiên bản Ceph trên backend ceph và OpenStack phải giống nhau. 

<a name="A"></a>
###A. CẤU HÌNH CEPH

**1. Create Pool**

```sh
ceph osd pool create volumes 128
ceph osd pool create images 128
ceph osd pool create backups 128
ceph osd pool create vms 128
```

**2. CÀI ĐẶT SERVICE CHO OPENSTACK**

- Install:

```sh
apt-get install python-rbd (Cho glance-api)
apt-get install ceph-common (Cho nova-compute, cinder-backup, cinder-volume)
```

- Copy `ceph.conf, ceph.client.admin.keyring` từ Ceph sang OpenStack.

- SETUP CEPH CLIENT AUTHENTICATION

```sh
ceph auth get-or-create client.cinder mon 'allow r' osd 'allow class-read object_prefix rbd_children, allow rwx pool=volumes, allow rwx pool=vms, allow rx pool=images'
ceph auth get-or-create client.glance mon 'allow r' osd 'allow class-read object_prefix rbd_children, allow rwx pool=images'
ceph auth get-or-create client.cinder-backup mon 'allow r' osd 'allow class-read object_prefix rbd_children, allow rwx pool=backups'
```

```sh
ceph auth get-or-create client.glance | tee /etc/ceph/ceph.client.glance.keyring 
chown glance:glance /etc/ceph/ceph.client.glance.keyring
ceph auth get-or-create client.cinder | tee /etc/ceph/ceph.client.cinder.keyring 
chown cinder:cinder /etc/ceph/ceph.client.cinder.keyring
ceph auth get-or-create client.cinder-backup | tee /etc/ceph/ceph.client.cinder-backup.keyring
chown cinder:cinder /etc/ceph/ceph.client.cinder-backup.keyring
```

`ceph auth get-key client.cinder | tee client.cinder.key`
```sh
uuidgen
212ba33a-5734-47af-8da4-84b3d39e03dc
```
```sh
cat > secret.xml <<EOF
<secret ephemeral='no' private='no'>
  <uuid>212ba33a-5734-47af-8da4-84b3d39e03dc</uuid>
  <usage type='ceph'>
    <name>client.cinder secret</name>
  </usage>
</secret>
EOF
```

```sh
sudo virsh secret-define --file secret.xml

sudo virsh secret-set-value --secret 212ba33a-5734-47af-8da4-84b3d39e03dc --base64 $(cat client.cinder.key) && rm client.cinder.key secret.xml
```

<a name="B"></a>
###B. CẤU HÌNH OPENSTACK VỚI BACKEND CEPH

**1. Glance**

`vim /etc/glance/glance-api.conf`

```sh
[glance_store]

show_image_direct_url = True
stores = rbd
rbd_store_pool = images
rbd_store_user = glance
rbd_store_ceph_conf = /etc/ceph/ceph.conf
rbd_store_chunk_size = 8
default_store = rbd
```

**2. Cinder**

`vim /etc/cinder/cinder.conf`

```sh
[DEFAULT]
enabled_backends = ceph
glance_api_servers = http://controller:9292
glance_api_version=2  #(configuring multiple cinder back ends)

backup_driver = cinder.backup.drivers.ceph
backup_ceph_conf = /etc/ceph/ceph.conf
backup_ceph_user = cinder-backup
backup_ceph_chunk_size = 134217728
backup_ceph_pool = backups
backup_ceph_stripe_unit = 0
backup_ceph_stripe_count = 0
restore_discard_excess_bytes = true

[ceph]
volume_driver = cinder.volume.drivers.rbd.RBDDriver
rbd_pool = volumes
rbd_ceph_conf = /etc/ceph/ceph.conf
rbd_flatten_volume_from_snapshot = false
rbd_max_clone_depth = 5
rbd_store_chunk_size = 4
rados_connect_timeout = -1
glance_api_version = 2

rbd_user=cinder
rbd_secret_uuid = 212ba33a-5734-47af-8da4-84b3d39e03dc
report_discard_supported = true
```

**3. Nova**

`vim /etc/nova/nova.conf`

```sh
[libvirt]
images_type = rbd
images_rbd_pool = vms
images_rbd_ceph_conf = /etc/ceph/ceph.conf
rbd_user = cinder
rbd_secret_uuid = 212ba33a-5734-47af-8da4-84b3d39e03dc
```

**4. Restart service**

```sh
sudo glance-control api restart
sudo service nova-compute restart
sudo service cinder-volume restart
sudo service cinder-backup restart
```

<a name="C"></a>
###C. Test backend

<img src=http://i.imgur.com/6uzig1C.png>
















