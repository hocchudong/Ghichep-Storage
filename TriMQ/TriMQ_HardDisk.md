#Cấu tạo ổ đĩa cứng:
Ổ đĩa cứng bao gồm nhiều thành phần 
<img src="http://data.sinhvienit.net/lab1/hdd_files/hdd17.jpg">
<src img="">
###Cấu trúc dữ liệu của đĩa cứng:
<img src="http://data.sinhvienit.net/lab1/hdd_files/hdd16.jpg">
#####1. Track:
Trên một mặt làm việc của đĩa chia thanh nhiều vòng tròn đồng tâm tạo thành các track để xác định các vùng dữ liệu riêng biệt trên mặt đĩa.
#####2. Sector:
Mỗi track được chia thành những phần nhỏ bằng các đoạn hướng tâm tạo thành các sector, hay nói cách khác sector là thành phần của track. Sector là đơn vị chứa dữ liệu nhỏ nhất, sector dạo động từ 512 bytes tới 4096 bytes. Các ổ đĩa cứng được chia ra 10 vùng mà trong mỗi vùng có số sector/track bằng nhau.
#####3. Cluster:
Cluster là 1 đơn vị lưu trữ gồm một hoặc nhiều sector. Khi HĐH lưu trữ 1 tập tin vào đĩa, nó ghi tập tin đó vào hàng chục, có khi là hàng trăm cluster liền nhau. Nếu không có sẵn cluster liền nhau, HĐH sẽ tìm kiếm cluster còn trống ở kế đó và ghi tiếp tập tin lên đĩa, quá trình cứ tiếp tục cho đến khi toàn bộ tập tin được lưu hết.
#####4. Cylinder:
Tập hợp các track cùng bán kính ở các mặt đĩa khác nhau tạo thành các cylinder.Trên đĩa 2 mặt, một cylinder sẽ bao gồm track 1 của mặt trên và track 1 của mặt dưới. Trên đĩa cứng sắp xếp cái này chồng lên cái kia, một cylinder gồm các track trên cả 2 mặt của tất cả các đĩa. Trên 1 ổ đĩa cứng có nhiều cylinder bởi có nhiều track trên mỗi mặt đĩa từ.
###Cụm đĩa:
#####Đĩa từ(platter):
Thường được làm bằng nhôm hoặc thủy tinh, trên bề mặt được phủ 1 lớp vật liệu từ tính để chứa dữ liệu. Tùy vào hãng sản xuất mà số lượng đĩa có thể nhiều hơn một và các đĩa này được sử dụng 1 hoặc cả 2 mặt trên và dưới, các đĩa được gắn song song, quay đồng trục, cùng tốc đọ quay với nhau khi làm việc.
###Đầu đọc:
#####Đầu đọc(head):
Đầu đọc đơn giản được cấu tạo gồm lõi ferit và cuộn dây giống như nam châm điện. Đầu đọc trong đĩa cứng có công dụng đọc dữ liệu dưới dạng từ hóa trên bề mặt đĩa từ hoặc từ hóa lên các mặt đĩa khi ghi dữ liệu. Số đầu đọc ghi luôn bằng số mặt hoạt động của các đĩa cứng.


##Đọc và ghi dữ liệu trên bề mặt:
Sự hoạt động của đĩa cứng cần thực hiện đồng thời hai chuyển động: Chuyển động quay của các đĩa và chuyển động ra vô của các đầu đọc. Đĩa từ quay được nhờ gắn cùng trục với động cơ và có tốc độ rất lớn từ 3600 đến 15.000 vòng/phút. Mỗi loại ổ đĩa cứng có một tốc độ nhất định tùy theo công nghệ chế tạo.
Khi đĩa cứng quay đều, cần di chuyển đầu đọc sẽ di chuyển đến các vị trí trên bề mặt phủ vật liệu từ theo phương bán kính của đĩa. Chuyển động này kết hợp với chuyển động quay của đĩa có thể làm đầu đọc/ghi tới bất kỳ vị trí nào trên bề mặt đĩa.
Tại các vị trí cần đọc ghi, đầu đọc/ghi có các bộ cảm biến với điện trường để đọc hay ghi dữ liệu.
Dữ liệu được ghi/đọc đồng thời trên mọi đĩa. Việc thực hiện phân bổ dữ liệu trên các đĩa được thực hiện nhờ các mạch điều khiển trên bo mạch của ổ đĩa cứng.

##3 thông số cơ bản để đánh giá hiệu năng ổ cứng : throughput, latency và IOPS:

#####1. Throughput:
Chỉ ra tốc độ transfer data ( MB/s hoặc GB/s). Là thông số khi check performance của ổ cứng bằng câu lệnh "dd"
trong linux. Bao gồm cả internal rate (chuyển dữ liệu giữa disk surface và controller của driver) và extenal rate (chuyển dữ liệu giữa controller trên driver và hệ thống máy chủ) 


#####2. Latency(ms):
Thời gian ổ cứng bắt đầu thưc hiện 1 data transfer. Trong HDD vật lý truyền thống, latency bao gồm seek time
(thời gian đầu đọc tìm ra vị trí data ) và rotational latency (độ trễ chuyển động quay của trục). Thông số latency quyết
định hiệu năng của volume vì nó quyết định thời gian trễ khi bắt đầu thực hiện thao tác.

- <b>Seek time</b>: Với ổ đĩa quay, seek time là khoảng thời gian để head di chuyển giữa các track. Hay nói cách khác là khoảng thời gian mà head cần có để di chuyển đến các track của đĩa quay nơi chứa dữ liệu sẽ được đọc hoặc ghi.
- <b>Rotatinal latency</b>: Rotatinal latency hay còn gọi là latency là khoảng thời gian head được chuyển tới sector hay nói cách khác đó là khoảng thừoi gian 1 I/O được xử lí, latency sẽ tăng lên khi lượng I/O tăng lên. Rotatinal latency phụ thuộc vào tốc độ quay của ổ đĩa, tốc độ quay càng cao thì latency sẽ càng giảm
- <b>Typycal HDD figures</b>

| HDD Spindle (rpm) | Average rotational latency |
|-------------------|----------------------------|
| 4200 | 7,14 |
| 5400 | 5.56 |
| 7200 | 4,17 |
| 10000 | 3,00 |
| 15000 | 2,00 |


#####3. IOPS:
- Input/Output Operations Per Second - số thao tác đọc ghi trên ổ cứng trong 1s. Trong điện toán đám mây, nơi mà tài 
nguyên phần cứng được chia sẻ với nhiều người, IOPS quyết định độ nhanh và nhạy của volume do bản chất IOPS càng cao thì 
càng nhiều thao tác có thể thực hiện được đồng thời 1 lúc, tốc độ xử lý càng nhanh => tốc độ hoạt động ứng dụng càng cao
- Đối với ổ **HDD**, IOPS được tính bởi công thức:
```sh
IOPS = 1/(seek + latency)
```

##Random Access và Sequential Access:
<src img="">

#####1. Random Access:
- Là hành động truy xuất ngẫu nhiên bất kì trên ổ đĩa bất kể số lượng và kích thước bao nhiêu. Random Access thường được dùng để thể hiện IOPS của hệ thống lưu trữ.
- IO được gọi là sequential khi dữ liệu được đọc và ghi liên tiếp lên các bit gần nhau của các sector liên tiếp nhau trên đĩa tròn. Sequential IO rất phù hợp với HDD.
 

#####2. Sequential Access:
- Truy xuất dạng tuần tự là hành động truy cập vào một nhóm các vùng nhớ được xác định trước, theo 1 thứ tự sắp xếp trên ổ đĩa. Sequential Access thường được dùng để thể hiện throughput của hệ thống lưu trữ.
- IO được gọi là random khi khi dữ liệu được đọc, tìm kiếm và ghi ngẫu nhiên lên các ô lộn xộn trên các sector phân bố ngẫu nhiên trên đĩa tròn do đó Random IO rất chậm trên ổ đĩa HDD. 


##IOPS và Throughtput:

#####1. IOPS:
- Là viết tắt của Input-Output per second. Ở các thiết bị lưu trữ file thì băng thông (Mbps) là thông số quan trọng nhất.  Còn đối với các thiết bị lưu trữ cho đám mây CLOUD thì IOPS quyết định độ “nhạy” và độ “NHANH” của máy ảo.
- Đối với HDD, IOPS được tính bởi công thức:
```sh
IOPS = 1/(seek + Latency)
```
- So sánh IOPS của HDD và SSD:

#####2. Throughtput:
Là thông số dùng để chỉ lượng dữ liệu lớn nhất có thể chuyển tới hệ thống trong 1 giây 

##Bộ nhớ đệm (Disk cache):
Bộ nhớ đệm có nhiệm vụ lưu tạm dữ liệu trong quá trình làm việc của ổ đĩa cứng nên độ lớn của bộ nhớ đệm có ảnh hưởng đáng kể tới hiệu suất hoạt động của ổ đĩa cứng bởi việc đọc/ghi không xảy ra tức thời (do phụ thuộc vào sự di chuyển của đầu đọc/ghi, dữ liệu được truyền tới hoặc đi) sẽ được đặt tạm trong bộ nhớ đệm.
Các dữ liệu được truy xuất gần đây nhất từ đĩa cứng sẽ được lưu trữ trong một buffer (phần đệm) của bộ nhớ. Khi chương trình nào cần truy xuất dữ liệu từ ổ đĩa, nó sẽ kiểm tra trước tiên trong bộ nhớ đệm đĩa xem dữ liệu mình cần đang có sẵn không. Cơ chế này làm tăng đáng kể tốc độ hoạt động của hệ thống.

####Các loại bộ nhớ đệm:
- <b>Write-back</b>:  cập nhật những khối trong bộ nhớ chính khi những khối trong bộ nhớ đệm bị loại khỏi cache. Nhanh hơn write-throught do không tốn thời gian ghi ra bộ nhớ đệm. Không thuận lợi vì dữ liệu bộ đệm và bộ nhớ chính có thể không cùng giá trị khi một tiến trình bị hỏng trước khi ghi tới bộ nhớ chính thực thi xong, dữ liệu ở bộ đệm bị mất.
- <b>Write-throught</b>: cập nhật cả bộ nhớ đệm và bộ nhớ chính mỗi lần ghi. Ghi chậm hơn write-back nhưng đảm bảo bộ đệm phù hợp với bộ chính không thuận lợi là phải truy cập tới bộ nhớ chính








