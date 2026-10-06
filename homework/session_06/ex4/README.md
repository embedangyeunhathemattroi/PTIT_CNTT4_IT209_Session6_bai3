# Bài 4: Quản lý tiến trình nền với nohup và tín hiệu kill

## 1. Mục tiêu

- Khởi chạy tiến trình chạy nền độc lập với phiên Terminal bằng `nohup` và `&`.
- Giám sát tiến trình bằng `ps`, `pgrep`, `top` hoặc `htop`.
- Tìm PID của tiến trình đang chạy.
- Tắt tiến trình an toàn bằng tín hiệu `SIGTERM (15)`.
- Chỉ dùng `SIGKILL (9)` nếu tiến trình không dừng sau `SIGTERM`.

## 2. Tạo shell script

File script: `loop-monitor.sh`

```bash
#!/bin/bash

while true; do
    echo "System time: $(date)" >> /tmp/monitor.log
    sleep 5
done
```

Script này ghi thời gian hiện tại vào file `/tmp/monitor.log` sau mỗi 5 giây.

## 3. Gán quyền thực thi

Lệnh thực hiện:

```bash
chmod +x loop-monitor.sh
```

Kiểm tra quyền:

```bash
ls -l loop-monitor.sh
```

Kết quả:

```text
-rwxr-xr-x 1 student student 101 Oct 06 07:50 loop-monitor.sh
```

## 4. Khởi chạy tiến trình bằng nohup

Lệnh thực hiện:

```bash
nohup ./loop-monitor.sh > /dev/null 2>&1 &
```

Kết quả:

```text
[1] 2487
```

Trong đó `2487` là PID của tiến trình chạy nền.

## 5. Tìm PID của tiến trình

Lệnh kiểm tra:

```bash
pgrep -f loop-monitor.sh
```

Kết quả:

```text
2487
```

Có thể kiểm tra chi tiết bằng:

```bash
ps aux | grep loop-monitor.sh
```

Kết quả:

```text
student     2487  0.0  0.0   4348  3192 pts/0    S    07:50   0:00 /bin/bash ./loop-monitor.sh
student     2510  0.0  0.0   4088  2048 pts/0    S+   07:51   0:00 grep --color=auto loop-monitor.sh
```

## 6. Kiểm tra file log

Lệnh kiểm tra:

```bash
tail -n 10 /tmp/monitor.log
```

Kết quả:

```text
System time: Tue Oct  6 07:50:05 AM +07 2026
System time: Tue Oct  6 07:50:10 AM +07 2026
System time: Tue Oct  6 07:50:15 AM +07 2026
System time: Tue Oct  6 07:50:20 AM +07 2026
System time: Tue Oct  6 07:50:25 AM +07 2026
System time: Tue Oct  6 07:50:30 AM +07 2026
System time: Tue Oct  6 07:50:35 AM +07 2026
System time: Tue Oct  6 07:50:40 AM +07 2026
System time: Tue Oct  6 07:50:45 AM +07 2026
System time: Tue Oct  6 07:50:50 AM +07 2026
```

Kết quả cho thấy script đang ghi log đều đặn sau mỗi 5 giây.

## 7. Tắt tiến trình bằng SIGTERM

Lệnh thực hiện:

```bash
kill -15 2487
```

Hoặc dùng tên tín hiệu:

```bash
kill -SIGTERM 2487
```

Kiểm tra lại:

```bash
ps aux | grep loop-monitor.sh
```

Kết quả:

```text
student     2533  0.0  0.0   4088  2048 pts/0    S+   07:52   0:00 grep --color=auto loop-monitor.sh
```

Tiến trình `/bin/bash ./loop-monitor.sh` đã biến mất, chỉ còn tiến trình `grep`.

## 8. Trường hợp cần dùng SIGKILL

Nếu tiến trình không dừng sau `SIGTERM`, sử dụng:

```bash
kill -9 2487
```

Hoặc:

```bash
kill -SIGKILL 2487
```

`SIGKILL (9)` buộc tiến trình dừng ngay lập tức, nên chỉ dùng khi `SIGTERM (15)` không hiệu quả.

## 9. Kết luận

Đã hoàn thành các yêu cầu:

- Tạo script `loop-monitor.sh` ghi thời gian vào `/tmp/monitor.log` mỗi 5 giây.
- Gán quyền thực thi cho script.
- Chạy script nền độc lập bằng `nohup`.
- Tìm PID tiến trình bằng `pgrep -f loop-monitor.sh`.
- Kiểm tra log bằng `tail -n 10 /tmp/monitor.log`.
- Tắt tiến trình an toàn bằng `kill -15 <PID>`.
