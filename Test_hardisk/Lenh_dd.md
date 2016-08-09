###Sử dụng lệnh dd để kiểm tra tốc độ đọc/ghi của ổ cứng

**Cú pháp của lệnh như sau:**

`dd  if=<source file name> of=<target file name> [Options]` 

**Ví dụ sử dụng lệnh dd để kiểm tra tốc độ ghi của 1 ổ cứng:**

`# dd if=/dev/zero of=/tmp/output.tmp bs=4k count=256k iflag=fullblock conv=fdatasync`

Trong đó:

```
if= 	Dữ liệu đầu vào
of=		Dữ liệu đầu ra (ghi vào file output.img)
bs=		Kích thước 1 block
count= 256k : số lần ghi (ghi 256*1024 lần bs=4k vào file output.img)

iflag=fullblock: Đảm bảo 'count=' được hiểu là số block chứ không phải là số lần ghi 
conv=fdatasync: Kiểm tra tốc độ ghi khi đã đồng bộ dữ liệu vào ổ cứng (nếu không có option này, câu lệnh sẽ chỉ kiểm tra tốc độ ghi vào RAM)
conv=fsync: Đồng bộ data và metadata trước khi kết thúc câu lệnh.
```

#### Một số ví dụ kiểm tra tốc độ đọc, ghi trên ổ cứng hdd của laptop

**Ví dụ kiểm tra tốc độ ghi ổ cứng khi chưa đồng bộ dữ liệu vào ổ cứng:**

```
root@ubuntu:/# dd if=/dev/zero of=/writetest bs=8k count=131072
131072+0 records in
131072+0 records out
1073741824 bytes (1.1 GB) copied, 21.4056 s, 90.2 MB/s
```

**Ví dụ kiểm tra tốc độ ghi ổ cứng khi đồng bộ dữ liệu vào ổ cứng:**

```
root@ubuntu:/# dd if=/dev/zero of=/writetest bs=8k count=131072 conv=fdatasync
131072+0 records in
131072+0 records out
1073741824 bytes (1.1 GB) copied, 21.6239 s, 49.7 MB/s
```

**Kiểm tra tốc độ đọc của ổ cứng:**

```
root@ubuntu:/test#  dd if=/dev/zero  bs=8192 count=131072 | cat > writetest
131072+0 records in
131072+0 records out
1073741824 bytes (1.1 GB) copied, 16.6204 s, 64.6 MB/s
```

#### Một số ví dụ kiểm tra tốc độ đọc ghi của ổ cứng trên server có sử dụng SAN:

**Ví dụ kiểm tra tốc độ ghi ổ cứng khi chưa đồng bộ dữ liệu vào ổ cứng:**

```
root@Nam-ovs:~/Test# dd if=/dev/zero of=/writetest3 bs=8k count=131072 iflag=fullblock
131072+0 records in
131072+0 records out
1073741824 bytes (1.1 GB) copied, 3.00987 s, 357 MB/s
```

**Ví dụ kiểm tra tốc độ ghi ổ cứng khi đồng bộ dữ liệu vào ổ cứng:**

```
root@Nam-ovs:~/Test# dd if=/dev/zero of=/writetest2 bs=8k count=131072 iflag=fullblock conv=fdatasync
131072+0 records in
131072+0 records out
1073741824 bytes (1.1 GB) copied, 6.39174 s, 168 MB/s
```

**Ví dụ kiểm tra tốc độ ghi ổ cứng khi đồng bộ dữ liệu và metadata vào ổ cứng:**

```
root@Nam-ovs:~/Test# dd if=/dev/zero of=/writetest1 bs=8k count=131072 iflag=fullblock conv=fsync
131072+0 records in
131072+0 records out
1073741824 bytes (1.1 GB) copied, 7.62919 s, 141 MB/s
```