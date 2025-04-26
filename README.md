# cat.htb
https://app.hackthebox.com/machines/Cat

```
sudo nmap -sC -sV -p- -T5 --max-rate 10000 10.10.11.53

Hexada@hexada ~/Downloads$ sudo nmap -sC -sV -p- -T5 --max-rate 10000 10.10.11.53                                                                                                     1 ↵  
[sudo] password for Hexada: 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-04-26 10:54 EEST
Warning: 10.10.11.53 giving up on port because retransmission cap hit (2).
Nmap scan report for cat.htb (10.10.11.53)
Host is up (0.20s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 96:2d:f5:c6:f6:9f:59:60:e5:65:85:ab:49:e4:76:14 (RSA)
|   256 9e:c4:a4:40:e9:da:cc:62:d1:d6:5a:2f:9e:7b:d4:aa (ECDSA)
|_  256 6e:22:2a:6a:6d:eb:de:19:b7:16:97:c2:7e:89:29:d5 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Best Cat Competition
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-git: 
|   10.10.11.53:80/.git/
|     Git repository found!
|     Repository description: Unnamed repository; edit this file 'description' to name the...
|_    Last commit message: Cat v1 
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 349.36 seconds
```

```
Hexada@hexada ~/Downloads$ gobuster dir -u http://cat.htb -w ~/app/pentesting-wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt                                       
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://cat.htb
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/Hexada/app/pentesting-wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/img                  (Status: 301) [Size: 300] [--> http://cat.htb/img/]
/uploads              (Status: 301) [Size: 304] [--> http://cat.htb/uploads/]
/css                  (Status: 301) [Size: 300] [--> http://cat.htb/css/]
/winners              (Status: 301) [Size: 304] [--> http://cat.htb/winners/]
/server-status        (Status: 403) [Size: 272]
Progress: 220559 / 220560 (100.00%)
===============================================================
Finished
===============================================================
```

```
POST /contest.php HTTP/1.1
Host: cat.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:137.0) Gecko/20100101 Firefox/137.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=----geckoformboundarybd5374079d18108187245d6c7c59c63e
Content-Length: 697
Origin: http://cat.htb
Connection: keep-alive
Referer: http://cat.htb/contest.php
Cookie: PHPSESSID=9qr30345sfdv0qmc304igt0dke
Upgrade-Insecure-Requests: 1
Sec-GPC: 1
Priority: u=0, i

------geckoformboundarybd5374079d18108187245d6c7c59c63e
Content-Disposition: form-data; name="cat_name"

Cotino
------geckoformboundarybd5374079d18108187245d6c7c59c63e
Content-Disposition: form-data; name="age"

1717
------geckoformboundarybd5374079d18108187245d6c7c59c63e
Content-Disposition: form-data; name="birthdate"

2025-04-09
------geckoformboundarybd5374079d18108187245d6c7c59c63e
Content-Disposition: form-data; name="weight"

0.04
------geckoformboundarybd5374079d18108187245d6c7c59c63e
Content-Disposition: form-data; name="cat_photo"; filename="shell"
Content-Type: application/octet-stream

hello world
------geckoformboundarybd5374079d18108187245d6c7c59c63e--
```

```
Hexada@hexada ~/Downloads$ gobuster dir -u http://cat.htb/.git -w ~/app/pentesting-wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt                                  
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://cat.htb/.git
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/Hexada/app/pentesting-wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index                (Status: 200) [Size: 1726]
/info                 (Status: 301) [Size: 306] [--> http://cat.htb/.git/info/]
/config               (Status: 200) [Size: 92]
/logs                 (Status: 301) [Size: 306] [--> http://cat.htb/.git/logs/]
/objects              (Status: 301) [Size: 309] [--> http://cat.htb/.git/objects/]
/description          (Status: 200) [Size: 73]
/branches             (Status: 301) [Size: 310] [--> http://cat.htb/.git/branches/]
/refs                 (Status: 301) [Size: 306] [--> http://cat.htb/.git/refs/]
/HEAD                 (Status: 200) [Size: 23]
Progress: 220559 / 220560 (100.00%)
===============================================================
Finished
===============================================================
```

```
Hexada@hexada ~/Downloads$ curl http://cat.htb/.git//HEAD                                                                                                                                  
ref: refs/heads/master
Hexada@hexada ~/Downloads$ curl http://cat.htb/.git/refs/heads/master                                                                                                                      
8c2c2701eb4e3c9a42162cfb7b681b6166287fd5
```

`.git/objects/xx/yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy`

```
Hexada@hexada ~/app/vrm/cat.htb$ curl http://cat.htb/.git/objects/8c/2c2701eb4e3c9a42162cfb7b681b6166287fd5 --output 8c2c27_object                                                    8 ↵  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   121  100   121    0     0    810      0 --:--:-- --:--:-- --:--:--   812
Hexada@hexada ~/app/vrm/cat.htb$ ls                                             
```

```
Hexada@hexada ~/app/vrm/cat.htb$ cat 8c2c27_object | zlib-flate -uncompress                                                                                                                
commit 157tree c9e281ffb3f5431800332021326ba5e97aeb2764
author Axel <axel2017@gmail.com> 1725146774 +0000
committer Axel <axel2017@gmail.com> 1725146774 +0000

Cat v1
```




                                                                                                
