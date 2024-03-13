# Plotted-tms | THM
### Difficulty Level: Easy 
-------------------------------------
First, scan the machine with nmap:

```
nmap -p- -T4 -A <machine-ip> -oN plotted.nmap
```

-p- Scan all port instead of the 1000 common ports.

-T4 - Will increase the tempo of the scan.

-A - Aggresive scan

-oN - Save the results into a file.

Results:

```
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-13 06:49 EDT
Stats: 0:08:01 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 99.99% done; ETC: 06:57 (0:00:00 remaining)
Nmap scan report for 10.10.26.6
Host is up (0.10s latency).
Not shown: 65532 closed tcp ports (reset)
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 a3:6a:9c:b1:12:60:b2:72:13:09:84:cc:38:73:44:4f (RSA)
|   256 b9:3f:84:00:f4:d1:fd:c8:e7:8d:98:03:38:74:a1:4d (ECDSA)
|_  256 d0:86:51:60:69:46:b2:e1:39:43:90:97:a6:af:96:93 (ED25519)
80/tcp  open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
445/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.41 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=3/13%OT=22%CT=1%CU=30354%PV=Y%DS=2%DC=T%G=Y%TM=65F1
OS:86D5%P=x86_64-pc-linux-gnu)SEQ(SP=100%GCD=1%ISR=10A%TI=Z%CI=Z%II=I%TS=A)
OS:SEQ(SP=FD%GCD=1%ISR=10A%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=FE%GCD=1%ISR=109%TI=Z
OS:%CI=Z%II=I%TS=A)SEQ(SP=FE%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=FF%GC
OS:D=1%ISR=10A%TI=Z%CI=Z%TS=A)OPS(O1=M508ST11NW7%O2=M508ST11NW7%O3=M508NNT1
OS:1NW7%O4=M508ST11NW7%O5=M508ST11NW7%O6=M508ST11)WIN(W1=F4B3%W2=F4B3%W3=F4
OS:B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M508NNSNW7%CC=Y%Q=
OS:)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W
OS:=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
OS:T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S
OS:+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUC
OS:K=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_smb2-time: Protocol negotiation failed (SMB2)

TRACEROUTE (using port 1723/tcp)
HOP RTT       ADDRESS
1   109.01 ms 10.9.0.1
2   110.20 ms 10.10.26.6

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 557.82 seconds
```

There are 3 open ports: 

22 - SSH

80 - HTTP

445 - HTTP

As known, port 445 generally runs SMB on it, so let's check if there any vulnerability that comes out from this configuration.
So, the nmap scan tells us that the pages that runs on 80 and 445 are apache default pages.

It's the time to use gobuster to scan for any directory that runs on the system.

```
gobuster dir -u http://<machine-ip> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

dir - Tells gobuster to commit the directories scan.

-u - append to it the host-address.

-w - append to it the wordlist that you want use.

 
 There are 3 directories:

 /admin with 301 status code.

 /shadow with status code of 200.

 /passwd also with 200 status code.

While browsing into /shadow , there is a text that got the base64 encoded view.


![image](https://cdn-images-1.medium.com/max/1000/1*TAkhiYbEpHLsz62vGdMb3g.png)


Rabbit hole.

Let's browse into /passwd .

There is the same encoded text so it's another rabbit hole.

Let's try to browse into /admin.

![image](https://cdn-images-1.medium.com/max/1000/1*SdAqx2Zg6HSm-PBsN2JP5g.png)

Is it really private key?

```
VHJ1c3QgbWUgaXQgaXMgbm90IHRoaXMgZWFzeS4ubm93IGdldCBiYWNrIHRvIGVudW1lcmF0aW9uIDpE
```

It doesn't look like a key.

As I thought:

![image](https://cdn-images-1.medium.com/max/1000/1*R3RfIwGI9GG5mvuOSbt47w.png)


So I back to gobuster scan, but with a directories that runs on port 445.


![image](https://cdn-images-1.medium.com/max/1000/1*wmBj8JdE3OIcnfgKuCeelQ.png)


There is /management directory.


It looks like a legitimate web page.


![image](https://cdn-images-1.medium.com/max/1000/1*PPRAMElPq2tUZicTXvrXSw.png)


 After a quick testing I found login page:

 
 ![image](https://cdn-images-1.medium.com/max/1000/1*AYE1p8mrobfaNKZrlyvIQQ.png)



 


















