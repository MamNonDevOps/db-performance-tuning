# MySQL

## query_cache_size

Vùng chứa dữ liệu cho truy vấn thường xuyên lặp lại, câu truy vấn đang chờ để chạy

Bắt đầu tối ưu từ giá trị **10 MB**, nếu truy vấn bị `waiting for cache lock` thì tăng dần giá trị

Đề xuất: đối với website lớn thì cần từ **256 MB** đến **512 MB**, còn những website nhỏ thì chỉ cần khoản **64-128 MB**

```
query_cache_type=1
query_cache_limit=256K
query_cache_min_res_unit=2k
query_cache_size = 64M
```

## max_connection

Số lượng kết nối tối đa tới database.

Nếu kết nối gặp lỗi `too many connections`, nghĩa là ta cần tăng giá trị cho tham số cài đặt này.

Tuy nhiên, nếu đặt giá trị quá cao, khi có nhiều kết nối tới sẽ khiến RAM load cao

Đề xuất: đối với websites lớn thì cần **200-600**, còn website nhỏ thì cần **80-200**

## innodb_buffer_pool_size

Vùng chứa data và indexes. Thực hiện tối ưu tham số này khi gặp tình trạng "nghẽn cổ chai"

Đề xuất: giá trị chiếm 60-70% dung lượng RAM

## thread_cache_size

Số lượng threads sẽ được cache

Các số liệu cần biết để tính toán
```
mysql -e "show status like 'Threads_created';"
mysql -e "show status like 'Connections';"
```
Giá trị tối ưu tính được theo công thức sau
```
100 – ((Threads_created / Connections) * 100))
```

## Enable MySQL slow query log

Log lại các query chậm để phân tích hoặc xử lý sự cố
```
slow-query-log=1
slow-query-log-file= /var/lib/mysql/mysql-slow-query.log
long-query-time=1
```

## Disable DNS reverse lookup for MySQL

Đề phòng trường hợp DNS có vấn đề và làm chậm kết nối, ta nên **disable** DNS lookup
```
skip-name-resolve
```

## Swappiness

Tiến trình sử dụng hết RAM, thay vì giải phóng bộ nhớ, nó sẽ lấy 1 phần dung lượng trên ổ cứng. Mà ổ cứng thì đọc/ghi chậm hơn RAM.

Đề xuất: **disable** tham số này

```
vm.swappiness = 0
```

## max_allowed_packet

Xác định kích thước tối đa của 1 gói tin có thể gửi đi. (default= 64MB, max= 1GB)

Nếu gặp lỗi `Got a packet bigger than ‘max_allowed_packet’ bytes`, hoặc database sử dụng BLOB (Binary Large OBject) columns or long string

Đề xuất: mỗi lần tăng *64MB*

## Terminate idle connections

Các connection nhàn rỗi cũng tiêu tốn tài nguyên bộ nhớ, cần được làm mới hoặc hủy bỏ

Kiểm tra các kết nối nhàn rỗi
```
mysqladmin processlist | grep "Sleep"
```
Cài đặt tham số `wait_timeout` và `interactive_timeout`

Giá trị mặc định là 28800s (8 giờ). Đặt giá trị quá thấp (30s, 60s, 90s) có thể gây ra lỗi `MySQL has gone away`

Đề xuất: 180s (3 phút), 300s (5 phút)
```
wait_timeout=180
interactive_timeout=180s
```

https://blog.vinahost.vn/mysql


https://vitux.com/tune-and-optimize-mysql-mariadb/


https://www.woktron.com/secure/knowledgebase/272/How-to-optimize-MySQL-performance.html

