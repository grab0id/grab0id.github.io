---
layout: post
title:  "Kioptrix Level Two"
date:   2020-07-27
categories: blog
tags:   [hacking, Kioptrix]
---
**[Disclaimer!][1]**

### Kioptrix Level Two
Download: [VulnHub][2]  
**NOTE:** Always exercise caution when downloading VMs of this nature (or anything for that matter) 
from the internet. VulnHub offers some guidance around protecting yourself and your home network, 
and there are other resources out there to help as well. I have provided the link to VulnHub only 
as a reference, and cannot be held liable for what you download.

Starting out with a quick ```nmap``` scan:

```
sudo nmap -vv -sV -sC -O 10.0.100.26
Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-27 12:01 EDT
...
Scanning 10.0.100.26 [1 port]
...
Scanning 10.0.100.26 [1000 ports]
Discovered open port 22/tcp on 10.0.100.26
Discovered open port 111/tcp on 10.0.100.26
Discovered open port 80/tcp on 10.0.100.26
Discovered open port 443/tcp on 10.0.100.26
Discovered open port 3306/tcp on 10.0.100.26
Discovered open port 631/tcp on 10.0.100.26
Completed SYN Stealth Scan at 12:01, 0.12s elapsed (1000 total ports)
Initiating Service scan at 12:01
Scanning 6 services on 10.0.100.26
Completed Service scan at 12:01, 14.02s elapsed (6 services on 1 host)
Initiating OS detection (try #1) against 10.0.100.26
NSE: Script scanning 10.0.100.26.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 12:01
...
Nmap scan report for 10.0.100.26
Host is up, received arp-response (0.00077s latency).
Scanned at 2020-07-27 12:01:43 EDT for 106s
Not shown: 994 closed ports
Reason: 994 resets
PORT     STATE SERVICE    REASON         VERSION
22/tcp   open  ssh        syn-ack ttl 64 OpenSSH 3.9p1 (protocol 1.99)
| ssh-hostkey: 
|   1024 8f:3e:8b:1e:58:63:fe:cf:27:a3:18:09:3b:52:cf:72 (RSA1)
| 1024 35 149174282886581624883868648302761292182406879108668063702143177994710569161669502445416601666211201346192352271911333433971833283425439634231257314174441054335295864218587993634534355128377261436615077053235666774641007412196140534221696911370388178873572900977872600139866890316021962605461192127591516843621
|   1024 34:6b:45:3d:ba:ce:ca:b2:53:55:ef:1e:43:70:38:36 (DSA)
| ssh-dss AAAAB3NzaC1kc3MAAACBAOWJ2N2BPBPm0HxCi630ZxHtTNMh+uVkeYCkKVNxavZkcJdpfFTOGZp054sj27mVZVtCeNMHhzAUpvRisn/cH4k4plLd1m8HACAVPtcgRrshCzb7wzQikrP+byCVypE0RpkQcDya+ngDMVzrkA+9KQSR/5W6BjldLW60A5oZgyfvAAAAFQC/iRZe4LlaYXwHvYYDpjnoCPY3xQAAAIBKFGl/zr/u1JxCV8a9dIAMIE0rk0jYtwvpDCdBre450ruoLII/hsparzdJs898SMWX1kEzigzUdtobDVT8nWdJAVRHCm8ruy4IQYIdtjYowXD7hxZTy/F0xOsiTRWBYMQPe8lW1oA+xabqlnCO3ppjmBecVlCwEMoeefnwGWAkxwAAAIAKajcioQiMDYW7veV13Yjmag6wyIia9+V9aO8JmgMi3cNr04Vl0FF+n7OIZ5QYvpSKcQgRzwNylEW5juV0Xh96m2g3rqEvDd4kTttCDlOltPgP6q6Z8JI0IGzcIGYBy6UWdIxj9D7F2ccc7fAM2o22+qgFp+FFiLeFDVbRhYz4sg==
|   1024 68:4d:8c:bb:b6:5a:bd:79:71:b8:71:47:ea:00:42:61 (RSA)
|_ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAIEA4j5XFFw9Km2yphjpu1gzDBglGSpMxtR8zOvpH9gUbOMXXbCQeXgOK3rs4cs/j75G54jALm99Ky7tgToNaEuxmQmwnpYk9bntoDu9SkiT/hPZdOwq40yrfWIHzlUNWTpY3okTdf/YNUAdl4NOBOYbf0x/dsAdHHqSWnvZmruFA6M=
|_sshv1: Server supports SSHv1
80/tcp   open  http       syn-ack ttl 64 Apache httpd 2.0.52 ((CentOS))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.0.52 (CentOS)
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
111/tcp  open  rpcbind    syn-ack ttl 64 2 (RPC #100000)
443/tcp  open  ssl/https? syn-ack ttl 64
|_ssl-date: 2020-07-27T16:03:29+00:00; +1m00s from scanner time.
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|_    ...
631/tcp  open  ipp        syn-ack ttl 64 CUPS 1.1
| http-methods: 
|   Supported Methods: GET HEAD OPTIONS POST PUT
|_  Potentially risky methods: PUT
|_http-server-header: CUPS/1.1
|_http-title: 403 Forbidden
3306/tcp open  mysql      syn-ack ttl 64 MySQL (unauthorized)
MAC Address: 00:0C:29:98:61:0A (VMware)
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6
OS details: Linux 2.6.9 - 2.6.30
TCP/IP fingerprint:
...

Uptime guess: 0.064 days (since Mon Jul 27 10:31:15 2020)
Network Distance: 1 hop
TCP Sequence Prediction: Difficulty=196 (Good luck!)
IP ID Sequence Generation: All zeros

Host script results:
|_clock-skew: 59s

NSE: Script Post-scanning.
...
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 106.91 seconds
           Raw packets sent: 1020 (45.626KB) | Rcvd: 1016 (41.362KB)
```

The first things that jump out at me are the Apache server version, as well as fact that the server
is running MySQL. So I start on the website, which gets us to a login page:
![Login page](/assets/images/kioptrix2-site-login.png)

The easiest way to start out here is to test the basic credentials of admin/admin, but that didn't 
work, so then I moved on to a simple authentication bypass SQL injection. There are a few different
methods that could work here, so I tried the following:
 1. a' or 1=1;#
 1. a' or 1=1;--
 1. ' OR 'a' = 'a

The final string I got from the Exploid-DB page about SQL authentication bypass [here][3], and 
this was successful in allowing me to login as 'admin'. This brings up an administative page
allowing the admin to ping devices on the network: 
![Admin page](/assets/images/kioptrix2-site-ping.png)

And a quick test of the web app shows us how it appears to work: 
![Ping results page](/assets/images/kioptrix2-ping-results.png)

From what we see here, it would seem that the web app takes your input and passes it along to the 
standard Linux ping command, then returns the results on another tab. Next steps in my mind are to 
test out what kind of limitations are on this functionality. My favorite string to test in these 
cases is `input; whoami`. For those who might not be immediately familiar with the command line, 
the semi-colon tells the command line that, as soon as the first command has finished running, run 
the next command. The result is that you get the output from both commands with one "command", 
which is shown below:  
![whoami test page](/assets/images/kioptrix2-ping-whoami-test.png)

Above, we see that we got additional output when we ran the test, which tells us two things:
 1. The web app is not performing any evaluation of the input, and is passing a literal string to 
 the command line.
 1. The web server is running as the 'apache' user.

What this testing has given us is proof of remote command execution, which we are going to use to 
gain additional access into teh system. The next things that I like to test for are:  
```bash
cat /etc/passwd
cat /etc/shadow
nc --version
curl --version
```

The 'passwd' file is readable by everyone, and is a great source of information and if we can read 
the 'shadow' file that means the user executing the commands has root privileges and we would get 
the password hashes for the users on the computer. Netcat is a great program that can be used for 
lots of things, including remote shell and the transfer of files across the network. Curl can also 
be used to pull files across the network, if netcat isn't installed on the target. In our case, we 
read the 'passwd' file, couldn't read the shadow file, netcat isn't installed but curl is 
installed.

From here I want to get a shell on the computer, so I am going to look to `msfvenom` to develop a 
payload that will get me a remote shell.

[1]: /disclaimer/
[2]: https://www.vulnhub.com/entry/kioptrix-level-11-2,23/
[3]: https://www.exploit-db.com/papers/14340