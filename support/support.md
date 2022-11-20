---
layout: default
title: Support
permalink: /writeup/support/
lang: en
---

<img src="./img/support.png"  width="650"/>

Support is a Windows machine. The IP of the box is 10.10.11.174.

# Recon

I starting with *nmap* scan `nmap -sV -sC  -oA nmap/support 10.10.11.174`

<img src="./img/nmap.png"  width="650"/>


and there are open the standard port for Windows DC.

Is possible access without credential to share 

<img src="./img/smbclient.png"  width="650"/>

and access to the directory `support-tools` 

<img src="./img/support-tools.png"  width="650"/>



# User
Inside the directory there is an archive `UserInfo.exe.zip` by extracting the content and analyze it with `ILSpy` is possible find the credential for the user `ldap` inside the classes `ldapquery` 

<img src="./img/ldapquery.png"  width="650"/>

and `protected`

<img src="./img/protected.png"  width="650"/>

The password is encrypted and is necessary to run the function `getPassword()` and obtain the password `nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz`.


With the credentials of the user `ldap` is possible to access to LDAP and retrive the credential for the user `support`

<img src="./img/ldap.png"  width="650"/>


and access with winrm and grab the flag


<img src="./img/winrm.png"  width="650"/>


# Root

I retrived the infos on DC with `bloodhound-python`

<img src="./img/bloodhound.png"  width="650"/>

By analyzating the output with `bloodhound` the user have the right `GenericAll` over the computer `DC.SUPPORT.HTB` so following this [link](https://bloodhound.readthedocs.io/en/latest/data-analysis/edges.html#genericall) is possible obtain full control of a computer object can be used to perform a resource based constrained delegation attack.

<img src="./img/bloodhound2.png"  width="650"/>

With `powermad` and `powerview` was added a new computer and created a new ACE to insert inside the field `msDS-AllowedToActOnBehalfOtherIdentity`

<img src="./img/computer.png"  width="650"/>

Successively was request a Service Ticket and got exection as `Administrator`

<img src="./img/impacket.png"  width="650"/>

and the flag

<img src="./img/root.png"  width="650"/>



<img src="./img/supportpwn.png"  width="650"/>
