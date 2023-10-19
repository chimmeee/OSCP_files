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

EXTRA

Looking for hidden information in files in share folder /  trích xuất thông tin có trong files document:

EXIFTOOL:

```exiftool *``` #extract information in all files / tìm kiếm thông tin trong tất cả các file có trong folder

```exiftool -a -u filename.pdf```











