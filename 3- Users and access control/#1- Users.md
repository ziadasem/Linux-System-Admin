### 1- Users
#### 1.3- Manage Users
**1.3.1- Add and Switch Users**
adding new user can be achieved through different methods, such as:

 - useradd command
 - adduser command
 - ubuntu settings
 
 1) **useradd**
  ```
  useradd [options] <username>
```
for example:
```
ziad@ziadpc:/home$ sudo useradd ziad2
```
useradd is a low level utility for adding new users, it creates new account with minimal features, for example:
 - no home directory
 - no metadata
 - no password
 - no terminal configuration
 - can't be recognized by Ubuntu GUI
![output](https://i.ibb.co/ggzCJ0P/Screenshot-from-2024-04-13-23-13-15.png)

on switching accounts in Ubuntu you will not find `ziad2`.

	**Switch to ziad2**
	to switch to ziad2 you should give this account a password or to switch to it as a root user (i.e. using sudo)
	
```
ziad@ziadpc:/home$ sudo su ziad2
$ ls
ziad
$ cd ..
$ 
```

![su to ziad2](https://i.ibb.co/mzwW3Pq/Screenshot-from-2024-04-13-23-21-33.png)

as we can see; ziad2 terminal isn't visually formatted as ziad terminal, since ziad account has rc-files

 2) **adduser**
 
 ```
 adduser  [options]  [--home  DIR]  [--shell  SHELL] [--no-create-home] [--uid ID] [--firstuid ID] [--lastuid ID] [--
 ingroup GROUP |--gid ID] [--disabled-password]
  [--disabled login] [--gecos GECOS] [--add_extra_groups] [--encrypt-home] username
```

provide a friendly utility to create a new user with all of its necessary features for example; home directory, metadata, name, password, ..etc.
![adduser](https://i.ibb.co/CtpC0cv/Screenshot-from-2024-04-13-23-27-01.png)

we can see a home directory is created for ziad3 and can be seen by Ubuntu GUI.
switch to ziad3 and type its given password.

![ziad3](https://i.ibb.co/MBh3GyS/Screenshot-from-2024-04-13-23-29-06.png)

as we can see ziad3 terminal is visually formatted.

**1.3.2- Delete  Users**

    sudo userdel <username>
delete user account without its directories 

    sudo userdel -r <username>
   
delete user account and  its directories 
```
ziad@ziadpc:/home$ sudo userdel ziad2
[sudo] password for ziad: 
ziad@ziadpc:/home$ su ziad2
su: user ziad2 does not exist or the user entry does not contain all the required fields
ziad@ziadpc:/home$ sudo userdel -r ziad3 
userdel: ziad3 mail spool (/var/mail/ziad3) not found
ziad@ziadpc:/home$ ls
total 4.0K
drwxr-x--- 41 ziad ziad 4.0K Apr 13 23:25 ziad
ziad@ziadpc:/home$ 
```

**1.3.3- Modify  Users**

    sudo usermod [options] <username>
you can use `man usermod` to find what options are provided and what attributes of an account could be modified.
for example you can change username or uid.
```bash
ziad@ziadpc:~$ sudo useradd ziad2
ziad@ziadpc:~$ sudo su ziad2
ziad2@ziadpc:~$ sudo su ziad
ziad@ziadpc:~$ sudo usermod -l ziad22 ziad2
ziad@ziadpc:~$ sudo su ziad2
su: user ziad2 does not exist or the user entry does not contain all the required fields
ziad@ziadpc:~$ sudo su ziad22
ziad22@ziadpc:/home/ziad$ 
``` 

**Change password**
```bash
ziad@ziadpc:~$ sudo passwd ziad22
[sudo] password for ziad: 
New password: 
Retype new password: 
passwd: password updated successfully
```

#### 1.4-  Users Configuration Files
**/etc/shadow** File
Basically, the /etc/shadow file stores secure user account information. All fields are separated by a colon (:) symbol. It contains one entry per line for each user listed in [/etc/passwd file](https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/). Generally, shadow file entry looks as follows:
![output](https://www.cyberciti.biz/faqs/uploaded_images/shadow-file-718705.png)


As with the [/etc/passwd](https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/ "Understanding /etc/passwd File Format"), each field in the shadow file is also separated with “:” colon characters as follows:

1.  **Username** : A valid account name, which exist on the system.
2.  **Password** : Your encrypted password is in hash format. The password should be minimum 15-20 characters long including special characters, digits, lower case alphabetic and more. Usually password format is set to $id$salt$hashed, The $id is the algorithm prefix used On GNU/Linux as follows
    1.  **\$1\$** is MD5
    2.  **\$2a\$** is Blowfish
    3.  **\$2y\$** is Blowfish
    4.  **\$5\$** is SHA-256
    5.  **\$6\$** is SHA-512
    6.  **\$y\$** is yescrypt
3.  **Last password change (lastchanged)** : The date of the last password change, expressed as the number of days since Jan 1, 1970 (Unix time). The value 0 has a special meaning, which is that the user should change her password the next time she will log in the system. An empty field means that password aging features are disabled.
4.  **Minimum** : The minimum number of days required between password changes i.e. the number of days left before the user is allowed to change her password again. An empty field and value 0 mean that there are no minimum password age.
5.  **Maximum** : The maximum number of days the password is valid, after that user is forced to change her password again.
6.  **Warn** : The number of days before password is to expire that user is warned that his/her password must be changed
7.  **Inactive** : The number of days after password expires that account is disabled.
8.  **Expire** : The date of expiration of the account, expressed as the number of days since Jan 1, 1970.

[source](https://www.cyberciti.biz/faq/understanding-etcshadow-file/) 

**/etc/passwd** File

The **/etc/passwd** file stores essential information required during login. In other words, it stores user account information. The /etc/passwd is a plain text file. It contains a list of the system’s accounts, giving for each account some useful information like user ID, group ID, home directory, shell, and more. The /etc/passwd file should have general read permission as many command utilities use it to map user IDs to user names. However, write access to the /etc/passwd must only limit for the superuser/root account.
![o](https://www.cyberciti.biz/media/ssb.images/uploaded_images/passwd-file-791527.png)
1.  **Username**: It is used when user logs in. It should be between 1 and 32 characters in length.
2.  **Password**: An x character indicates that encrypted and salted password is stored in [/etc/shadow file](https://www.cyberciti.biz/faq/understanding-etcshadow-file/ "Understanding /etc/shadow file format on Linux"). Please note that you need to use the passwd command to computes the hash of a password typed at the CLI or to store/update the hash of the password in /etc/shadow file.
3.  **User ID (UID)**: Each user must be assigned a user ID (UID). UID 0 (zero) is reserved for root and UIDs 1-99 are reserved for other predefined accounts. Further UID 100-999 are reserved by system for administrative and system accounts/groups.
4.  **Group ID (GID)**: The primary group ID (stored in /etc/group file)
5.  **User ID Info (GECOS)**: The comment field. It allow you to add extra information about the users such as user’s full name, phone number etc. This field use by finger command.
6.  **Home directory**: The absolute path to the directory the user will be in when they log in. If this directory does not exists then users directory becomes /
7.  **Command/shell**: The absolute path of a command or shell (/bin/bash). Typically, this is a shell. Please note that it does not have to be a shell. For example, sysadmin can use the nologin shell, which acts as a replacement shell for the user accounts. If shell set to /sbin/nologin and the user tries to log in to the Linux system directly, the /sbin/nologin shell closes the connection.
[source](https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/) 
```
statd:x:132:65534::/var/lib/nfs:/usr/sbin/nologin
stunnel4:x:999:999:stunnel service system account:/var/run/stunnel4:/usr/sbin/nologin
dhcpd:x:133:139::/var/run:/usr/sbin/nologin
ziad22:x:1001:1001:ziad,assem,,:/home/ziad2:/bin/bash
```
as you can see system users like dhcpd has shell path of ```sbin/nologin```, but regular users has ```/bin/bash```.

to search for an account you can grep this /etc/passwd file.
