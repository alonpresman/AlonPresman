# Precious | HTB
### Difficulty: Easy
----------------------------

First let's use nmap to scan the system:

```bash
nmap -p- -T4 -A <machine-ip> -oN precious.nmap
```

-p- Scans all ports instead of just scanning common 1000 ports.

-T4 - Increase the speed of the scan.

-A - Aggressive scan.

-oN - Saves the outoput into a file.

Results:

```bash
Nmap scan report for precious.htb (10.10.11.189)                                                               
Host is up (0.12s latency).                                                                                    
Not shown: 65511 closed tcp ports (reset)                                                                      
PORT      STATE    SERVICE VERSION                                                                             
22/tcp    open     ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)                                       
| ssh-hostkey:                                                                                                 
|   3072 84:5e:13:a8:e3:1e:20:66:1d:23:55:50:f6:30:47:d2 (RSA)                                                 
|   256 a2:ef:7b:96:65:ce:41:61:c4:67:ee:4e:96:c7:c8:92 (ECDSA)
|_  256 33:05:3d:cd:7a:b7:98:45:82:39:e7:ae:3c:91:a6:58 (ED25519)
80/tcp    open     http    nginx 1.18.0
| http-server-header: 
|   nginx/1.18.0
|_  nginx/1.18.0 + Phusion Passenger(R) 6.0.15
|_http-title: Convert Web Page to PDF
120/tcp   filtered cfdptkt
4752/tcp  filtered snap
13206/tcp filtered unknown
17685/tcp filtered unknown
19600/tcp filtered unknown
21236/tcp filtered unknown
26206/tcp filtered unknown
31409/tcp filtered unknown
34118/tcp filtered unknown
34951/tcp filtered unknown
37111/tcp filtered unknown
37505/tcp filtered unknown
37679/tcp filtered unknown
42550/tcp filtered unknown
44178/tcp filtered unknown
47497/tcp filtered unknown
48499/tcp filtered unknown
48733/tcp filtered unknown
51570/tcp filtered unknown
54124/tcp filtered unknown
61063/tcp filtered unknown
63516/tcp filtered unknown
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=3/16%OT=22%CT=1%CU=33358%PV=Y%DS=2%DC=T%G=Y%TM=65F5
OS:BB72%P=x86_64-pc-linux-gnu)SEQ(SP=101%GCD=1%ISR=10C%TI=Z%CI=Z%II=I%TS=A)
OS:SEQ(SP=103%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=103%GCD=1%ISR=10C%TI
OS:=Z%CI=Z%II=I%TS=A)SEQ(SP=104%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=FA
OS:%GCD=1%ISR=10A%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M53CST11NW7%O2=M53CST11NW7%O3=
OS:M53CNNT11NW7%O4=M53CST11NW7%O5=M53CST11NW7%O6=M53CST11)WIN(W1=FE88%W2=FE
OS:88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M53CNNSNW7
OS:%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=
OS:Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%
OS:RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0
OS:%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIP
OS:CK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 3306/tcp)
HOP RTT      ADDRESS
1   92.22 ms 10.10.14.1
2   92.29 ms precious.htb (10.10.11.189)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 498.05 seconds
```



