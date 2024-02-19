# Hack the box: Cap writeup
### Difficulty level: Easy
--------------------------------------
 First of all, let’s add the target machine IP address to /etc/hosts file.
  
  ///
  echo '10.10.10.245 cap.htb' >> /etc/hosts
  ///

  The next step will be nmap scan. Let's write the results inato a nmap.txt file.

  nmap -p- -sC -sV <MACHINE-IP> > nmap.txt

  nmap resuls:

That machine has 3 open ports:

21- ftp
22- ssh
80- http
    
Let’s start with port 80:

![image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*A1rQg_1l1XH00LtcE4TTqg.png)

There is security dashboard with pcap file download that includes non-encrypted network traffic.
    
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*zVIb0odsnclZoH3TnNjb_w.png)

  In the URL there is direct object on data directory.
http://cap.htb/data/1

After I replaced the number with another number, I got another data information about the
packets which there is IDOR. Those numbers are different pcap files, so after checking how
many files there are, the results were 4 files. With changing the value to 0, 1, 2, we got those
3 different files. When changing the value to number 3 and above that, the app leads us back
to the home dashboard. Let’s inspect all those 3 files with WIRESHARK.

    /// Educational anecdote ///
Generally, this is an IDOR vulnerability cause we got access to security dashboard of one of
the regular user that his name is Nathan. This account actually gets an access to network
traffic of other users (every file gives information about different IP’S).

Let’s inspect file “0.pcap”

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*qla9pygxgh_pOGgv65oGgw.png)

There is FTP server connection of the user nathan with those plain text credentials cause FTP is not encrypted.

I tried to make the connection with the credentials.
![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*OiW5QOvBAPCwisQ5KcBxaA.png)

user.txt is in our hands.
The next step is trying to connect with SSH and escalate our privileges.
Those credentials are used also to connect with SSH.

After the connection, I uploaded linpeas.sh to the machine getting a way to do my privesc.

![image](https://miro.medium.com/v2/resize:fit:828/format:webp/1*WBlXh2v6cad-NqhUBqcNTw.png)

Linpeas found a way to to that, with the binary of python.
Time for gtfobins:

https://gtfobins.github.io

'''bash
python3.8 -c 'import os; os.setuid(0); os.system("/bin/sh")'
'''

Whoami?

![image](https://miro.medium.com/v2/resize:fit:720/format:webp/1*R3g22nG12bJaXXf_dArEIw.png)

I’m happy to write those walkthroughs to help beginners to do their first step in cybersecurity field and being better ethical hackers.





    

    
    

  

  
  
  
