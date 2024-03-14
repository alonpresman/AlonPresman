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


Now, Let's try to get more information about the behavior with burp:





















