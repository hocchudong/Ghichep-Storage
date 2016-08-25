Hardware planning

1. MON

Single core CPU with a few gigabytes of memory is good enough in most cases for being a monitor node.

If you have configured your cluster to store logs on local monitor node, make sure you  have a sufficient amount of local disk space on the monitor node to store the logs.

A redundant NIC with 1 Gbps is sufficient

2. OSD requirements

• CPU and memory

• An OSD journal (block device or a file)

• An underlying filesystem (XFS, ext4, and Btrfs)

• A separate OSD or cluster redundant network (recommended)

Should have is 1 GHz of CPU and 2 GB of RAM per OSD.

3. Network requirements

Least two separate networks, one for front-side data network or public networks and the other for the backside data network or clustered network.

4. MDS requirements

They require significantly high CPU processing powers with quad core or more. should keep Ceph MDS on dedicated physical machines with relatively more physical CPU cores and memory. A redundant network interface with 1 GB or more speed will work for most cases for Ceph MDS.