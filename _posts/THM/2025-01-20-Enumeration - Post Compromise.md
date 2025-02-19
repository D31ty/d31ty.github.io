---
date: 2023-10-15
linktitle: enumeration-thm
title: TryHackMe - Enumeration Post Compromise
showreadingtime: true
tags: ['THM']
categories: ['THM','Learning']
---

## Linux Enumeration
**OS Version Details**
```
ls /etc/*-release
```
```
hostname
```
**Users and groups**
```
cat /etc/passwd
cat /etc/group
cat /etc/shadow
```
**Sensitive Info**
```
ls -lh /var/mail/
```
```
ls -lh /usr/bin/
ls -lh /sbin/
```
**Installed Packages**
```
dpkg -l
```
**Current user info**
```
who
whoami
last
id
```
**Network Details**
```
ip a s
cat /etc/resolv.conf
sudo netstat -plt
```

`netstat -atupn` will show _All TCP and UDP_ listening and established connections and the _program_ names with addresses and ports in _numeric_ format.
**Running Services**
```
ps axf
```
## Windows Enumeration
**Systeminfo**
```
systeminfo
```
**Installed Updates**
```
wmic qfe get Caption,Description
```
**Windows Services**
```
net start
```
**Users**
```
whoami /priv
whoami /groups
net user
net localgroup
net localgroup administrators
net accounts
net accounts /domain
```
**Networking**
```
ipconfig /all
netstat -abno
arp -a
```
**DNS**
```
dig -t AXFR DOMAIN_NAME @DNS_SERVER
```
**SMB**
```
net share
```
## Other Tools
>List
### Sysinternals Suite

The [Sysinternals Suite](https://docs.microsoft.com/en-us/sysinternals/downloads/) is a group of command-line and GUI utilities and tools that provides information about various aspects related to the Windows system. To give you an idea, we listed a few examples in the table below.

|Utility Name|Description|
|---|---|
|Process Explorer|Shows the processes along with the open files and registry keys|
|Process Monitor|Monitor the file system, processes, and Registry|
|PsList|Provides information about processes|
|PsLoggedOn|Shows the logged-in users|

Check [Sysinternals Utilities Index](https://docs.microsoft.com/en-us/sysinternals/downloads/) for a complete list of the utilities. If you want to learn more and experiment with these different utilities, we suggest the [Sysinternals](https://tryhackme.com/room/btsysinternalssg) room.

### Process Hacker

Another efficient and reliable MS Windows GUI tool that lets you gather information about running processes is [Process Hacker](https://processhacker.sourceforge.io/). Process Hacker gives you detailed information regarding running processes and related active network connections; moreover, it gives you deep insight into system resource utilization from CPU and memory to disk and network.

### GhostPack Seatbelt

[Seatbelt](https://github.com/GhostPack/Seatbelt), part of the GhostPack collection, is a tool written in C#. It is not officially released in binary form; therefore, you are expected to compile it yourself using MS Visual Studio.

## Summary
### Commands and Cheatsheet

|Linux Command|Description|
|---|---|
|`hostname`|shows the system’s hostname|
|`who`|shows who is logged in|
|`whoami`|shows the effective username|
|`w`|shows who is logged in and what they are doing|
|`last`|shows a listing of the last logged-in users|
|`ip address show`|shows the network interfaces and addresses|
|`arp`|shows the ARP cache|
|`netstat`|prints network connections|
|`ps`|shows a snapshot of the current processes|

|Windows Command|Description|
|---|---|
|`systeminfo`|shows OS configuration information, including service pack levels|
|`whoami`|shows the user name and group information along with the respective security identifiers|
|`netstat`|shows protocol statistics and current TCP/IP network connections|
|`net user`|shows the user accounts on the computer|
|`net localgroup`|shows the local groups on the computer|
|`arp`|shows the IP-to-Physical address translation tables|
