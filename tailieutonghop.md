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
```sh
1. Trả lời các câu hỏi về cách tiếp cận mô hình cơ bản:
 - How much network bandwidth is needed to support a diskless workstation?
 - Mô hình truy cập file là gì?
 - Tại sao cần quản lý disk block cache?
2. Các mô hình chính với dữ liệu
3. Làm sao để thu thập dữ liệu và đo lường, tránh 1 lượng lớn các volume của metric
```




- Measurements of a Distributed File System [1991, 767 refs]
(https://www.eecs.harvard.edu/cs261/papers-a1/baker91.pdf)
```sh
Key finding
- Các mô hình truy cập hiện nay giống với mô hình 6 năm trước
- Xem bảng dữ liệu
- Chia sẻ quyền truy cập dữ liệu xảy ra thường xuyên, do đó cần có 1 cache thống nhất
Token-based của thuật toán cho phép đồng thời ghi và chia sẻ
Phần tóm tắt tương đối tốt
```


- UNIX disk access patterns [1993, 532 refs]
(http://www.hpl.hp.com/techreports/92/HPL-92-152.pdf)
```sh
Mô hình truy cập ổ đĩa
The caching
Trace method
```


- File System Aging—Increasing the Relevance of File System Benchmarks [1997, 130 refs]
(https://www.eecs.harvard.edu/margo/papers/sigmetrics97-fs/paper.pdf)
```sh
Test fileysystem dựa trên aging, kết quả chính của aging là các thuật toán fragmentation
Phương pháp để Replay aging
Layout score: Biện pháp phân mảnh
Ý nghĩa của phân mảnh, phân mảnh trong filesystem
```



- Scheduling Algorithms for Modern Disk Drives [1994, 333 refs]
(https://users.soe.ucsc.edu/~sbrandt/290S/scheduling.pdf)
```sh
Disk scheduler (on hardware disk) scheduling algorithms FCFS vs SSTF vs Scan (elevator) vs C-SCAN vs Look vs C-Look vs VSCAN
Các thuật toán sử dụng hiệu quả giúp tăng hiệu suất đáng kể đọc và tính liên tục (C-look)
```

- Disk Scheduling Algorithms Based on Rotational Position [1991, 244 refs]
(https://users.soe.ucsc.edu/~sbrandt/290S/scheduling2.pdf)
```sh
- Thuật toán OAT
- The Aged Shortest Access Time First(w) - ASATF(w) algorithm
   M = wTage – TA/τ
- Thế nào là thử nghiệm đánh giá
```


- The LFS Storage Manager [1990, 300 refs]
(http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.104.1363&rep=rep1&type=pdf)
```sh
Bắt đầu của cấu trúc log filesystem
Design:
 - Write: Buffer trong file cache, 
 - Read: use inode map to index inode location
 - Disk space management
 - Recovery
 - Performance
```

- Today: Distributed File Systems NFS Architecture & Sun's Network File System (NFS) - Pages
(http://lass.cs.umass.edu/~shenoy/courses/spring03/lectures/Lec20.pdf)
```sh
- Ý nghĩa của NFS
- Thống nhất bộ nhớ cache là thách thức
- NFSv4 nhận được nhiều sự chú ý
```



- Extent-Like Performance from a Unix File System [1991, 226 refs]
http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.55.2970&rep=rep1&type=pdf
```sh
Về cơ bản có 2 cách tiếp cận thiết kế FS
Những thứ đang được sử dụng nhiều để thiết kế filesystem
Những thiết kế trước đây và các vấn đề
Những vấn đề mới
```


- Deciding when to forget in the Elephant file system [1999, 475 refs]
https://cseweb.ucsd.edu/classes/fa01/cse221/papers/santry-efs-sosp99.pdf
```sh
Sự bắt đầu của các phiên bản filesystem
Duy trì đặc tính của hệ thống file
```

- Storage Management for Web Proxies [2001, 66 refs]
http://www.eecs.harvard.edu/~syrah/filesystems/papers/shriver_2001.pdf
```sh
Filesystem được thiết kế để caching web proxy
Web proxy
Designed
 - Hummingbird quản lý lượng lớn cache
 - Flat namespace
 - decide locality set bởi LRU truy cập ứng dụng
```



- RAID:High-Performance, Reliable Secondary Storage [1994, 1492 refs]
https://web.eecs.umich.edu/~pmchen/papers/chen94_1.pdf
```sh
Giới thiệu công nghệ RAID
Các cải tiến trong công nghệ RAID
```


- Scalability in the XFS File System [1996, 400 refs]
```sh
Tài liệu về XFS
Thiết kế XFS
```

- An Empirical Study of a Wide-Area Distributed File System [1996, 95 refs]
http://www.cs.cmu.edu/~coda/docdir/spasojevic96.pdf
```sh
Andrew File System
Thiết kế
 - Cell
 - RPC
Finding
 - hazard rate
 - Thống kê toàn diện
So sánh với World Wide Web
```



- Caching in the Sprite Network File System [1988, 689 refs]
```sh
Beginning papers để giới thiệu cache trong hệ thống storage
Thiết kế
 - Sprite network operating system
 - Sprite guarantees
 - Sprite cache
 - Cache structure
 - Cache consistency
 - Sử dụng virual memory và swap để kiểm soát kết nối giữa cache và user program
```


- Disconnected Operation in the Coda File System [1992, 1439 refs]


- The Zebra Striped Network File System [1995, 327 refs]
https://users.soe.ucsc.edu/~sbrandt/290S/zebra.pdf
```sh
Sprite operating system paper, chỉ ra cấu trúc log filesystem
```


- SWIFT/RAID: a distributed raid system [1994, 152 refs]
https://www.usenix.org/legacy/publications/compsystems/1994/sum_long.pdf
```sh
Cách để xây dựng storage FS
Thiết kế
 - Transfer plan executor
 - Experience with network
```

- Anticipatory Scheduling: A Disk Scheduling Framework to Overcome Deceptive Idleness in Synchronous I/O [2001, 272 refs]
http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.29.1503&rep=rep1&type=pdf
```sh
IO scheduler sử dụng trong Linux
Key finding
 - Deceptive idleness
Thiết kế
```

- A Low-bandwidth Network File System [2001, 1002 refs]
https://users.soe.ucsc.edu/~sbrandt/290S/lbfs.pdf
```sh
Thiết kế
- LBFS provides close-to-open consistency
- LBFS uses are large persistent file cache at client
- Giảm bandwidth bởi exploiting similarities giữa các file
- NFS RPC handling
- File read and write
- Security concerns
```

- File Server Scaling with Network-Attached Secure Disks [1997, 313 refs]
https://users.soe.ucsc.edu/~sbrandt/290S/nasd.pdf
```sh
Tài liệu này thảo luận về cấu trúc của NAS
So sánh với SAD, SID, NetSCSI, NASD
```

- RAMA: An Easy-To-Use, High-Performance Parallel File System [1997, 52 refs]
https://users.soe.ucsc.edu/~sbrandt/290S/rama.pdf
```sh
Sử dụng các thuật toán để chia dữ liệu trên đĩa
```


- Serverless network file systems [1995, 606 refs]
https://users.soe.ucsc.edu/~sbrandt/290S/xFS.pdf
```sh
Tài liệu về xFS
```


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
```sh
Ý tưởng chung là track update dependencies để đảm bảo theo thứ tự và tính nhất quán của metadata
Thiết kế:
- 2 cách đảm bảo tính nhất quán của metadata
- Tóm tắt bản cập nhật
```

- Managing Update Conflicts in Bayou, a Weakly Connected Replicated Storage System [1995, 1112 refs],
https://people.cs.umass.edu/~mcorner/courses/691M/papers/terry.pdf
```sh
Bayou is highly referenced in oceanstore
```



<a name="3"></a>
##3. The University of UTAH - Ryan Stutsman - Distributed Systems
Bài giảng về các khái niệm quan trọng trong hệ thống phân phối và chuyên sâu về thiết kế hệ thống. 

- Chord: A Scalable Peer-to-peer Lookup Service for Internet Applications [2001, 12405 refs]
https://pdos.csail.mit.edu/papers/chord:sigcomm01/chord_sigcomm.pdf

##Nguồn tham khảo:
http://accelazh.github.io/storage/Storage-College-Course-Study
 
