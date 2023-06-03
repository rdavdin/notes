## [Configuring Linux Web Servers](https://www.udacity.com/course/configuring-linux-web-servers--ud299) - Part 2
[Part 1](/Configuring-Linux-Web-Servers-part1.md)

### Linux Security

#### The Rules of Least Privilege
- A user or an application only has enough permission to do its job, **nothing extra**.

#### Becomming a Super User
- It's very common to **disable** the ability to **remotely login as root**.
- By default, Ubuntu disables the **root** account for ssh login.

#### sudo vs su
- It's typically regarded as a best practice that you not use the ```su``` command. Why?
- Because of the rules of least privilege.
  - You **do not really** need to switch your entire working context over to the root user to run **a single or even a few** commands.
  - If you forget that you're currently within ```su```, you could potentially do some extremely damaging operations, and there'is no warning when you do so.
- ```/etc/sudoers```: Defines which users can run which commands with elevated privileges using the sudo command.

#### Package Source Lists
Try the command below
```
cat /etc/apt/sources.list
```

#### Updating Available Package Lists
- One of the most important and simplest ways to ensure your system is secure is to **keep your software up to date** with new releases.
- Update the package source lists
```
sudo apt-get update
```

- The update command will run through all of the repositories we saw within the file ```/etc/apt/sources.list```, and it will check to see what all softwares are available and what those version numbers are.
- **Remember** that the update command **doesn't actually perform any changes** to the software on your system. It just makes sure that your system is aware of the latest information stored within all of these repositories that you're making use of.

#### Upgrading Installed Packages
- Now our system is aware of what all software is available, and the most recent version numbers. 
- It's now time to actually update the software with the command below
```
sudo apt-get upgrade
```

#### Other Package Related Tasks
Read more about ```man apt-get```
Remove packages that were automatically installed and are no longer required
```
sudo apt-get autoremove
```

Install new package
```
sudo apt-get install <package-name>
```

Remove a package
```
sudo apt-get remove <package-name>
```

#### Introduction to /etc/passwd
- This is a very important file on your system. It's used to keep track of all users on the system. 
- Run ```cat /etc/passwd``` to have a look the output:

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
.
.
.
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
rdav:x:1000:1000:rdavdin:/home/rdav:/bin/bash
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
```

- Each line is organized in this format:
```username:password:UID:GID:UID info:home directory:command/shell```

1. username: the user's login name
2. password: the password, will simply be an x if it's encrypted
3. user ID (UID): the user's ID number in the system. 
  - 0: is root
  - 1-99: are for predefined users
  - and 100-999: are for other system accounts
4. group ID (GID): Primary group ID, stored in ```/etc/group```
5. user ID info: Metadata about the user, such as: user's full name or application name if _the account is for a program or service_, telephone number or any other contact info
6. home directory: where the user is sent upon login
7. command/shell: The absolute path of a command or shell. 

###### Some more explanation
1. The value of the field _command/shell_ is ```/usr/sbin/nologin```, which means that the user is **not allowed** to login to the system.
2. _the account is for a program or service_ means the program or service runs under that user account that is **created specially** for that program or service. In other words, the program or service is executed **with the permission and privileges** of that user account. This is done for **security reasons**, as it allows the program or service to run with **limited privileges and access** only the resources that it needs to function properly.
- For example, The Apache service runs under a user account called ```www-data``` on Ubuntu systems. This account is created specially for the Apache service to run under, and it has limited privileges that prevent it from accessing sensitive system resources.

### Introduction to User Management

#### Creating a New User

```
sudo adduser <username>
 ```
#### ```/etc/sudoers``` and ```/etc/sudoers.d```

Look the last line in the file ```/etc/sudoers```
```
#includedir /etc/sudoers.d
```
The ```#includedir``` directive is similar to ```#include``` directive, but instead of including a single file, it includes an entire directory. 
So, ```#includedir /etc/suduoers.d``` tells the system to look through any files in the ```/etc/sudoers.d``` directory and include those as if they **were written directly** within the sudoers file.

To understand the meaning of the ```sudoers``` refers to [this link](https://help.ubuntu.com/community/Sudoers). Especially, _User Specifications_ that are where the _sudoers file_ **sets who can run what as who**.
A user specification is in the format:
```
<user list> <host list>= <operator list> <tag list> <command list>
```

- _user list_: a list of users or a user alias that has already been set
- _host list_: a list of hosts or a host alias
- _operator list_: a list of users they must be running as or a runas alias
- _command list_: a list of commands or a command alias
- _tag list_: allows you set special things for each command. For example, you can use _PASSWD_ and _NOPASSWD_ to specify whether the user has to enter a password or not.

###### How to assign the user account _student_ that can run command _cat_ as _root_ on _any host_ without entering a password?
- create a file named _student_ (or any name) under ```/etc/sudoers.d```
- edit the student file ```student ALL=(root) NOPASSWD: /usr/bin/cat```
- that's it!

##### Force users password to expire
```
sudo passwd -e <user>
```

**Try some other flags of ```passwd```**

Change password of a user account
```
sudo passwd <user>
```

Lock a user account
```
sudo passwd -l <user>
```

Unlock a user account
```
sudo passwd -u <user>
```

Get password expiration data for <user>
```
chage -l <user>
```

#### Key Based Authentication: Much More Secure
##### Generate Key Pairs on Client/Local Machine
```
ssh-keygen
```

- Generate a key pair with one private key and one public key. The private key is kept secretly on your local, and the public key is placed on our server.
- Key types that ```ssh-keygen``` supports with SSH protocol version 2: **DSA**, **ECDSA**, **RSA**, and **ED25519**

##### Installing a Public Key
- Log in to the server with a user account. E.g: ```student```
- ```cd ~```
- ```mkdir .ssh```
- ```touch .ssh/authorized_keys``` This is a special file that will store all of **the public keys** that this account (```student```) is allowed to use for authentication, with one key per line in that file.
- edit the ```authorized_keys``` file by adding the public key to a new line
- set permissions on ```.ssh``` directory with 700 and ```authorized_keys``` file with 644:
```
chmod 700 .ssh
chmod 644 .ssh/authorized_keys
```
- that's it! Now you can login to the server with the private key as below:
```
ssh -i /path/to/private/key username@server_ip_address
```

##### Forcing Key Based Authentication: Disable The Password Based Logins
Forcing all users only be able to login using a key pair.

- The configuration file ```/etc/ssh/sshd_config``` which is the service that's running on the server listening for all your ```ssh``` connections. In other words, this is a configuration file for a ssh server that is something like a http server.
- Edit the ```/etc/ssh/sshd_config``` at:
```
PasswordAuthentication no
```
- restart the ssh service
```
sudo systemctl restart ssh
```

### File Permissions
- _owner_ - _group_ - _world_
- three dashes: --- is for read-write-execute (r-w-x). So:
  - r: 4
  - w: 2
  - x: 1

#### Owner and Group

- **Remember:** In Linux, when a user account is created, a group with the same name is automatically created as well. 
- While many Unix-like systems assign regular users to a common group such as 'users', **modern Linux practice** is to create a unique, single-member group with the same name as the user. **This makes certain types of permission assignment easier.**

#### _chgrp_ and _chown_

- Change the group of a file using _chgrp_
- Change the owner of a file using _chown_
- Only root or user with sudo privileges executes two these commands
```
sudo chgrp root .bash_history
sudo chown root .bash_history
sudo chgrp student .bash_history
sudo chown student .bash_history
```

### Intro to Ports

- Each application is configured to respond to requests destined for a specific port, such as a web server would respond on port 80, which is the default port for _HTTP_.
- We can control which ports our server is allowed to accept requests for using an application called **the firewall**.

Common Networking Ports:
  
| Port No | Port    | Protocol |
|:----:|:----:|:----:|
|21|FTP|TCP|
|22|SSH|TCP|
|23|TELNET|TCP|
|25|SMTP|TCP|
|53|DNS|TCP, UDP|
|67, 68|DHCP|UDP|
|80|HTTP|TCP|
|110|POP3|TCP|
|143|IMAP|TCP, UDP|
|443|HTTPS|TCP|

### Intro to Firewalls
- We should let our server listen on the ports required for our application to function correctly.
- We can configure which ports we want our server to listen to by using an application called **firewall**.
![Firewalls Illustration](/imgs/linux/firewall-illustration.png)
![Firewalls Illustration](/imgs/linux/HardwareFirewall.gif)

#### Intro to ```ufw```
- Ubuntu comes with a firewall **pre-installed** called ```ufw```, but it's not active.
```
rdav@serverrdav:~$ sudo ufw status
Status: inactive
```

Good practices for adding rules to our firewall:
1. Blocking all incoming requests
```
 sudo ufw default deny incoming
```
2. Any request to our server is trying out to send out to the Internet
```
sudo ufw default allow outgoing
```
**Remember:** The firewall now is also inactive. We actually have to **turn it on** ourselves **once we have everything how we want**.
**Configuring Ports in ```ufw```**
3. Allow the ports we know we'll need for the applications our server will be supporting. Surely, _ssh_
```
sudo ufw allow ssh
```
4. In case, the web application that we plan to support is a basic _HTTP_ server
```
sudo ufw allow www
```
**Remember:** ```www``` only allows _HTTP_ traffic. If you want to allow _HTTPS_ traffic, you need to allow port 443.
```
sudo ufw allow 443/tcp
```
5. That's it! It's time to enable our firewall
```
sudo ufw enable
```
6. Check the status ```sudo ufw status```

```
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
443/tcp                    ALLOW       Anywhere
22/tcp (v6)                ALLOW       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)
443/tcp (v6)               ALLOW       Anywhere (v6)
```