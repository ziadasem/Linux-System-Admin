### 3 Sudoers File 
in this .md file some examples are given to show the working of the sudo command.

**NOTE: Before editing the Sudoers file, make sure that your root user has a password, as Ubuntu by default leaves the root account without a password.**

```bash
ziad@ziadpc:~/$ sudo passwd root #set a password for root
```
to check:
```bash
ziad@ziadpc:/home$ su root
Password: 
root@ziadpc:/home# 
```

we will set a password for root because if the sudoers file is misconfigured, the sudo command will not work.

#### **1- Switch to a user that not in sudoers file and execute Sudo command**

**`less` the sudoers file to find the privileged users**

```
# User privilege specification
root    ALL=(ALL:ALL) ALL

# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL
```

the syntex of these lines in the User privilege specification is

**User <space> OnHost = (Runas-User:Group) <space> Commands**

or read it as 

“**who where = (as_whom) what”.**

[src](https://medium.com/kernel-space/linux-fundamentals-a-to-z-of-a-sudoers-file-a5da99a30e7f)

so we can conclude that root, users in admin group, and users in sudo group are the privileged users.

List the sudo group and the admin group
```
ziad@ziadpc:/home$ grep sudo /etc/group
sudo:x:27:ziad
grep admin /etc/group
lpadmin:x:122:ziad
```
no admin group and the user ziad only in sudo group.

switch to any user, for example user ' b ', then can sudo on any command.

```bash
ziad@ziadpc:/home$ su b
Password: 
b@ziadpc:/home$ sudo cd ..
[sudo] password for b: 
b is not in the sudoers file.  This incident will be reported.
```
the error `
b is not in the sudoers file.  This incident will be reported.` indicates that user `b` wants to gain the privilege of another user and he is not a sudoer, it can be solved by two methods:

 1. add user `b` to the sudo group.
 ```bash
 b@ziadpc:/home$ su ziad
Password: 
ziad@ziadpc:/home$ sudo usermod -aG sudo b
ziad@ziadpc:/home$ su b
Password: 
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

b@ziadpc:/home$ sudo ls ..
bin   cdrom  etc     home  lib32  libx32      media  opt   root  sbin  srv	 sys  usr
boot  dev    export  lib   lib64  lost+found  mnt    proc  run	 snap  swapfile  tmp  var
b@ziadpc:/home$ 

 ```
since it is the first time that `b` login as a sudo, a welcome message for him is shown.
 
  2. edit the User privilege specification section in the sudoers file and add `b` as user with full privilege.
 to edit the sudoers file, use the command `visudo` as sudo
 ```
 b       ALL=(ALL:ALL) ALL
 ```

```bash
ziad@ziadpc:/home$ su b
Password: 
b@ziadpc:/home$ sudo ls ..
bin   cdrom  etc     home  lib32  libx32      media  opt   root  sbin  srv	 sys  usr
boot  dev    export  lib   lib64  lost+found  mnt    proc  run	 snap  swapfile  tmp  var
b@ziadpc:/home$ 
```
	


#### **2- Gives account c the privileges of account d on some commands**

we will choose the `ls` command.

```bash
ziad@ziadpc:/home$ ls
total 20K
drwxr-x---  2 a    a    4.0K أبر 14 19:45 a
drwxr-x---  2 b    g2   4.0K أبر 14 23:23 b
drwxr-x---  2 c    c    4.0K أبر 14 19:44 c
drwxr-x---  2 d    d    4.0K أبر 14 19:45 d
drwxr-x--- 41 ziad ziad 4.0K أبر 14 22:30 ziad
```
as shown listing directory d can't be done from any user unless user `d`, so we will make when user `c` users the command `sudo` lists the content of d only without any other root privileges.

to do so, edit the sudoers file by visudo utility and add the following in the user privilege section 
follow this rule 
>**who where = (as_whom) what**

who is user `c`, where is all hosts `ALL`, as whom is `d:d`, and the actions are only `ls` which is `/usr/bin/ls `, then the line will be
```
c       ALL=(d:d) /usr/bin/ls
```


```bash
ziad@ziadpc:/home$ su c
Password: 
$ ls -ll
total 20
drwxr-x---  2 a    a    4096 أبر 14 19:45 a
drwxr-x---  2 b    g2   4096 أبر 14 23:38 b
drwxr-x---  2 c    c    4096 أبر 14 19:44 c
drwxr-x---  2 d    d    4096 أبر 14 23:44 d
drwxr-x--- 41 ziad ziad 4096 أبر 14 22:30 ziad
```
**list d with privileges of c**
```bash
$ ls d
ls: cannot open directory 'd': Permission denied
```
**list d as sudo**
```bash
$ sudo ls d
Sorry, user c is not allowed to execute '/usr/bin/ls d' as root on ziadpc.
```
the issue here is because in the sudoers file we give the privileges of d only to c and by default sudo command uses the privileges of the root, hence on checking the sudoers file the sudo command will find that user c hasn't the privileges of the root.
```
c       ALL=(d:d) /usr/bin/ls
```
so we need to specify to sudo, which user does the user `c` will gain his privileges by using the argument  `-u` or `--user`
```bash
$ sudo -u d ls d
file
$ #done
```
check another commands 
```bash
$ sudo -u d mkdir d/aaa
Sorry, user c is not allowed to execute '/usr/bin/mkdir d/aaa' as d on ziadpc.
```
as expected user `c` gain the `privilege` from `d` to use only the command `ls`

#### **3- Creates new group and give the privileges of root to it**

```bash
ziad@ziadpc:/home$ sudo groupadd new_sudo
ziad@ziadpc:/home$ sudo gpasswd -a c new_sudo
Adding user c to group new_sudo
ziad@ziadpc:/home$ sudo gpasswd -a d new_sudo
Adding user d to group new_sudo
ziad@ziadpc:/home$ sudo visudo #edit the sudoer file
```

the sudoers last section file:
```
# User privilege specification
root    ALL=(ALL:ALL) ALL
# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL
%new_sudo       ALL=(ALL:ALL) ALL

# See sudoers(5) for more information on "@include" directives:
```

now for testing
```bash
ziad@ziadpc:~$ su d
Password: 
$ sudo ls 
a  b  c  d  ziad
$ 
```

#### **4- Try to comment the line that gives member in the sudo group the privileges of the root**

**NOTE: the sudo command will not work after this action, please make sure that you can switch user to `root` without sudo command, to check do the following:**
```bash
ziad@ziadpc:/home$ su root
Password: 
root@ziadpc:/home# 
```
now switch user to any normal user and use visudo to comment this line:
```
# Allow members of group sudo to execute any command
#%sudo  ALL=(ALL:ALL) ALL
%new_sudo       ALL=(ALL:ALL) ALL
```

save and try this:
```bash
ziad@ziadpc:/home$ sudo visudo
ziad is not in the sudoers file.  This incident will be reported.
```
as expected, to fix it switch to the root user and uncomment the line.
```
%sudo   ALL=(ALL:ALL) ALL
```
