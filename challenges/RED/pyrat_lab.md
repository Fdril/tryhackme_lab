# Red
# PYrat

Pyrat receives a curious response from an HTTP server, which leads to a potential Python code execution vulnerability. With a cleverly crafted payload, it is possible to gain a shell on the machine. Delving into the directories, the author uncovers a well-known folder that provides a user with access to credentials. A subsequent exploration yields valuable insights into the application’s older version. Exploring possible endpoints using a custom script, the user can discover a special endpoint and ingeniously expand their exploration by fuzzing passwords. The script unveils a password, ultimately granting access to the root.
## STEP 1 -  nmap
``` sudo nmap 10.10.204.34 -oN scan -sC -sV -v ```  
#### port 22
<img width="611" height="79" alt="image" src="https://github.com/user-attachments/assets/d8d161ec-d2a6-4893-be47-4f923f8c8c50" />\
#### port 8000
<img width="609" height="436" alt="image" src="https://github.com/user-attachments/assets/d928c887-c084-48bc-ad69-80f6ffb6430a" />\




1. SUdo 
Runs the command with administrator/root privileges.
Nmap needs root for certain scans (like some scripts, OS detection, or advanced probing).
2. nmap

The Nmap tool itself — used for network discovery and security auditing.
3. 10.10.154.18

The target IP address you’re scanning.
This could be a host in a TryHackMe, Hack The Box, or lab environment.
4. -oN nmap

    -oN → Output in normal format (readable text).

    nmap → File name to save the results to.
    📄 This means your scan results will be stored in a file called nmap in the current directory.

5. -sC

Runs Nmap’s default scripts (--script=default).

    These are part of NSE (Nmap Scripting Engine).

    They check for common vulnerabilities, misconfigurations, and gather extra service info.

6. -sV

Enables service version detection.

    Attempts to identify the software and version running on each open port.
### explaining the output

    PORT — port number and protocol (e.g. 22/tcp = TCP port 22).

    STATE — whether the port is open, closed, filtered, etc. open means a service is listening and responding.

    SERVICE — the common name for the service nmap thinks runs there (e.g. ssh, http-alt).

    REASON — how nmap determined the state (e.g. syn-ack means the remote host replied to our TCP SYN with SYN-ACK).

    VERSION — nmap’s guess at the software and version running on that port (banner/service fingerprinting).

Port 1 — 22/tcp open ssh syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu ...

Breakdown:

    22/tcp — TCP port 22 (standard SSH port).

    open — SSH is accepting connections.

    ssh — nmap identifies the service as SSH.

    syn-ack ttl 63 — nmap sent a SYN and the host replied with SYN-ACK; TTL (time-to-live) 63 is the value in the reply (gives a tiny hint about OS/hops — many Linux boxes start at 64 so 63 often means one hop less). Don’t rely on TTL alone for OS detection.

    OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 — the SSH server banner: OpenSSH version 8.2p1 on an Ubuntu package. This leaks useful info (what software and version is running).
    | (pipe) lines = script output. |_ marks the end of that block.

    ssh-hostkey: — nmap pulled the server’s public host keys (what the server uses to prove its identity).

    Each key line shows:

        key size (e.g. 3072 bits for RSA),

        fingerprint (hex groups like 0c:9b:...) — a short representation you can compare to detect changes (if a host key changes unexpectedly it could be a security issue or MITM),

        the algorithm (RSA, ECDSA, ED25519).

    The long AAAAB3NzaC1... string is the server’s public key in base64 form. It’s normal; clients store a fingerprint in ~/.ssh/known_hosts to remember servers.

What to take away: SSH is up and giving a normal banner + host keys. That’s useful for connecting (if authorized) and for detecting host-key changes later.\

Port 2 — 8000/tcp open http-alt syn-ack ttl 63 SimpleHTTP/0.6 Python/3.11.2\

<img width="609" height="436" alt="image" src="https://github.com/user-attachments/assets/d928c887-c084-48bc-ad69-80f6ffb6430a" />\

Breakdown:

    8000/tcp — TCP port 8000 (commonly used for dev web servers or alternate HTTP).

    open — an HTTP-like service is listening.

    http-alt — nmap’s name for an alternate HTTP port.

    SimpleHTTP/0.6 Python/3.11.2 — the server header: this looks like Python’s simple HTTP server (a small built-in HTTP server), running under Python 3.11.2.

Script and detection output under 8000

|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: SimpleHTTP/0.6 Python/3.11.2
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
|_http-favicon: Unknown favicon MD5: FBD3...
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
| fingerprint-strings: 
|   ... various probe responses ...

Explain each:

    http-open-proxy: Proxy might be redirecting requests
    Nmap’s proxy-check script suspects the server may act like a proxy (i.e., it could forward requests to other sites). This could be a valid behavior or a false positive — verify manually. An open proxy can be abused if exposed to the public.

    http-server-header — repeats the Server: header sent by the web service. This leaks product/version info (useful for attackers to look up known vulnerabilities).

    http-title: Site doesn't have a title — the HTML page returned had no <title> tag. Not important security-wise, but a hint about page content (maybe directory listing or minimal page).

    http-favicon: Unknown favicon MD5: — nmap hashed the site’s favicon and prints the MD5; this helps fingerprinting known apps (some tools/databases match favicon hashes to web apps).

    http-methods: Supported Methods: GET HEAD POST OPTIONS — lists HTTP methods the server accepts:

        GET — fetch a resource (normal reading),

        HEAD — fetch headers only,

        POST — send data (forms, APIs),

        OPTIONS — ask the server what methods are allowed.
        Missing PUT/DELETE often means less risk of remote file modification, but don’t assume.

    fingerprint-strings — nmap tries many different probes (like simulating weird or non-HTTP traffic) to see how the service reacts. The quoted responses (e.g. invalid syntax (<string>, line 1) or name 'GET' is not defined) are strings the server returned when fed those probes.

        Those responses look like Python error messages — which matches the SimpleHTTP server identification. So nmap matched those unique responses to identify the server type.

Small notes on syn-ack ttl 63

    syn-ack = positive TCP handshake reply (so port is definitely open).

    ttl 63 is the TTL value the remote host used in its reply. Common starting TTLs are 64/128/255; seeing 63 often means the host started at 64 and it decremented once on the path. TTL can give a very rough hint of OS or number of network hops, but it’s not definitive.
### next advisable step
***check the http header title***  
curl -I http://10.10.204.34:8000  

curl -v http://10.10.204.34:8000/


# STEP 2 
type 
```10.10.204.34:8000```\

then ***netcat***
```sudo nc 10.10.204.34 8000 -v```\
<img width="468" height="136" alt="image" src="https://github.com/user-attachments/assets/409f6343-7a63-43ed-a1cc-763110b86831" />


The name of the room gave us already a hint that python would be included to some degree. This confirms it. We can execute Python code. Trying some commands resulted in enumerating the system with basically those two commands:
``` print(os.listdir('/'))```\
``` print(open('/filename', 'r').read()) ```\
#### the explanation of the codes.
firstline\

    print(os.listdir('/'))
    os.listdir(path) → Lists all files and directories in the given path.
    Here the path is '/' — the root directory in a Linux/Unix file system (kind of like “C:\” in Windows).
    So os.listdir('/') returns something like:
    ['bin', 'boot', 'dev', 'etc', 'home', 'lib', ...]
    print(...) just outputs that list to the console.\
<img width="614" height="196" alt="image" src="https://github.com/user-attachments/assets/45d97ed7-bb67-4ab0-b6a5-fcfd52118310" />\

Second line: **we can do best and be checking through each dir. to know the files in each of them to know what we are up to**

    print(open('/filename', 'r').read())
    open(path, 'r') → Opens the file located at / <filename> in read mode ('r').\ filename of any of the above dir.
    .read() → Reads the entire file contents into memory.
    print(...) → Prints that content to the console.\

*** had to check each dir oen-by-one to know which one we can start with***\
<img width="622" height="441" alt="image" src="https://github.com/user-attachments/assets/7b20f810-3deb-44fe-8b3f-db44ef04af80" />\
/home is a dead end as it need permission.
so\
let's use var.\
<img width="610" height="445" alt="image" src="https://github.com/user-attachments/assets/521f580d-c47e-49af-8e70-6cd6e1a16560" />\
so , we found a e-mail in (/var/mail/think) ___ but there was nothing in (/var/mail/www-data) as it was empty.  
#### let's continue to check the directories we might find some other useful stuffs.
checking back on the email the letter was written by root_user.\
From root@pyrat  Thu Jun 15 09:08:55 2023
Return-Path: <root@pyrat>
X-Original-To: think@pyrat
Delivered-To: think@pyrat
Received: by pyrat.localdomain (Postfix, from userid 0)
        id 2E4312141; Thu, 15 Jun 2023 09:08:55 +0000 (UTC)
Subject: Hello
To: <think@pyrat>
X-Mailer: mail (GNU Mailutils 3.7)
Message-Id: <20230615090855.2E4312141@pyrat.localdomain>
Date: Thu, 15 Jun 2023 09:08:55 +0000 (UTC)
From: Dbile Admen <root@pyrat>
Hello jose, I wanted to tell you that i have installed the RAT you posted on your GitHub page, i'll test it tonight so don't be scared if you see it running. Regards, Dbile Admen\






