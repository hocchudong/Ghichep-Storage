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
#####Seek:
Là khoảng thời gian để di chuyển head giữa các track
#####Latency:
<img src="http://louwrentius.com/static/images/io03.png">
- Khoảng thời gian di chuyển head tới secter hay nói cách khác đó là khoảng thời gian 1 I/O được xử lí
- Latency tăng lên khi lượng I/O tăng lên.
<img src="http://louwrentius.com/static/images/io055.png">

##Đọc và ghi dữ liệu trên bề mặt:
Sự hoạt động của đĩa cứng cần thực hiện đồng thời hai chuyển động: Chuyển động quay của các đĩa và chuyển động ra vô của các đầu đọc. Đĩa từ quay được nhờ gắn cùng trục với động cơ và có tốc độ rất lớn từ 3600 đến 15.000 vòng/phút. Mỗi loại ổ đĩa cứng có một tốc độ nhất định tùy theo công nghệ chế tạo.
Khi đĩa cứng quay đều, cần di chuyển đầu đọc sẽ di chuyển đến các vị trí trên bề mặt phủ vật liệu từ theo phương bán kính của đĩa. Chuyển động này kết hợp với chuyển động quay của đĩa có thể làm đầu đọc/ghi tới bất kỳ vị trí nào trên bề mặt đĩa.
Tại các vị trí cần đọc ghi, đầu đọc/ghi có các bộ cảm biến với điện trường để đọc hay ghi dữ liệu.
Dữ liệu được ghi/đọc đồng thời trên mọi đĩa. Việc thực hiện phân bổ dữ liệu trên các đĩa được thực hiện nhờ các mạch điều khiển trên bo mạch của ổ đĩa cứng.
##Random Access và Sequential Access:
<src img="">

#####1. Random Access:
- Là hành động truy xuất ngẫu nhiên bất kì trên ổ đĩa bất kể số lượng và kích thước bao nhiêu. Random Access thường được dùng để thể hiện IOPS của hệ thống lưu trữ
- Random IO sẽ có dạng: đọc-tìm kiếm-viết-tìm kiếm...

#####2. Sequential Access:
- Truy xuất dạng tuần tự là hành động truy cập vào một nhóm các vùng nhớ được xác định trước, theo 1 thứ tự sắp xếp trên ổ đĩa. Sequential Access thường được dùng để thể hiện throughput của hệ thống lưu trữ.
- Sequential có dạng: đọc-đọc-đọc hoặc viết-viết-viết...

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

##Bộ nhớ đệm (cache hoặc buffer):
Bộ nhớ đệm có nhiệm vụ lưu tạm dữ liệu trong quá trình làm việc của ổ đĩa cứng nên độ lớn của bộ nhớ đệm có ảnh hưởng đáng kể tới hiệu suất hoạt động của ổ đĩa cứng bởi việc đọc/ghi không xảy ra tức thời (do phụ thuộc vào sự di chuyển của đầu đọc/ghi, dữ liệu được truyền tới hoặc đi) sẽ được đặt tạm trong bộ nhớ đệm





