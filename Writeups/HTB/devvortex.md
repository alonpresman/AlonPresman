# Devvortex | HTB
### Difficulty Level: Easy
--------------------------------------------

*add machine ip-address to /etc/hosts file with:*
```bash
echo '<machine-ip>    devvortex.htb' >> /etc/hosts
```

First, for the enumeration part, start with host's scan with nmap:

```bash
nmap -T4 -p- -A devvortex.htb -oN dev.nmap
```

-T4 ---> increases scan's speed.

-p- ---> scans all available ports instead the 1000 common ports.

-A ---> aggresive scan to gather more information.

-oN ---> saves the output to a file.









While browsing into the webpage, there is home page:

![image](https://cdn-images-1.medium.com/max/1000/1*RX5u537CK3zKK3K2g7UFRw.png)


After Investigation of the web application, I discovered nothing.
so let's use gobuster to find directories or files within the system:

```bash
gobuster dir -u devvortex.htb -w /usr/share/wordlist/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt

===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://devvortex.htb
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 178] [--> http://devvortex.htb/images/]                                          
/css                  (Status: 301) [Size: 178] [--> http://devvortex.htb/css/]                                             
/js                   (Status: 301) [Size: 178] [--> http://devvortex.htb/js/]                                              
Progress: 1680 / 220561 (0.76%)^C
[!] Keyboard interrupt detected, terminating.
Progress: 1680 / 220561 (0.76%)
===============================================================
Finished
```


















