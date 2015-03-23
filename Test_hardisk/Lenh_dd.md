###Sử dụng lệnh dd để kiểm tra tốc độ ghi của ổ cứng

**Cú pháp của lệnh như sau:**

`dd  if=<source file name> of=<target file name> [Options]` 

**Ví dụ sử dụng lệnh dd để kiểm tra tốc độ ghi của 1 ổ cứng:**

`# dd if=/dev/zero of=/tmp/output.tmp bs=4k count=256k conv=fdatasync`

Trong đó:

```
if= 	Dữ liệu đầu vào
of=		Dữ liệu đầu ra (ghi vào file output.img)
bs=		Kích thước 1 block
count= số block (ghi 256*1024 lần bs=4k vào file output.img)
conv=fdatasync: Kiểm tra tốc độ ghi khi đã đồng bộ dữ liệu vào ổ cứng (nếu không có option này, câu lệnh sẽ chỉ kiểm tra tốc độ ghi vào RAM)
```

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

