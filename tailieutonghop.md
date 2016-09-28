#Tài liệu nghiên cứu chuyên đề Storage 

Đây là bài viết tổng hợp một số tài liệu hữu ích về Storage mà các trường đại học nghiên cứu, để có thể nắm được 1 nền tảng vững chắc giúp hiểu được tài liệu và tư duy hiệu quả.

#Mục lục:

**Table of Content**

- [1. University of California, Santa Cruz - Prof. Scott A. Brandt - Advance topics in Computer System: Storage System ](#1)

- [2. Stanford - Secure Computer Systems - David Mazières - Distributed Storage Systems](#2)

- [3. The University of UTAH - Ryan Stutsman - Distributed Systems](#3)

=======================================================================================

<a name="1"></a>
##1. University of California, Santa Cruz - Prof. Scott A. Brandt - Advance topics in Computer System: Storage System

Tài liệu nghiên cứu này được tổng hợp từ trước đến hiện tại, từ những nền tảng cơ bản để hoàn thành thiết kế filesystem. Những tài liệu này rất hữu ích để hiểu được các động lực để thiết kế hệ thống lưu trữ ngày nay. Yêu cầu trả lời câu hỏi dưới đây sau khi đọc.


- Computer Architecture: A Quantitative Approach Chapter 6 [4th-edition in 2007, 14823 refs]
(http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.115.1881&rep=rep1&type=pdf) 
```sh
Tài liệu nghiên cứu khá tốt, tuy nhiên những vấn đề nêu ra là quá cũ
```

- An Introduction to Disk Drive Modelling [1994, 1063 refs]
http://pages.cs.wisc.edu/~remzi/Classes/838/Fall2001/Papers/diskmodel-computer94.pdf
```sh
Mô phỏng ổ đĩa
Ổ đĩa quay khá phức tạp nên ổ SSD được sử dụng nhiều hơn
```


- A Fast File System for UNIX [1984, 1195 refs]
https://cs162.eecs.berkeley.edu/static/readings/FFS84.pdf
```sh
Tài liệu viết về filesystem, khá cũ
Những cải tiến trong filesystem
```

- MDS Functionality Analysis [2001, 0 refs]
(https://users.soe.ucsc.edu/~sbrandt/290S/xue.pdf)
```sh
Sử dụng đối tượng lưu trữ như là filesystem
Giải quyết các vấn đề trong filesystem
```


- A Trace-Driven Analysis of the UNIX 4.2 BSD File System [1985, 748 refs]
(https://users.soe.ucsc.edu/~sbrandt/290S/trace.pdf)



- Measurements of a Distributed File System [1991, 767 refs]
(https://www.eecs.harvard.edu/cs261/papers-a1/baker91.pdf)



- UNIX disk access patterns [1993, 532 refs]
(http://www.hpl.hp.com/techreports/92/HPL-92-152.pdf)
```sh
Mô hình truy cập ổ đĩa
The caching
Trace method
```


- File System Aging—Increasing the Relevance of File System Benchmarks [1997, 130 refs]
(https://www.eecs.harvard.edu/margo/papers/sigmetrics97-fs/paper.pdf)




- Scheduling Algorithms for Modern Disk Drives [1994, 333 refs]
(https://users.soe.ucsc.edu/~sbrandt/290S/scheduling.pdf)



- Disk Scheduling Algorithms Based on Rotational Position [1991, 244 refs]
(https://users.soe.ucsc.edu/~sbrandt/290S/scheduling2.pdf)



- The LFS Storage Manager [1990, 300 refs]
(http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.104.1363&rep=rep1&type=pdf)



- Today: Distributed File Systems NFS Architecture & Sun's Network File System (NFS) - Pages
(http://lass.cs.umass.edu/~shenoy/courses/spring03/lectures/Lec20.pdf)



- Extent-Like Performance from a Unix File System [1991, 226 refs]
http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.55.2970&rep=rep1&type=pdf


- Deciding when to forget in the Elephant file system [1999, 475 refs]
https://cseweb.ucsd.edu/classes/fa01/cse221/papers/santry-efs-sosp99.pdf


- Storage Management for Web Proxies [2001, 66 refs]
http://www.eecs.harvard.edu/~syrah/filesystems/papers/shriver_2001.pdf


- RAID:High-Performance, Reliable Secondary Storage [1994, 1492 refs]
https://web.eecs.umich.edu/~pmchen/papers/chen94_1.pdf
```sh
Giới thiệu công nghệ RAID
Các cải tiến trong công nghệ RAID
```


- Scalability in the XFS File System [1996, 400 refs]





- An Empirical Study of a Wide-Area Distributed File System [1996, 95 refs]
http://www.cs.cmu.edu/~coda/docdir/spasojevic96.pdf


- Caching in the Sprite Network File System [1988, 689 refs]


- Disconnected Operation in the Coda File System [1992, 1439 refs]


- The Zebra Striped Network File System [1995, 327 refs]
https://users.soe.ucsc.edu/~sbrandt/290S/zebra.pdf


- SWIFT/RAID: a distributed raid system [1994, 152 refs]
https://www.usenix.org/legacy/publications/compsystems/1994/sum_long.pdf


- Anticipatory Scheduling: A Disk Scheduling Framework to Overcome Deceptive Idleness in Synchronous I/O [2001, 272 refs]
http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.29.1503&rep=rep1&type=pdf


- A Low-bandwidth Network File System [2001, 1002 refs]
https://users.soe.ucsc.edu/~sbrandt/290S/lbfs.pdf


- File Server Scaling with Network-Attached Secure Disks [1997, 313 refs]
https://users.soe.ucsc.edu/~sbrandt/290S/nasd.pdf


- RAMA: An Easy-To-Use, High-Performance Parallel File System [1997, 52 refs]
https://users.soe.ucsc.edu/~sbrandt/290S/rama.pdf



- Serverless network file systems [1995, 606 refs]
https://users.soe.ucsc.edu/~sbrandt/290S/xFS.pdf



- Frangipani: A Scalable Distributed File System [1997, 590 refs]
https://users.soe.ucsc.edu/~sbrandt/290S/frangipani.pdf



- File-Access Characteristics of Parallel Scientific Workloads [1996, 237 refs]
https://users.soe.ucsc.edu/~sbrandt/290S/pfw.pdf



- The Vesta Parallel File System [1996, 307 refs]
https://users.soe.ucsc.edu/~sbrandt/290S/vesta.pdf



- HFS: A Performance-Oriented Flexible File System Based on Building-Block Compositions [1997, 73 refs]
https://users.soe.ucsc.edu/~sbrandt/290S/hfs.pdf



- OceanStore: An Architecture for Global-scale Persistent Storage [2000, 2874 refs] & Web Portal
https://users.soe.ucsc.edu/~sbrandt/290S/oceanstore.pdf

<a name="2"></a>
##2. Stanford - Secure Computer Systems - David Mazières - Distributed Storage Systems

- Soft Updates - a method for metadata consistency, Soft Updates: A Technique for Eliminating Most Synchronous Writes in the Fast Filesystem [1999, 121 refs]
http://www.scs.stanford.edu/06wi-cs240d/notes/l8d.txt
https://www.usenix.org/legacy/event/usenix99/full_papers/mckusick/mckusick.pdf


- Managing Update Conflicts in Bayou, a Weakly Connected Replicated Storage System [1995, 1112 refs],
https://people.cs.umass.edu/~mcorner/courses/691M/papers/terry.pdf



<a name="3"></a>
##3. The University of UTAH - Ryan Stutsman - Distributed Systems
Bài giảng về các khái niệm quan trọng trong hệ thống phân phối và chuyên sâu về thiết kế hệ thống. 

- Chord: A Scalable Peer-to-peer Lookup Service for Internet Applications [2001, 12405 refs]
https://pdos.csail.mit.edu/papers/chord:sigcomm01/chord_sigcomm.pdf


 
