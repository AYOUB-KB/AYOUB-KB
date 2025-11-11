---
title: "Attacking LSASS"
categories: [ctf]
tags: [ctf, writeup, nahamcon, nahamconctf]
hasComments: true
date: 2025-11-11
image: /images/CTF/NAHAMCONCTF2020/logo.png
---

<p align="center">
  <img src="/images/CTF/NAHAMCONCTF2020/logo.png"  width="600"/>
</p>
  
Main CTF page: [https://ctf.nahamcon.com/](https://ctf.nahamcon.com/)  
CTFTime even page: [https://ctftime.org/event/1067](https://ctftime.org/event/1067)  
Conference page: [https://nahamcon.splashthat.com/](https://nahamcon.splashthat.com/)  

NahamCon 2020 was a conference and a CTF organized by [Naham Sec](https://twitter.com/NahamSec), [John Hammond](https://twitter.com/_johnhammond), [The Cyber Mentor](https://twitter.com/thecybermentor) and [STÖK](https://twitter.com/stokfredrik). The CTF was with no doubts one of the best I participated so far. I'd like to say thanks to all the admins, organizers and authors for this fantastic CTF. It was truly a great experience, the infrastructure was on point and the challenges as well. Top notch quality. The level was also very good, there were challenges for everybody ranging from beginner to advanced. Thank everybody involved in it for these ~30 hours, I enjoyed it and learned quite a few things, which is always the ultimate goal. Looking forward to the next instance :)

This time I participated along with [Kashmir54](https://twitter.com/ManuSanchez54). We finished in 205th-203rd position. We're happy not only with the result, but also with the concepts and techniques we've learned in this CTF. Each CTF feels like we're actually progressing, and that's fantastic. Our knowledge is still at beginners level but we are slowly and steady getting stronger :)

<p align="center">
  <img src="/images/CTF/NAHAMCONCTF2020/scoreboard.png"/>
  <img src="/images/CTF/NAHAMCONCTF2020/scoreboard0.png"/>
</p>


The CTF had several categories. In this page you will find the writeups for some of the challenges we solved:
  - Warmup:
    - Read the rules, CLIsay, Metameme, Mr.Robot, UGCC, Easy Keesy and Pang. 
  - Binary Exploitation:
    - Dangerous and SaaS.
  - OSINT:
    - Time Keeper, Finsta, New Years Resolution and Tron.
  - Forensics:
    - Microsooft and Volatile.

# Warmup
## Read The Rules

<p align="center">
  <img src="/images/CTF/NAHAMCONCTF2020/readtherules0.png"/>
</p>

This challenge was very easy, hence the category `warmup`. The flag was hidden in the [rules webpage](https://ctf.nahamcon.com/rules)'s source code. 

<p align="center">
  <img src="/images/CTF/NAHAMCONCTF2020/readtherules1.png"/>
</p>

Flag is: flag{we_hope_you_enjoy_the_game}

## CLIsay

<p align="center">
  <img src="/images/CTF/NAHAMCONCTF2020/clisay0.png"/>
</p>

In this challenge we're given a 64-bit ELF. Executing it requests for input. As always, the first thing to do with binaries is looking for hardcoded/static strings in them. 

This time turns out the flag was split in 2, but still hardcoded in the binary. 

<p align="center">
  <img src="/images/CTF/NAHAMCONCTF2020/clisay1.png"/>
</p>

Flag is: flag{Y0u_c4n_r3Ad_M1nd5}

Notice how I used `rabin2 -zzz` to get the strings of the binary. It is an alternative to the all-mighty `strings`. 


## Metameme

<p align="center">
  <img src="/images/CTF/NAHAMCONCTF2020/metameme0.png"/>
</p>

This time the downloaded file is a JPG image. 

<p align="center">
  <img src="/images/CTF/NAHAMCONCTF2020/metameme1.png"/>
</p>

The image looks pretty normal at a first glance. As always, the next step it to inspect the metadata in case there is something useful. This time there was! The flag was the author's name.

<p align="center">
  <img src="/images/CTF/NAHAMCONCTF2020/metameme2.png"/>
</p>

The flag is: flag{N0t_7h3_4cTuaL_Cr3At0r}

## Mr. Robot

<p align="center">
  <img src="/images/CTF/NAHAMCONCTF2020/mrrobot0.png"/>
</p>

In this challenge we must inspect a webpage and see where the flag is hidden. 

<p align="center">
  <img src="/images/CTF/NAHAMCONCTF2020/mrrobot1.png"/>
</p>
