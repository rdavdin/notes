## [Configuring Linux Web Servers](https://www.udacity.com/course/configuring-linux-web-servers--ud299) - Part 1

### Intro to Linux
#### Distributions
- _Red Hat Enterprise Linux_ is a popular distribution for enterprises. 
  - **NOT** free as in beer.
  - Companies have to pay a licensing fee.
  - They receive the support from _Red Hat_
- _Ubuntu_
  - Has various versions: servers, desktops, and mobile phones
  - receives consistent updates to it's software
- _Debian_ is the parent distribution of _Ubuntu_
  - is known for stability and reliability
  - its software updates cycle is very slow compared to others
![Linux Distribution](/imgs/linux/distributions.png)
#### Linux Commands
[Ubuntu Server CLI](/Ubuntu_Server_CLI_pro_tips_2020-04.pdf)

#### Linux file system
##### _home_
- All **standard users'** home directories will be within the _/home/_
- _root_ user is a special case. The _root_ home is in _/root/_
##### _etc_ 
- _etc_ is where **configuration files** live
- setting up the web or database server, we'll be modifying some files within this directory
##### _var_
- _var_ is for **variable files**
- variable files are files that you expect to grow or change in size over time
- system logs or application logs within this directory
##### _bin_
- _bin_ is where **executable binaries** are stored, that are accessible all users
- these applications you run like _ls_ cmd
##### _sbin_
- _sbin_ is similar to _bin_, except that these binaries are to **only** be accessed by the _root_ user for system administration and maintenance purposes
##### _lib_
- _lib_ is for libraries that support the **binaries** that are located **around the system**.
##### _usr_
- _usr_ is fro user programs 
- it's something similar to _bin_, but the only difference is that the binaries within _bin_ are required for a boot-up and system maintenance processes. For example, _ls_ is an essential function of the operating system, so it's loaded in memory while starting up. That is the reason why it's located in the _bin_ directory. 
- the binaries in _usr_ aren't required for that.
#### Environment variables 
[Refer to EnvironmentVariables](https://help.ubuntu.com/community/EnvironmentVariables)
###### A shell variable
```
VU='VU set without export'
```
- A shell variable does not influence the behavior of other applications
##### An environment variable
- A shell variable can be _exported_ to become an environment variable
```
VU='VU set without export'
export VU
```

Shortcut for creating an environment variable
```
export VU='VU set without export'
```
Use _env_ or _printenv_ to list all env variables in shell
###### Erasing env variables
```
export LINH=
```
Set an env variable to a shell variable
```
export -n LINH
```
###### Set the values of env variables as arguments
```
rdav@serverrdav:~$ cat ~/bin/myprg/echoenv
echo $VU
echo $LINH
rdav@serverrdav:~$ VU='VU set' LINH='LINH set' echoenv
VU set
LINH set
rdav@serverrdav:~$ echo $VU

rdav@serverrdav:~$
```
#### Common Environment Variables
##### _PATH_
- Why does the command ```ls``` work without indicating it to the full path like ```/bin/ls```?
- The _ls_ cmd works thanks to the environment variable _PATH_. This variable is **used by the shell** to **locate executable files**. 
- When you type a command at the shell prompt, the shell looks for the corresponding executable file in the directories listed in the PATH variable, **in the order in which they are listed**.
- Try ``echo $PATH``
  - **_$_**: access the **value** of the variable named **PATH**
```
rdav@serverrdav:~$ echo $PATH
/home/rdav/.nvm/versions/node/v18.16.0/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```
#### HOME
- This variable contains the path to the current user's home directory.
```
rdav@serverrdav:~$ echo $HOME
/home/rdav      <-- the home dir of user 'rdav'
rdav@serverrdav:~$ sudo su
root@serverrdav:/home/rdav# echo $HOME
/root           <-- the home dir of root 
```
#### USER
- contains the username of the current user
```
rdav@serverrdav:~$ echo $USER
rdav
rdav@serverrdav:~$ sudo su
root@serverrdav:/home/rdav# echo $USER
root
```
#### SHELL
- contains the path to the user's default shell
#### PS1
- defines the prompt string that is displayed in the shell
#### LANG
- defines the default languages and character set used by the system
```
rdav@serverrdav:~$ echo $LANG
en_US.UTF-8
```
#### TERM
- defines the type of terminal being used
```
rdav@serverrdav:~$ echo $TERM
xterm
```
###### _PATH=\$PATH:/home/rdav/myprg_ vs _export PATH=\$PATH:/home/rdav/myprg_
- What is a child process of the shell process? A child process that is created by the shell. For example:
  - type ```ls -la``` then press _enter_ in the shell
  - the shell will create a process from the binary file _ls_ under /bin directory, the process that has just created is a child process of the shell.
- **Remember** a child process inherits certain properties from the parent process.
- So, a child process created by the shell uses the environment variable _RDAV_, it will get the new value of the _RDAV_ if we set the _RDAV_ with _export_ keyword.
```
rdav@serverrdav:~$ echoenv
echopath: command not found
rdav@serverrdav:~$ PATH=$PATH:~/bin/myprg/
rdav@serverrdav:~$ echoenv


rdav@serverrdav:~$ VU='VU set without export'
rdav@serverrdav:~$ export RDAV='RDAV set with export'
rdav@serverrdav:~$ echoenvcp

RDAV set with export
rdav@serverrdav:~$
```

#### Bash files
- A Bash file, aka a Bash script, is a text file that contains a series of commands that can be executed by the Bash shell.
- Bash files are commonly used to **automate repetitive tasks** or **to perform complex operations** that would be difficult to execute manually.
- Bash scripting language is a high-level programming language that is based on the Bash shell.
##### Some important bash files on Ubuntu server:
**_RECHECK THE POINTS FOLLOWING_**
1. ```~/.pam_environment``` is not a script. It is used to to set env variables for a user. These variables are set when the user logs in and are available to **all processes started** by that user. In general, the ```~/.pam_environment``` is read before the user's shell is started. So, the bash shell is started, it inherits the env variables in the .pam_environment file.
2. ```/etc/profile``` contains system-wide environment variables and startup programs that are used by **all users**. This file is executed during the startup of the system.
3. ```~/.profile``` is a script that is executed whenever a user **logs into** the system interactively. It sets up the **environment** for the user, such as defining env variables, setting the _PATH_, and running other scripts. This is a **login shell**. It is executed during the login progress.
**NOTE:** code in ```~/.profile``` is run after ```~/.pam_environment``` has been read.
4. ```~/.bashrc``` contains user-specific environment variables and startup programs that are used by **individual users**. This is a non-login shell. It is executed during opening a new terminal.
5. ```/etc/bash.bashrc``` contains system-wide environment variables and startup programs that are used by all users, similar to ```/etc/profile```. But this is a non-login shell while ```/etc/profile``` is a login shell.
6. ```/etc/bash_completion``` contains command-line completion rules for bash, which help users complete and arguments more quickly.
7. ```/etc/skel/.bashrc``` is used as a template for new user accounts, and contains default environment variables and startup programs for new users.

###### The order in which bash files are read is:
1. /etc/profile
2. ~/.bash_profile
3. ~/.bash_login (if ~/.bash_profile does not exist)
4. ~/.profile (if neither ~/.bash_profile nor ~/.bash_login exist)

**These files are read during the Bash startup process.**

**Remind:** 
1. In general, the .pam_environment is read before these files.
2. ~/.bashrc file **is not** read during the Bash startup process. It is read by Bash every time a new interactive shell is started, such as when you open a new terminal window or tab.
> Shell config files such as ~/.bashrc, ~/.bash_profile, and ~/.bash_login are often suggested for setting environment variables. While this may work on Bash shells for programs started from the shell, variables set in those files are not available by default to programs started from the graphical environment in a desktop session. - [Source: help.ubuntu.com](https://help.ubuntu.com/community/EnvironmentVariables)

**_END: RECHECK THE POINTS_**

#### System-wide environment variables
##### /etc/environment
This file is specially meant for **system-wide environment variables** settings. It is not a script file, but rather consists of **assignment expressions**, one per line.
```
FOO=bar
```
```/etc/environment``` is similar to ```~/.pam_environment```, both are read by the **PAM module pam_env** during **login**. But, ```/et/environment``` sets **system-wide environment variables**, while ```~/.pam_environment``` sets **user-specific environment variables**.
##### /etc/profile.d/*.sh
- Files with the ```.sh``` extension in the **/etc/profile.d** directory get executed whenever a bash login shell is entered (e.g. when logging in from the console or over ssh), as well as by the DisplayManager when the desktop session loads.
- You can, for instance, create the file ```/etc/profile/myenvvars.sh``` and set variables for your programs, like this:
```
export JAVE_HOME=/usr/lib/jvm/jdk1.7.0
export PATH=$PATH:$JAVA_HOME/bin
 ```
##### /etc/profile
- While **/etc/profile** is often suggested for setting environment variables system-wide, it is a configuration file of the base-files package, so it's **not appropriate to edit** that file **directly**.
- You should use a file in **/etc/profile.d** as shown above.
- **Remember** that files in **/etc/profile.d** are sourced by **/etc/profile**. It means that whenever **/etc/profile** is run, all files with the extension ```.sh`` under **/etc/profile.d** will be run as well.
##### /etc/bash.bashrc
- ```/etc/bash.bashrc``` is the shell config file, it sometimes suggested for setting env variables system-wide.
- While this may work on Bash shells **for programs started from the shell**, variables set in that file are **not** available to programs started from the **graphical environment in a desktop session.
##### /etc/default
Debian Policy Manual:
> Often there are some variables in the init.d scripts whose values control the behavior of the scripts, and which a system administrator is likely to want to change. As the scripts themselves are frequently conffiles, modifying them requires that the administrator merge in their changes each time the package is upgraded and the conffile changes. To ease the burden on the system administrator, such configurable values should not be placed directly in the script. Instead, they should be placed in a file in /etc/default, which typically will have the same base name as the init.d script. This extra file should be sourced by the script when the script runs. It must contain only variable settings and comments in SUSv3 sh format. It may either be a conffile or a configuration file maintained by the package maintainer scripts. See Configuration files, Section 10.7 for more details.
To ensure that vital configurable values are always available, the init.d script should set default values for each of the shell variables it uses, either before sourcing the /etc/default/ file or afterwards using something like the : ${VAR:=default} syntax. Also, the init.d script must behave sensibly and not fail if the /etc/default file is deleted.

In short, the files in ```/etc/default``` contain default values that the system administrator wants to adjust the behavior for the programs respectively. If the administrator had adjusted these values in the scripts under ```/etc/init.d```, the changes would have been lost each time the programs updated/upgraded. 

Look a part code at the start of the file ```/etc/init.d/ufw```
```
for s in "/lib/ufw/ufw-init-functions" "/etc/ufw/ufw.conf" "/etc/default/ufw" ; do
    if [ -s "$s" ]; then
        . "$s"
    else
        log_failure_msg "Could not find $s (aborting)"
        exit 1
    fi
done
```

See, ```/etc/default/ufw``` is sourced by ```/etc/init.d/ufw```before starting/stopping the ```ufw``` service.
##### /etc/default/locale
- The system-wide **locale** environment variables that determine the language and cultural preferences for the entire system. For example, it affect the way that dates, times, currency, and other types of information are displayed.