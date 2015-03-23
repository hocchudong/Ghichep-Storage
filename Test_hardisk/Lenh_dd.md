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

