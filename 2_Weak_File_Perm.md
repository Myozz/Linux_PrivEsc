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
