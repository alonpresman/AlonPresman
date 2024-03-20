# Shocker | HTB 
### Difficulty Level: Easy
----------------------------------------------------

First, start with nmap scan:

```bash
nmap -p- -T4 -A 10.10.10.56 -oN shocker.nmap
```

Results:

```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-20 10:00 EDT
Nmap scan report for 10.10.10.56
Host is up (0.086s latency).
Not shown: 65493 closed tcp ports (reset), 40 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.18 (Ubuntu)
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c4:f8:ad:e8:f8:04:77:de:cf:15:0d:63:0a:18:7e:49 (RSA)
|   256 22:8f:b1:97:bf:0f:17:08:fc:7e:2c:8f:e9:77:3a:48 (ECDSA)
|_  256 e6:ac:27:a3:b5:a9:f1:12:3c:34:a5:5d:5b:eb:3d:e9 (ED25519)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=3/20%OT=80%CT=1%CU=34868%PV=Y%DS=2%DC=T%G=Y%TM=65FA
OS:ED2A%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=108%TI=Z%II=I%TS=8)SEQ(S
OS:P=105%GCD=1%ISR=108%TI=Z%CI=I%II=I%TS=8)SEQ(SP=106%GCD=1%ISR=108%TI=Z%CI
OS:=I%II=I%TS=8)OPS(O1=M53CST11NW6%O2=M53CST11NW6%O3=M53CNNT11NW6%O4=M53CST
OS:11NW6%O5=M53CST11NW6%O6=M53CST11)WIN(W1=7120%W2=7120%W3=7120%W4=7120%W5=
OS:7120%W6=7120)ECN(R=Y%DF=Y%T=40%W=7210%O=M53CNNSNW6%CC=Y%Q=)T1(R=Y%DF=Y%T
OS:=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R
OS:%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=
OS:40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0
OS:%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R
OS:=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 199/tcp)
HOP RTT      ADDRESS
1   83.96 ms 10.10.14.1
2   84.21 ms 10.10.10.56

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 284.35 seconds
```

There are 2 open ports:

80 - HTTP

2222 - SSH

Browsw into the webpage. I didn't get any access so let's add the ip address to /etc/hosts.

```bash
echo '10.10.10.56    shocker.htb' >> /etc/hosts
```

 And there is a webpage:

 ![image](https://cdn-images-1.medium.com/max/1000/1*wDiDo-7di39upvReK-Btzg.png)


Start with directories enumeration with gobuster :

```bash
└─# gobuster dir -u http://10.10.10.56 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```








 





