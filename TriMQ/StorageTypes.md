##Block Storage:
Block Storage là 1 kiểu lưu trữ dữ liệu nơi mà các dữ liệu được lưu trữ trong các volumes. Mỗi block hoạt động như 1 ổ cứng riêng biệt và được cấu hình bởi admin.

####Ứng dụng của Block storage:
- Thường được sử dụng trong SAN (Storage area network)
- Block Storage làm việc rất tốt để lưu trữ 1 loạt các ứng dụng như các file system và database
- Openstack thường sử dụng block storage để cấp volumes cho các instance. Block Storage được gán vào các VMs dưới dạng các volumes. Trong Openstack, Cinder là project triển khai block storage. Các storage volumes này có thể gán vào cho 1 instance rồi gỡ bỏ và gán cho 1 instance khác mà dữ liệu vẫn được bảo toàn. Các block storage driver cho phép instance truy cập trực tiếp đến phần cứng storage của thiết bị thật, việc này giúp tăng hiệu suất IO.

##Object Storage:
Object storage (hay còn gọi là object-based storage) là 1 thuật ngữ chung để chỉ cách thức lưu trữ dữ liệu với các đơn vị lưu trữ gọi là object. Mỗi object chứa 3 yếu tố sau.
- <b>Data</b>: Dữ liệu cần lưu trữ
- <b>Metadata</b>: Metadata được tạo ra nhằm mục đích lưu trữ thông tin về data như là dữ liệu này được xử dụng vì mục đích gì, cách thức mà data được sử dụng
- <b>Identifier</b>: Identifier là 1 địa chỉ trao cho các object theo thứ tự mà các object được tìm thấy trên 1 hệ thống phân phối

####Ứng dụng của Object Storage:
- Object storage là 1 giải pháp giải quyết vấn đề về sự tăng lên của dữ liệu bởi vì càng nhiều dữ liệu được tạo ra thì hệ thống sẽ phải tăng lên theo đó trong trường hợp sử dụng block storage. Object storage có thể thu nhỏ và quan lý 1 cách đơn giản bằng cách thêm các nodes.
- Một lợi ích khác của object storage là dữ liệu được bảo vệ bằng cách lưu trữ nhiều bản sao của dữ liệu, nếu 1 trong các nodes hỏng thì dữ liệu vẫn có thể được bảo toàn.
- Object storage được sử dụng trong project Switf của Openstack, trong trường hợp muốn lưu trữ và quản lý 1 lượng lớn dữ liệu như như các images,Openstack sử dụng object storage để lưu trữ.
- Sử dụng trên Amazon S3 

####So sánh object storage và block storage:
- Với Block Storage, file sẽ được chia nhỏ thành các block như nhau của data, mỗi block đó có địa chỉ riêng nhưng không có metadata để cung cấp nhằm xác định được data
- Với Object Storage sẽ không chia các tập tin thành các khối dữ liệu mà thay vào đó, toàn bộ khối dữ liệu sẽ được lưu trữ. Một object chứa đựng data, metadata, unique identifier. Không có giới hạn của loại hay số lượng metadata, điều đó làm cho object trở nên rất hữu ích. Metadata chứa đựng rất nhiều thông tin về data như việc phân loại bảo mật của các tập tin trong object. Object storage được sử dụng cho cùng 1 loại lưu trữ nơi mà dữ liệu có tính sẵn sàng cao và có độ bền cao. 
Tuy nhiên Object storage không cung cấp khả năng chỉnh sửa từng phần của tập tin như là Block storage, khi chỉnh sửa trong object storage đòi hỏi phải truy cập, cập nhật và chỉnh sửa toàn bộ, điều đó làm giảm hiệu suất làm việc

##File Storage:
File Storage hay còn gọi là file-level hoặc file-based storage là 1 công nghệ lưu trữ thường được sử dụng trên ổ cứng, hệ thống lưu trữ NSA. Các dữ liệu được lưu trong các file và forder, dữ liệu có thể truy cập bằng cách sử dụng giao thức NFS cho Linux hoặc giao thức SMB/CIFS cho Window.

####So sánh File storage và Block storage:
- Block Storage ghi hoặc lưu dữ liệu từ các khối xác định, thường được sử dụng trong các hệ thống lưu trữ quy mô lớn. Với block storage, mỗi block có thể kiểm soát như 1 ổ cứng cá nhân. Block storage có thể sử dụng để lưu trữ hầu hết các ứng dụng bao gồm cả file storage, database...
- File Storage là mức lưu trữ dữ liệu đơn giản để sử dụng, thường được sử dụng để chia sẻ dữ liệu với người dùng  
