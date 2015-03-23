## RAID

### Mục Lục

[1. Giới thiệu chung] (#Gioithieu)

[2. Phân loại RAID] (#Phanloai)
- [2.1 Software RAID] (#Software)
- [2.2 Hardware RAID] (#Hardware)

[3. Các cấp độ RAID] (#Capdo)
- [3.1 RAID 0] (#RAID0)
- [3.2 RAID 1] (#RAID1)
- [3.3 RAID 3] (#RAID3)
- [3.4 RAID 5] (#RAID5)
- [3.5 RAID 6] (#RAID6)
- [3.6 RAID 0+1 và RAID 1+0 (RAID không tiêu chuẩn)] (#RAID0+1)

[4. Một số điểm cần lưu ý] (#Motsodiem)

<a name="Gioithieu"></a>
###1. Giới thiệu chung

RAID (Redundant Arrays of Inexpensive Disks) là hình thức ghép nhiều ổ đĩa cứng vật lý thành một hệ thống ổ đĩa cứng có chức năng gia tăng tốc độ đọc/ghi dữ liệu hoặc nhằm tăng thêm sự an toàn của dữ liệu chứa trên hệ thống đĩa hoặc kết hợp cả hai yếu tố trên.

<a name="Phanloai"></a>
###2. Phân loại RAID

<a name="Software"></a>
####2.1 Software RAID

Sau khi cài xong HĐH, bạn tiến hành dùng luôn Windows để thiết lập RAID (0, 1, 5, gì đó) tùy ý bạn muốn – Windows based RAID. Còn bạn nào sử dụng Linux thì có sẵn mdadm utility .

Các software RAID dựa trên phần mềm chủ yếu được sử dụng với các máy lưu trữ gia đình, các máy chủ entry-level . Điểm chủ yếu để nhận diện là nó thực hiện tất cả các lệnh I/O và các thuật toán toán RAID chuyên sâu trực tiếp trên các CPU của máy chủ lưu trữ. Chính điều này làm chậm hiệu suất hệ thống bằng cách tăng lưu lượng truy cập máy chủ qua PCI bus, sử dụng vào ngay luôn tài nguyên của hệ thống CPU, memory, .... Ưu điểm chính của software RAID là giá thành rẻ hơn (nhiều software RAID cho free luôn) so với các lựa chọn thay thế RAID khác như hardware RAID có mức giá cao hơn nhiều.

<a name="Hardware"></a>
####2.2 Hardware RAID

Nó thường ở dưới hình thức là một dạng card add-in. Loại card RAID controller này cắm vào một khe cắm bus chủ PCI. Giảm tải hệ thống máy chủ trong một số hoặc tất cả các lệnh I/O, dành các hoạt động tính toán RAID cho một hoặc nhiều bộ vi xử lý thứ cấp mà nó có.

Ngoài việc cung cấp những lợi ích chịu lỗi của một RAID thông thường, bộ điều khiển hardware RAID còn thực hiện các chức năng kết nối tương tự như bộ điều khiển trên máy chủ tiêu chuẩn. Và cũng bởi nhờ nó có riêng cho mình tài nguyên (CPU, memory,...), nên chúng thường cung cấp hiệu suất cao nhất cho tất cả các loại RAID. Hardware RAID cũng cung cấp tính năng chịu lỗi mạnh mẽ hơn đa dạng hơn software RAID. Ví dụ như RAID 0/1/5/6/10. 

<img src="http://i.imgur.com/cu2YLnm.png">

<a name="Capdo"></a>
###3. Các cấp độ RAID

Theo RAB thì RAID được chia thành 7 cấp độ (level), mỗi cấp độ có các tính năng riêng, hầu hết chúng được xây dựng từ hai cấp độ cơ bản là RAID 0 và RAID 1. Dưới đây, tôi sẽ giới thiệu những cấp độ RAID thường được sử dụng

<a name="RAID0"></a>
####3.1 RAID 0

<img src="http://i.imgur.com/5BvD8Qs.png">

RAID 0 cần ít nhất 2 ổ đĩa. Tổng quát ta có n đĩa (n >= 2) và các đĩa là cùng loại.

Dữ liệu sẽ được chia ra nhiều phần bằng nhau để lưu trên từng đĩa. Như vậy mỗi đĩa sẽ chứa 1/n dữ liệu. Tổng dung lượng = dung lượng đĩa nhỏ nhất nhân với tổng số đĩa. Array Capacity = Size of Smallest Drive * Number of Drives Dung lượng tổng cộng của ổ cứng trong hệ thống RAID0 bằng tổng dung lượng của hai ổ đĩa. Nếu chúng ta dùng 02 ổ cứng 80GB thì hệ thống đĩa của chúng ta là 160GB. Ưu điểm: - Tăng tốc độ đọc/ghi đĩa: mỗi đĩa chỉ cần phải đọc/ghi 1/n lượng dữ liệu được yêu cầu. Lý thuyết thì tốc độ sẽ tăng n lần.

Nhược điểm: - Tính an toàn thấp. Nếu một đĩa bị hư thì dữ liệu trên tất cả các đĩa còn lại sẽ không còn sử dụng được. Xác suất để mất dữ liệu sẽ tăng n lần so với dùng ổ đĩa đơn.

<a name="RAID1"></a>
####3.2 RAID 1

<img src="http://i.imgur.com/ZE42imP.png">

Đây là dạng RAID cơ bản nhất có khả năng đảm bảo an toàn dữ liệu. Cũng giống như RAID 0, RAID 1 đòi hỏi ít nhất hai đĩa cứng để làm việc. Dữ liệu được ghi vào 2 ổ giống hệt nhau (Mirroring). Trong trường hợp một ổ bị trục trặc, ổ còn lại sẽ tiếp tục hoạt động bình thường. Bạn có thể thay thế ổ đĩa bị hỏng mà không phải lo lắng đến vấn đề thông tin thất lạc. Đối với RAID 1, hiệu năng không phải là yếu tố hàng đầu nên chẳng có gì ngạc nhiên nếu nó không phải là lựa chọn số một cho những người say mê tốc độ. Tuy nhiên đối với những nhà quản trị mạng hoặc những ai phải quản lý nhiều thông tin quan trọng thì hệ thống RAID 1 là thứ không thể thiếu. Dung lượng cuối cùng của hệ thống RAID 1 bằng dung lượng của ổ đơn (hai ổ 80GB chạy RAID 1 sẽ cho hệ thống nhìn thấy duy nhất một ổ RAID 80GB).

<a name="RAID3"></a>
####3.3 RAID 3

<img src="http://i.imgur.com/D44MeGZ.png">

RAID 3 là sự cải tiến của RAID 0 nhưng có thêm (ít nhất) một ổ đĩa cứng chứa thông tin có thể khôi phục lại dữ liệu đã hư hỏng của các ổ đĩa cứng RAID 0. Giả sử dữ liệu A được phân tách thành 3 phần A1, A2, A3 (Xem hình minh hoạ RAID 3), khi đó dữ liệu được chia thành 3 phần chứa trên các ổ đĩa cứng 0, 1, 2 (giống như RAID 0). Phần ổ đĩa cứng thứ 3 chứa dữ liệu của tất cả để khôi phục dữ liệu có thể sẽ mất ở ổ đĩa cứng 0, 1, 2. Giả sử ổ đĩa cứng 1 hư hỏng, hệ thống vẫn hoạt động bình thường cho đến khi thay thế ổ đĩa cứng này. Sau khi gắn nóng ổ đĩa cứng mới, dữ liệu lại được khôi phục trở về ổ đĩa 1 như trước khi nó bị hư hỏng.

Yêu cầu tối thiểu của RAID 3 là có ít nhất 3 ổ đĩa cứng

<a name="RAID5"></a>
####3.4 RAID 5

<img src="http://i.imgur.com/p3qEKgM.png">

Đây có lẽ là dạng RAID mạnh mẽ nhất cho người dùng văn phòng và gia đình với 3 hoặc 5 đĩa cứng riêng biệt. Dữ liệu và bản sao lưu được chia lên tất cả các ổ cứng. Nguyên tắc này khá rối rắm. Ví dụ có 8 đoạn dữ liệu (1-8) và giờ đây là 3 ổ đĩa cứng. Đoạn dữ liệu số 1 và số 2 sẽ được ghi vào ổ đĩa 1 và 2 riêng rẽ, đoạn sao lưu của chúng được ghi vào ổ cứng 3. Đoạn số 3 và 4 được ghi vào ổ 1 và 3 với đoạn sao lưu tương ứng ghi vào ổ đĩa 2. Đoạn số 5, 6 ghi vào ổ đĩa 2 và 3, còn đoạn sao lưu được ghi vào ổ đĩa 1 và sau đó trình tự này lặp lại, đoạn số 7,8 được ghi vào ổ 1, 2 và đoạn sao lưu ghi vào ổ 3 như ban đầu. Như vậy RAID 5 vừa đảm bảo tốc độ có cải thiện, vừa giữ được tính an toàn cao. Dung lượng đĩa cứng cuối cùng bằng tổng dung lượng đĩa sử dụng trừ đi một ổ. Tức là nếu ta dùng 3 ổ 80GB thì dung lượng cuối cùng sẽ là 160GB.

4 ổ vào 1 RAID 5 là tối ưu cả về tốc độ lẫn xác suất hỏng. Chú ý là càng nhiều ổ trong 1 RAID thì tốc độ ghi càng chậm. Vài nhóm RAID có thêm 1 ổ hotspare nữa thì xác xuất mất dữ liệu do hỏng gần như không có
 
<a name="RAID6"></a>
####3.5 RAID 6
 
<img src="http://i.imgur.com/8XuBOMh.png">
 
RAID 6 phần nào giống như RAID 5 nhưng lại được sử dụng lặp lại nhiều hơn số lần sự phân tách dữ liệu để ghi vào các đĩa cứng khác nhau. Ví dụ như ở RAID 5 thì mỗi một dữ liệu được tách thành hai vị trí lưu trữ trên hai đĩa cứng khác nhau, nhưng ở RAID 6 thì mỗi dữ liệu lại được lưu trữ ở ít nhất ba vị trí (trở lên), điều này giúp cho sự an toàn của dữ liệu tăng lên so với RAID 5.
RAID 6 yêu cầu tối thiểu 4 ổ cứng. Trong RAID 6, ta thấy rằng khả năng chịu đựng rủi ro hư hỏng cứng được tăng lên rất nhiều. Nếu với 4 ổ cứng thì chúng cho phép hư hỏng đồng thời đến 2 ổ cứng mà hệ thống vẫn làm việc bình thường, điều này tạo ra một xác xuất an toàn rất lớn. Chính do đó mà RAID 6 thường chỉ được sử dụng trong các máy chủ chứa dữ liệu cực kỳ quan trọng.

Tổng dung lượng bằng tổng dung lượng của các ổ cứng trừ đi 2 ổ cứng.

<a name="RAID0+1"></a>
####3.6 RAID 0+1 và RAID 1+0 (RAID không tiêu chuẩn)

<img src="http://i.imgur.com/rUATgZq.png">

Tối thiểu 4 đĩa cứng để chạy RAID 0+1. Kết hợp giữa RAID 0 và RAID 1. 4 ổ đĩa này phải giống hệt nhau và khi đưa vào hệ thống RAID 0+1, dung lượng cuối cùng sẽ bằng ½ tổng dung lượng 4 ổ, ví dụ bạn chạy 4 ổ 80GB thì lượng dữ liệu “thấy được” là (4*80)/2 = 160GB.

Tương tự là RAID 1+0

<img src="http://i.imgur.com/n6BDcHk.png">

<a name="Motsodiem"></a>
###4. Một số điểm cần lưu ý

**Parity**

Yếu tố cơ bản để giúp cho ta có thể phục hồi dữ liệu trực tuyến (tức lúc máy chủ vẫn đang vận hành) trong trường hợp một đĩa cứng bị hư hỏng bằng cách sử dụng một hình thức dự phòng được gọi là parity. Parity là phần bổ sung của tất cả các ổ đĩa được sử dụng trong cái Raid đó. Khi ta muốn tạm đọc dữ liệu từ một HDD bị hư nào đó (khi ấy tốc độ truy xuất dữ liệu của toàn mảng Raid sẽ rất chậm, chứng chỉ được phục hồi tốc độ khi đã được thay bằng một ổ cứng mới), Raid sẽ thực hiện bằng cách đọc các dữ liệu tốt còn lại và kiểm tra nó bằng cách đối chiếu lại với dữ liệu pairty được lưu trữ trong mảng.

Ví dụ với RAID 5 sau:

<img src="http://i.imgur.com/1on4aKl.png">

Giả sử DISK 0 bị hỏng, đoạn dữ liệu A1 bị mất, từ 2 đoạn dữ liệu còn lại là A2 và A3, A Parity sẽ tính toán lại được đoạn dữ liệu bị mất là A1.
