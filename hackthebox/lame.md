---
description: Walkthrough (Easy)
---

# Lame🟢🟢🟢

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

[https://app.hackthebox.com/machines/Lame](https://app.hackthebox.com/machines/Lame)&#x20;

About Lame

Lame is a beginner level machine, requiring only one exploit to obtain root access. It was the first machine published on Hack The Box and was often the first machine for new users prior to its retirement.

### Reconnaissance

```bash
nmap -T4 -sVC 10.10.10.3 -oA Nmap
```

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

* Found it is using ftp vsftpd 2.3.4 -> searched in google got a famous backdoor exploit. But didn't work for this website.
* Then found it is using Samba 3.0.20 -> Search in google got CVE-2007-2447 Samba "username map script" Command Execution.

**Description**

This module exploits a command execution vulnerability in Samba versions 3.0.20 through 3.0.25rc3 when using the non-default "username map script" configuration option. By specifying a username containing shell meta characters, attackers can execute arbitrary commands. No authentication is needed to exploit this vulnerability since this option is used to map usernames prior to authentication!

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

* Loadup Metasploit and search for Samba 3.0.20 set Rhosts, Lhosts -> RUN

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

* Got your shell as Root user now check the user flag in /home -> user directory and root flag in /root directory

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>
