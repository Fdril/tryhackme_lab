# Red
# PYrat

Pyrat receives a curious response from an HTTP server, which leads to a potential Python code execution vulnerability. With a cleverly crafted payload, it is possible to gain a shell on the machine. Delving into the directories, the author uncovers a well-known folder that provides a user with access to credentials. A subsequent exploration yields valuable insights into the application’s older version. Exploring possible endpoints using a custom script, the user can discover a special endpoint and ingeniously expand their exploration by fuzzing passwords. The script unveils a password, ultimately granting access to the root.

``` sudo nmap 10.10.204.34 -oN scan -sC -sV -v ```

 sudo

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
