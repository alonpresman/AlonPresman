# Agent Sudo | THM
### Difficulty: Easy
--------------------------------

First I'll start with nmap tool to scan the open ports.

```bash
nmap -sV -sC <ip-address>
```

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*8mKDBqh9QZXCrXh8v6HPWA.png)

as we can see there are 3 open ports: ftp, ssh and http. I’m going to use this information later…
now I need to redirect myself to a secret-page.

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*mI9VM47P_A2YvzFntdhu_g.png)

so, user-agent is my codename to access the secret-site.
Next- let’s take a ride on burp to intercept the request.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*-IczKWbtyarwtyjsLVwIHg.png)

I took another look on the message above and understand that maybe all the agents names are one letter(the message ends with “agent-R”). 
It’s the time to brute force the content of row number 3 “User-Agent” With a list
of capital letters to discover the name of the agent. Let’s bring it on!

![image](https://miro.medium.com/v2/resize:fit:786/format:webp/1*vRyxDkD86MlbAoahpN6BkQ.png)

There are 2 agents. The first is R and the second is C .(the length of the results are different from the other results.)
We asked for the full name of the agent so we need to keep going.
Just enter the letter ‘C’ in the request and we can forward it and see that response with the name of the agent:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*8VWXCDvnNCHiJepKyf65wg.png)

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*5TthVGuCYIzZRm_C59lg8w.png)

Yeah! it’s agent chris!
This is the end of the enumeration part.

We got the agent name and we can find the ftp password with hydra.

```bash
hydra -l chris -P /usr/share/wordlists/rockyou.txt ftp://<ip-address> -f  -V
```

Boboom! got it!

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*DGdmmd0_-YIyTPHU9zCjXg.png)

Now we can connect it with ftp.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*M9adiwCFlvnVyjUXpoIeuA.png)

The next step is to use “get” command to pull all the files from the ftp connection to my kali. 
There are 2 files (.png .jpg) and the third is .txt file. Let’s read it:

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*hofId-TIeAEQn1ev2TAy0Q.png)

I don’t know what does it mean yet but I’m going to check it. maybe Exif tool is going to help me?

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*kvmKGOsh1RdFmV6X7m8xvw.png)

I didn’t find any information that can help me to see a zip file. so I decided to use binwalk.

```bash
binwalk cutie.png
```

![image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*7ynXtzR9dBqZQYSxnVwGow.png)

Ok! I find a hidden zip file and now I can extract it with:

```bash
binwalk -e --run-as=root cutie.png 
```

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*I_OjqfcFENyF1NfkhdtnkA.png)

We can use john to find the zip password.

```bash
zip2john 8702.zip > ctfhash.txt
```

John will crack it:

```bash
john ctfhash.txt
```

John found it! the password for the zip file is alien! now, enter to the zip file.

![image](https://miro.medium.com/v2/resize:fit:786/format:webp/1*-QKOdD3PdtiytNm52KO-aA.png)

So we got that string ‘QXJlYTUx’ . let’s use cyberchef to decode that.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*4IEFvjZiGxn7ZzQPtxA1Mw.png)

Area51 is the steg password.

Steg password means that we need to use Steghide to extract files from the alien.png

```bash
steghide extract -sf cute-alien.jpg 
```

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*YppafmuZSLj4maO3IDwq4g.png)

so the extracted file is “message.txt”. cat it.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*yLF7am4tcyaXc5oPSP597Q.png)

That message shows us the full name of the agent: james! and the ssh password: “hackerrules!”
This is the end for that part.

We got the agent name james and the ssh password. It’s time to connect with ssh.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*UaZhzjGYvYK8CneUIdRDbQ.png)

and we are in!

![image](https://miro.medium.com/v2/resize:fit:786/format:webp/1*q8qTjcJORhyNawbfh9GbjA.png)

user flag found!
The next question : What is the incident of the photo called?
let’s google the name of the photo maybe we can find interesting information.
I found an article with the answer!!

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*ILavsolS2aoCFCLVEba32A.png)

firstly, I started with sudo -l to check james privileges.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*gY3ZFf2byIKdOGASAYyCFg.png)

(All, !root) /bin/bash … let’s google it to check the cves we can find.
I found an article with the cve ! cve-2019–14287

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*z2_MvAvWdTJqXGGTiAJK5A.png)

That vulnerability works with that command to get root:

```bash
sudo -u#-1 /bin/bash
```

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*0oJYYt-pjpLOjVzqnZXIZw.png)

We need to move to the root directory to read the root flag.

![image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*ggZBK7NOjV5Kgc19AGXGOg.png)

and we find the name of agent R! deskel.

Done!
Happy Hacking!

**Written by Alon Presman, Penetration Tester and Ethical Hacker.**






































































