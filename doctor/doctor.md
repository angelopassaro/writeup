---
layout: default
title: Doctor
permalink: /writeup/doctor/
lang: en
---

<img src="./img/doctor.png"  width="650"/>

Doctor is a Linux machine. The ip of the box is 10.10.10.209.

# Recon

I start with *nmap* `nmap -sC -Sv -oA nmap/doctor 10.10.10.209`

<img src="./img/nmap.png"  width="650"/>

And found three services:
- *ssh*
- *webserver apache*
- *Splunk*

So i enum the port 80 and found a login page.

<img src="./img/login.png"  width="650"/>

and a register page.

<img src="./img/register.png"  width="650"/>

I can create a new user and then login and can create posts.
<img src="./img/new.png"  width="650"/>

When i create a new post the title is reflected on http://doctors.htb/archive.

<img src="./img/archive1.png"  width="650"/>


# User

So i [tested](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection#methodology) for SSTI. The template used is Jinja2. So i created a post with a payload 
<img src="./img/payload.png"  width="650"/>

and got a revshell as user **web**.

<img src="./img/revshell.png"  width="650"/>

The user web belogns the group **adm** so i can read log files.
In  `/var/log/apache2` there is a **backup** file and inside found the credential for the user **shaun**

<img src="./img/dumppass.png"  width="650"/>

So i login as shaun (i used a [script](./exploit.py) for obtain shell)

<img src="./img/shaun.png"  width="650"/>

and take the user flag.

<img src="./img/userflag.png"  width="650"/>

# Root
For the root there is a [exploit](https://github.com/cnotin/SplunkWhisperer2) for Splunk. 

<img src="./img/rootflag.png"  width="650"/>



<img src="./img/doctorpwn.png"  width="650"/>
