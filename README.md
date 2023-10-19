OSCP NOTE/Cheat Sheet/SCRIPTS

OSCP Note from my PEN200 labs and OSCP A-B-C practice test / OSCP note từ khóa học PEN200 và bài kiểm tra thử OSCP A-B-C

ENUMERATION METHOD:

Personal opinion: autorecon+nmap rarely missed an initial access in OSCP lab and exam, and if it missed, likely it is a hidden API endpoint and hidden directories /  autorecon kết hợp với nmap hiếm khi bỏ sót việc truy cập ban đầu trong môi trường OSCP lab và bài thi, và nếu nó bỏ sót, có khả năng đó là các API endpoint ẩn và các thư mục ẩn.

Network Initial Scan:

NMAP:

```nmap -p <ports> -sV -sC -A $IP```

```nmap -sS -p- --min-rate=1000 $IP -Pn``` #stealth scans

```nmap -p- --min-rate 1000 $IP -Pn```
#disables the ping command and only scans ports, sometimes ping scan is not reliable way to determine if the machine is online / Vô hiệu hóa lệnh ping và chỉ quét cổng, đôi khi quét ping không phải là cách đáng tin cậy để xác định xem máy đang hoạt động hay không.

``sudo nmap -F -sU -sV $IP`` #UDP Scan

AUTORECON:

```sudo autorecon $IP --nmap-append="--min-rate=2500" --exclude-tags="top-100-udp-ports" --dirbuster.threads=30 -vv```

```sudo autorecon $IP```

PORT ENUMERATION:

FTP (port 21):

```ftp -A $IP #Active mode```

```ftp $IP```

```anonymous:anonymous``` #anonymous login

```put test.txt``` #check if it is reflected in a http port

```put winpeas.exe``` # test binary upload

```hydra -l usernames.txt -P /usr/share/wordlists/rockyou.txt $IP -t 4 ftp``` #brute force FTP

```wget -r ftp://anonymous:anonymous@1IP/``` #download file recursively (anonymous logged) / tải tất cả các files trong folder share

```wget -r ftp://username:password@IP/``` 

```find / -name Settings.*  2>/dev/null``` #looking through the files / tìm tên file

extra:

Looking for hidden information in files in share folder /  trích xuất thông tin có trong files document:

EXIFTOOL:

```exiftool *``` #extract information in all files / tìm kiếm thông tin trong tất cả các file có trong folder

```exiftool -a -u filename.pdf```

SSH (port 22):

Private key obtained

```chmod 600 id_rsa```

```ssh user@IP -i id_rsa```

Public key obtained

```cat id_rsa.pub``` 

```ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC8J1/BFjH/Oet/zx+bKUUop1IuGd93QKio7Dt7Xl/J91c2EvGkYDKL5xGbfQRxsT9IePkVINONXQHmzARaNS5lE+SoAfFAnCPnRJ+KrnJdPxYf4OQEiAxHwRJHvbYaxEEuye7GKP6V0MdSvDtqKsFk0YRFVdPKuforL/8SYtSfqYUywUJ/ceiZL/2ffGGBJ/trQJ2bBL4QcOg05ZxrEoiTJ09+Sw3fKrnhNa5/NzYSib+0llLtlGbagBh3F9n10yqqLlpgTjDp5PKenncFiKl1llJlQGcGhLXxeoTI59brTjssp8J+z6A48h699CexyGe02GZfKLLLE+wKn/4luY0Ve8tnGllEdNFfGFVm7WyTmAO2vtXMmUbPaavDWE9cJ/WFXovDKtNCJxpyYVPy2f7aHYR37arLL6aEemZdqzDwl67Pu5y793FLd41qWHG6a4XD05RHAD0ivsJDkypI8gMtr3TOmxYVbPmq9ecPFmSXxVEK8oO3qu2pxa/e4izXBFc= john@oscp``` #new user found

Brute force:

```hydra -L users.txt -p passwords.txt $IP -t 4 ssh``` 

Cracking private passphrase - NOTE: the key could be in id_rsa format (enumerate the nmap ssh scan to determine the key format)
/etc/ssh/*pub #Use this to view the type of key you have aka (ecdsa) / khóa ssh có thể ở dạng id_rsa hoặc id_ecdsa, chúng ta dựa vào nmap scan để định dạng

```ssh2john id_ecdsa > id_ecdsa.hash``` #this instance the private key is ecdsa format 

```cat id_ecdsa.hash``` 

```john --wordlist=/usr/share/wordlists/rockyou.txt id_ecdsa.hash```

Finding Private keys

```cat /etc/ssh/*pub``` #Use this to view the type of key you have aka (ecdsa) / in file ra để tìm định dạng của ssh key

```ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBK6SiUV5zqxqNJ9a/p9l+VpxxqiXnYri40OjXMExS/tP0EbTAEpojn4uXKOgR3oEaMmQVmI9QLPTehCFLNJ3iJo= root@web01``` #example











