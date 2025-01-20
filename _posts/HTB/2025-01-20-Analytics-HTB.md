---
date: 2023-10-15
linktitle: Analytics-HTB
title: HTB Analytics - 🐧
showreadingtime: true
tags: ['HTB']
categories: ['HTB']
---
#### Analytics is a new easy linux machine released for Open Beta Season III

## Enumeration: 
### Port Scan:

```
sudo nmap -sC -sV -Pn -A 10.10.11.233
[sudo] password for d31ty: 
Starting Nmap 7.94 ( https://nmap.org ) at 2023-10-13 12:58 IST
Nmap scan report for 10.10.11.233
Host is up (0.38s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
|_  256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://analytical.htb/
|_http-server-header: nginx/1.18.0 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94%E=4%D=10/13%OT=22%CT=1%CU=43150%PV=Y%DS=2%DC=T%G=Y%TM=6528F1
OS:C4%P=aarch64-unknown-linux-gnu)SEQ(SP=103%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%T
OS:S=A)SEQ(SP=103%GCD=2%ISR=10B%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M53AST11NW7%O2=M
OS:53AST11NW7%O3=M53ANNT11NW7%O4=M53AST11NW7%O5=M53AST11NW7%O6=M53AST11)WIN
OS:(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN(R=Y%DF=Y%T=40%W=FAF
OS:0%O=M53ANNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(
OS:R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z
OS:%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y
OS:%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RI
OS:PL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 995/tcp)
HOP RTT       ADDRESS
1   339.70 ms 10.10.16.1
2   176.56 ms 10.10.11.233

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 45.61 seconds    
```

Directory Enumeration:
```
gobuster dir -u "http://analytical.htb/" -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://analytical.htb/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 178] [--> http://analytical.htb/images/]
/css                  (Status: 301) [Size: 178] [--> http://analytical.htb/css/]
/js                   (Status: 301) [Size: 178] [--> http://analytical.htb/js/]

```

### Sud-Domain Scan:
```
ffuf -c -mc 200 -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -u http://analytical.htb/ -H 'Host: FUZZ.analytical.htb'

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.0.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://analytical.htb/
 :: Wordlist         : FUZZ: /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ.analytical.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200
________________________________________________

[Status: 200, Size: 77883, Words: 3574, Lines: 28, Duration: 204ms]
    * FUZZ: data

:: Progress: [4989/4989] :: Job [1/1] :: 234 req/sec :: Duration: [0:00:22] :: Errors: 0 ::
```

Got the login page at: http://data.analytical.htb/auth/login
### Page source Seems like this: 
```
<!doctype html><html lang="en" translate="no"><head><meta charset="utf-8"/><meta http-equiv="X-UA-Compatible" content="IE=edge"/>

</script><script>(function() {
  window.MetabaseBootstrap        = JSON.parse(document.getElementById("_metabaseBootstrap").textContent);
  window.MetabaseUserLocalization = JSON.parse(document.getElementById("_metabaseUserLocalization").textContent);
  window.MetabaseSiteLocalization = JSON.parse(document.getElementById("_metabaseSiteLocalization").textContent);

  var configuredRoot = document.head.querySelector("meta[name='base-href']").content;
  var actualRoot = "/";

  // Add trailing slashes
  var backendPathname = document.head.querySelector("meta[name='uri']").content.replace(/\/*$/, "/");
  // e.x. "/questions/"
  var frontendPathname = window.location.pathname.replace(/\/*$/, "/");
  // e.x. "/metabase/questions/"
  if (backendPathname === frontendPathname.slice(-backendPathname.length)) {
    // Remove the backend pathname from the end of the frontend pathname
    actualRoot = frontendPathname.slice(0, -backendPathname.length) + "/";
    // e.x. "/metabase/"
  }

  if (actualRoot !== configuredRoot) {
    console.warn("Warning: the Metabase site URL basename \"" + configuredRoot + "\" does not match the actual basename \"" + actualRoot + "\".");
    console.warn("You probably want to update the Site URL setting to \"" + window.location.origin + actualRoot + "\"");
    document.getElementsByTagName("base")[0].href = actualRoot;
  }

  window.MetabaseRoot = actualRoot;
})();
```

With some google search guessed the vulnerability with `CVE-2023-38646 (2023-07-21)`

Notes about the vulnerability: https://infosecwriteups.com/cve-2023-38646-metabase-pre-auth-rce-866220684396

The key component for the vulnerability lies in `/api/setup/validate`

With couple of POC from - https://github.com/nomi-sec/PoC-in-GitHub

I tried: https://github.com/securezeron/CVE-2023-38646

**Note:** Sometimes in the Reverse Shell POC the bash command won't work, so try to use sh command in the POC to get the reverse shell

We will access to the docker container. Initially I thought I got user, then after few minutes I realised like falling into a rabbit-hole, then a sudden thought of checking the `env`  made me come back to my senses.

I found the username and password in the env. 
```
/usr $ env
MB_LDAP_BIND_DN=
LANGUAGE=en_US:en
USER=metabase
HOSTNAME=24c2782950ea
FC_LANG=en-US
SHLVL=4
LD_LIBRARY_PATH=/opt/java/openjdk/lib/server:/opt/java/openjdk/lib:/opt/java/openjdk/../lib
HOME=/home/metabase
OLDPWD=/
MB_EMAIL_SMTP_PASSWORD=
LC_CTYPE=en_US.UTF-8
JAVA_VERSION=jdk-11.0.19+7
LOGNAME=metabase
_=-l
MB_DB_CONNECTION_URI=
PATH=/opt/java/openjdk/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
MB_DB_PASS=
MB_JETTY_HOST=0.0.0.0
META_PASS=An4lytics_ds20223#
LANG=en_US.UTF-8
MB_LDAP_PASSWORD=
SHELL=/bin/sh
MB_EMAIL_SMTP_USERNAME=
MB_DB_USER=
META_USER=metalytics
LC_ALL=en_US.UTF-8
JAVA_HOME=/opt/java/openjdk
PWD=/usr
MB_DB_FILE=//metabase.db/metabase.db
```

`META_USER=metalytics and META_PASS=An4lytics_ds20223#`

Went to **ssh** with username metalytics and password An4lytics_ds20223#

Got the user flag: `ead761838a2283c85bbd9995ab5562e5`

I ran linpeas, pspy64 and found nothing interesting. Surfing around the hints in the forum I got a OS level local Priv.Esc

So ran the command: `cat /etc/os-release`
Searched for this recent vuln and thought of executing it....

Without any realisation I have fallen for the trap.

The Rabbit hole Vuln I thought would be: https://www.exploit-db.com/exploits/51180

PS: IDK how it worked
![HTB](/assets/img/htb/analytics2.png)

![HTB](/assets/img/htb/analytics1.png)

So after a long time, got tired and used Cheat from a blog on how to priv.Esc

Got root flag: `9db8ff50d04f69cab85645c59091d608`

But I don't know how this vuln worked on Ubuntu 22.04.3 which is meant to work on versions lesser than Ubuntu 20.04

But the actual [Vulnerability Link](https://www.exploit-db.com/docs/49916)

Used POC: [Github Link](https://github.com/cerodah/overlayFS-CVE-2021-3493)