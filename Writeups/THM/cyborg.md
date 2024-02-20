# Cyborg | THM
### Difficulty: Easy
---------------------------------
First, let’s use nmap tool to enumerate that machine address.
```bash
nmap -sC -sV <ip-address>
```
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*qf7Z5Cl-EiKwKAKMW4PUbw.png)

As we can there are 2 open ports: ssh and http so the answer is: 2.

The answer for what service is running on port 22? ssh for sure!
The answer for what service is running on port 80? http for sure!
Later we asked to find user.txt flag. So the first thing that I saw when I put the ip address in the url is a index page of apache server.
I can do nothing with that so lets gobuster to find directories in this host.

```bash
gobuster dir -u <ip-address> -w <path/to/wordlist>
```
This is the output:
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*A9R6Z8v3P9u-2UWvnJylnQ.png)

I’m going to enter admin directory and I got a site.
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*y3DIFGTkrI0LBD_394vAVw.png)

and now let’s check the etc directory.
![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*QeGI3049xLKv_f3BZoaj1A.png)

There is a file “passwd” in squid directory and when opened it I saw those credentials.
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*pCObv6cY_YERMc-k5gL9Tw.png)

Grab the hash:
```bash
echo '<hash>' > hash.txt
```

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*epxX629I4rE3-ugbOxPRvQ.png)

John found the password.
maybe ssh connection is the next step because we can use the credentials above.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*O0ABkS31Y4PGskJGxRqkng.png)

Keep going.. maybe I can find something interesting on the site. oh, this is the password for the archive for sure.
So there is a conversation that I found in admins category on that site.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*B9VMgZnFM9rOeWIHyThxSg.png)

The term “music archive” sounds interesting but I still can’t understand what is going on here.
after checking the site again, I found a file that we can download under the archive category. (archive.tar)
open it:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*YQ9NtWL6PUYbDPLppzT7dA.png)

actually, we just only can open ‘config’ and ‘README’ because the other files are encrypted or irrelevant.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*kXaag_YMi6D0-ecYdwCnLw.png)

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*UbG9OF2nZ5dY2LPLlJ_GaQ.png)

just search a little to understand that we need to use ‘borg repository’
to continue with that information so I need to download it:

```bash
sudo apt install borgbackup 
```
list the repository
```bash
borg list home/field/dev/final_archive
```
and we are going to enter to password of the archive:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*Py9dHW0kGXHqZQCM8z5iYA.png)

now we need to move the files of the archive to a new dir to see the content of that archive:

```bash
mkdir cyborg
```
after that , we can mount the archive to cyborg directory:

```bash
borg mount home/field/dev/final_archive cyborg
```

next step -move to the archive and find note.txt
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*ps6dIAkcIMaZNJlUZa2e6g.png)

whoop whoop! now it’s the time for ssh connection!

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*OltDqVXEDUvn52Az7vVwVQ.png)

and we got user.txt
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*rdTkOwojsycfa-CjVtwNgg.png)

*privilege escalation*

```bash
sudo -l
```
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*RLAIVA6l9GfGh4Q-s98s7A.png)

and now we just need to add “/bin/bash” to that file to get root.

```bash
echo "/bin/bash" > /etc/mp3backups/backup.sh
```

execute it:

![image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*n99Nk82AE_6fAXyAYeN2rA.png)

move to root directory and find the root.txt:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*gqlXjFF9gJUUEoXeqqZY4g.png)

and this is the end!

Happy Hacking!

**Written by Alon Presman, Penetration Tester and Ethical Hacker.**


























