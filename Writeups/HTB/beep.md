# Beep | HTB
### Difficulty Level: Easy
----------------------------------

Nmap scan to find out services and open ports:

```bash
nmap -p- -T4 -A 10.10.10.7 -oN beep.nmap -Pn
```

-p-  scans all ports instead of 1000 common ports.

-T4 - increases the scan's speed

-A - aggressive scan to gather more information about the system.

-oN - saves the output to file.

-Pn - I got an error on the first scan that maybe the machine blocks ping so this flag fix it.

```bash
Nmap scan report for 10.10.10.7
Host is up (0.081s latency).
Not shown: 65519 closed tcp ports (reset)
PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 4.3 (protocol 2.0)
| ssh-hostkey: 
|   1024 ad:ee:5a:bb:69:37:fb:27:af:b8:30:72:a0:f9:6f:53 (DSA)
|_  2048 bc:c6:73:59:13:a1:8a:4b:55:07:50:f6:65:1d:6d:0d (RSA)
25/tcp    open  smtp       Postfix smtpd
|_smtp-commands: beep.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN
80/tcp    open  http       Apache httpd 2.2.3
|_http-title: Did not follow redirect to https://10.10.10.7/
|_http-server-header: Apache/2.2.3 (CentOS)
110/tcp   open  pop3       Cyrus pop3d 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_pop3-capabilities: RESP-CODES UIDL STLS LOGIN-DELAY(0) APOP USER TOP EXPIRE(NEVER) IMPLEMENTATION(Cyrus POP3 server v2) AUTH-RESP-CODE PIPELINING
111/tcp   open  rpcbind    2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100024  1            790/udp   status
|_  100024  1            793/tcp   status
143/tcp   open  imap       Cyrus imapd 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_imap-capabilities: SORT=MODSEQ Completed MAILBOX-REFERRALS RENAME RIGHTS=kxte CHILDREN IDLE OK URLAUTHA0001 X-NETSCAPE LIST-SUBSCRIBED LITERAL+ LISTEXT STARTTLS ATOMIC BINARY CONDSTORE ACL CATENATE ANNOTATEMORE QUOTA MULTIAPPEND SORT THREAD=ORDEREDSUBJECT UNSELECT THREAD=REFERENCES IMAP4rev1 ID UIDPLUS IMAP4 NAMESPACE NO
443/tcp   open  ssl/http   Apache httpd 2.2.3 ((CentOS))
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2017-04-07T08:22:08
|_Not valid after:  2018-04-07T08:22:08
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Elastix - Login page
|_ssl-date: 2024-03-19T15:27:50+00:00; +5s from scanner time.
|_http-server-header: Apache/2.2.3 (CentOS)
793/tcp   open  status     1 (RPC #100024)
993/tcp   open  ssl/imap   Cyrus imapd
|_imap-capabilities: CAPABILITY
995/tcp   open  pop3       Cyrus pop3d
3306/tcp  open  mysql      MySQL (unauthorized)
4190/tcp  open  sieve      Cyrus timsieved 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4 (included w/cyrus imap)
4445/tcp  open  upnotifyp?
4559/tcp  open  hylafax    HylaFAX 4.3.10
5038/tcp  open  asterisk   Asterisk Call Manager 1.1
10000/tcp open  http       MiniServ 1.570 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
|_http-server-header: MiniServ/1.570
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=3/19%OT=22%CT=1%CU=33859%PV=Y%DS=11%DC=T%G=Y%TM=65F
OS:9AFAC%P=x86_64-pc-linux-gnu)SEQ()SEQ(SP=BF%GCD=1%ISR=C3%TI=Z%CI=Z%II=I%T
OS:S=A)SEQ(SP=C1%GCD=1%ISR=C3%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=C1%GCD=1%ISR=C4%TI
OS:=Z%CI=Z%TS=A)SEQ(SP=C1%GCD=1%ISR=C4%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M53CST11N
OS:W7%O2=M53CST11NW7%O3=M53CNNT11NW7%O4=M53CST11NW7%O5=M53CST11NW7%O6=M53CS
OS:T11)WIN(W1=16A0%W2=16A0%W3=16A0%W4=16A0%W5=16A0%W6=16A0)ECN(R=N)ECN(R=Y%
OS:DF=Y%T=40%W=16D0%O=M53CNNSNW7%CC=N%Q=)T1(R=N)T1(R=Y%DF=Y%T=40%S=O%A=S+%F
OS:=AS%RD=0%Q=)T2(R=N)T3(R=N)T3(R=Y%DF=Y%T=40%W=16A0%S=O%A=S+%F=AS%O=M53CST
OS:11NW7%RD=0%Q=)T4(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=N)
OS:T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=N)T6(R=Y%DF=Y%T=40%W=
OS:0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=N)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD
OS:=0%Q=)U1(R=N)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%R
OS:UD=G)IE(R=N)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 11 hops
Service Info: Hosts:  beep.localdomain, 127.0.0.1, example.com, localhost; OS: Unix

Host script results:
|_clock-skew: 4s

TRACEROUTE (using port 8888/tcp)
HOP RTT       ADDRESS
1   ... 10
11  108.79 ms 10.10.10.7

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
```

There are 15 open port so let's make it more clear.

SSH - 22 (remote login securely).

SMTP - 25 (sending email messages between servers).

HTTP - 80  (transimitting web docs).

POP3 - 110 (retrieve emails from servers).

RPCBIND  - 111 (mediator between programs and port numbers).

IMAP - 143 (enables an email client to access and manage email messages stored on a mail server).

HTTPS - 443 (transmitting web docs securely).

PORT 793 random port.

SSL IMAP - 993 (secure imap).

POP3 - 995 (the same as 110).

MYSQL - 3306 (using db to manage data).

SIEVE - 4190 (used for sorting, filtering, and processing email messages)

PORT 4445 random port.

HYLAFAX - 4459 (fax server software).

ASTERIX - 5038 (Asterisk allows users to build communication applications)

HTTP - 10000 (same as 80)






