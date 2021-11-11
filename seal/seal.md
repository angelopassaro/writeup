---
layout: default
title: Seal
permalink: /writeup/seal/
lang: en
---

<img src="./img/seal.png"  width="650"/>

Seal is a Linux machine. The IP of the box is 10.10.10.250.

# Recon

I starting with *nmap* scan `nmap -A -T4 -oA nmap/seal 10.10.10.250`

<img src="./img/nmap.png"  width="650"/>


And I found three services:
- *ssh*
- *webserver nginx on 443*
- *webserver on 8080*

On port 443 thre is a static webpage.

<img src="./img/home.png"  width="650"/>

while on port 8080 there is a login page for gitbucket.

<img src="./img/home2.png"  width="650"/>

I can signup and then navigate between repositories 

<img src="./img/repo.png"  width="650"/>

In the repo `seal_market` in the file `tomcat-users.xml` i found a old commit with the credentials of tomcat. 

<img src="./img/cred.png"  width="650"/>

**Credentials**: tomcat:42MrHBf\*z8{Z%

So i tried some default pages of tomcat like `10.10.10.250/host-manager/text` and got the login box.

<img src="./img/auth.png"  width="650"/>

So i inserted the credentials found before and got a 403.

<img src="./img/403.png"  width="650"/>

After a little bit of research i found the following [article](https://www.acunetix.com/vulnerabilities/web/tomcat-path-traversal-via-reverse-proxy-mapping/) that explain a vulnerability of tomcat with a reverse proxy. So if i go `https://seal.htb/manager/..;/manager/html` i can login and go in the Application Manager of tomcat.

<img src="./img/tomcat.png"  width="650"/>

# User

So now i can craft a reverse shell with msfvenom

<img src="./img/rev.png"  width="650"/>

Upload it from webpage

<img src="./img/deploy1.png"  width="650"/>

and intercept the request with Burp for add `..;` inside the path

<img src="./img/deploy2.png"  width="650"/>

And i got shell as `tomcat` user

<img src="./img/shell.png"  width="650"/>

After the enum i found a cronjob for ansible-playbook

<img src="./img/cron.png"  width="650"/>

the playbook (/opt/backups/playbook/run.yml) is the following

```yml
- hosts: localhost
  tasks:
  - name: Copy Files
    synchronize: src=/var/lib/tomcat9/webapps/ROOT/admin/dashboard dest=/opt/backups/files copy_links=yes
  - name: Server Backups
    archive:
      path: /opt/backups/files/
      dest: "/opt/backups/archives/backup-{{ansible_date_time.date}}-{{ansible_date_time.time}}.gz"
  - name: Clean
    file:
      state: absent
      path: /opt/backups/files/
```

The playbook copy the content of `/var/lib/tomcat9/webapps/ROOT/admin/dashboard` in `/opt/backups/files` and `copy_links=yes` from the doc: 

>Copy symlinks as the item that they point to (the referent) is copied, rather than the symlink.

So i can create a symlink the  `.ssh` folder of the user `luis`

<img src="./img/link1.png"  width="650"/>

Check if the sym link is created.
<img src="./img/link2.png"  width="650"/>

I wait for the start of the cron and pass the archive on local machine 

<img src="./img/extract1.png"  width="650"/>

and extract the archive

<img src="./img/extract2.png"  width="650"/>

Now can login as `luis`

<img src="./img/user1.png"  width="650"/>

and grab the the flag

<img src="./img/userflag.png"  width="650"/>



# Root

Now i launch `sudo -l`

<img src="./img/sudo-l.png"  width="650"/>

and found that is possibile execute `ansible-playbook` so i checked on [GTFOBins](https://gtfobins.github.io/) and found how exploit it

<img src="./img/root.png"  width="650"/>

and grab flag

<img src="./img/rootflag.png"  width="650"/>



<img src="./img/sealpwn.png"  width="650"/>
