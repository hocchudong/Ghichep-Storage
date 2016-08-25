Mục lục:

[1. Pool operations]

<a name="1"></a>
##1. Pool operations

- Create a pool, web-services, with 128 PG and PGP numbers. This will
create a replicated pool as it's the default option.

`# ceph osd pool create web-services 128 128` 

- The listing of pools 
```sh
# ceph osd lspools
# rados lspools
# ceph osd dump | grep -i pool
```

- Replication size for a Ceph pool

`# ceph osd pool set web-services size 3` 

- Rename a pool

`# ceph osd pool rename web-services frontend-services` 

- Snapshots

<img src=http://i.imgur.com/LZCgJ1h.png>

- Removing a pool will also remove all its snapshots. After removing a pool,
you should delete CRUSH rulesets if you created them manually. If you
created users with permissions strictly for a pool that no longer exists, you
should consider deleting these users too:

`# ceph osd pool delete frontend-services frontend-services --
yes-i-really-really-mean-it`











