#Block Ceph with OpenStack

<img src=http://i.imgur.com/cSSCBUc.png>

A. CONFIGURE CEPH

1. Create Pool
```sh
ceph osd pool create volumes 128
ceph osd pool create images 128
ceph osd pool create backups 128
ceph osd pool create vms 128
```

2. CONFIGURE OPENSTACK CEPH CLIENTS

- Copy ceph.conf, ceph.client.admin.keyring

- Install

apt-get install python-rbd (glance-api)
apt-get install ceph-common (nova-compute, cinder-backup, cinder-volume)

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
82233076-a949-4884-a957-10191875061f
```
```sh
cat > secret.xml <<EOF
<secret ephemeral='no' private='no'>
  <uuid>457eb676-33da-42ec-9a8c-9293d545c337</uuid>
  <usage type='ceph'>
    <name>client.cinder secret</name>
  </usage>
</secret>
EOF
```

```sh
sudo virsh secret-define --file secret.xml
Secret 457eb676-33da-42ec-9a8c-9293d545c337 created
sudo virsh secret-set-value --secret 82233076-a949-4884-a957-10191875061f --base64 $(cat client.cinder.key) && rm client.cinder.key secret.xml
```

B. CONFIGURE OPENSTACK TO USE CEPH

1. Configure Glance

`vim /etc/glance/glance-api.conf`

```sh
[glance_store]

stores = rbd
rbd_store_pool = images
rbd_store_user = glance
rbd_store_ceph_conf = /etc/ceph/ceph.conf
rbd_store_chunk_size = 8
default_store = rbd
```

2. Configure Cinder

`vim/etc/cinder/cinder.conf`

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
rbd_secret_uuid = 82233076-a949-4884-a957-10191875061f
report_discard_supported = true
```

3. Configure Nova

`vim /etc/nova/nova.conf`
```sh
[libvirt]
images_type = rbd
images_rbd_pool = vms
images_rbd_ceph_conf = /etc/ceph/ceph.conf
rbd_user = cinder
rbd_secret_uuid = 82233076-a949-4884-a957-10191875061f
```

4. Restart service
```sh
sudo glance-control api restart
sudo service nova-compute restart
sudo service cinder-volume restart
sudo service cinder-backup restart
```

















