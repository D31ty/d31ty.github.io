---
author: d31ty
date: 2023-10-15
linktitle: layoftheland-thm
title: TryHackMe - Lay of the Land - Post Compromise
showreadingtime: true
tags: ['THM']
categories: ['THM']
---
### Internal Network:
![internal-net.png](/images/LayofLand-THM/internal-net.png)
### A Demilitarized Zone (DMZ)
![DMZ.png](/images/LayofLand-THM/DMZ.png)
#### Network Enumeration
```
netstat -na
arp -a
```
## AD - Environment
##### Components of AD
List of Active Directory components that we need to be familiar with:

- Domain Controllers
- Organizational Units
- AD objects
- AD Domains
- Forest
- AD Service Accounts: Built-in local users, Domain users, Managed service accounts
- Domain Administrators
![AD.png](/images/LayofLand-THM/AD.png)
##### Checking for AD
```
systeminfo | findstr Domain
```
##### Admin Groups
|   |   |
|---|---|
|BUILTIN\Administrator|Local admin access on a domain controller|
|Domain Admins|Administrative access to all resources in the domain|
|Enterprise Admins|Available only in the forest root|
|Schema Admins|Capable of modifying domain/forest; useful for red teamers|
|Server Operators|Can manage domain servers|
|Account Operators|Can manage users that are not in privileged group|
##### AD Info
```
Get-ADUser  -Filter *
```
```
Get-ADUser -Filter * -SearchBase "CN=Users,DC=THMREDTEAM,DC=COM"
```
##### Host Security Solution - Info
1. **AntiVirus**
```
wmic /namespace:\\root\securitycenter2 path antivirusproduct
```
$$ or $$
```
Get-CimInstance -Namespace root/SecurityCenter2 -ClassName AntivirusProduct
```
2. **Microsoft Windows Defender**
```
Get-Service WinDefend
```
$$ or $$
```
Get-MpComputerStatus | select RealTimeProtectionEnabled
```
$$ or $$
```
Get-NetFirewallProfile | Format-Table Name, Enabled
```
```
Get-NetFirewallRule | select DisplayName, Enabled, Description
```
3. **Threat Details**
```
Get-MpThreat
```
##### Checking Network Firewall - Allow and Deny
```
Test-NetConnection -ComputerName 127.0.0.1 -Port 80
```
##### Security Event Log
```
Get-EventLog -List
```
##### System monitor
```
Get-Process | Where-Object { $_.ProcessName -eq "Sysmon" }
```
```
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\WINEVT\Channels\Microsoft-Windows-Sysmon/Operational
```
##### Installed Applications
```
wmic product get name,version
```
##### Hidden Dir:
`Get-ChildItem -Hidden -Path C:\Users\kkidd\Desktop\`
##### Processes:
```
net start

wmic service where "name like 'THM Demo'" get Name,PathName

Get-Process -Name thm-demo
```