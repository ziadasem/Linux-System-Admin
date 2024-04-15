### 4-  Permissions
#### 4.1- Simple Permissions

**1- using chmod and chown commands**
```bash
ziad@ziadpc:~/sysadmin_playground$ touch file
ziad@ziadpc:~/sysadmin_playground$ ls -ll
total 0
-rw-rw-r-- 1 ziad ziad 0 أبر 15 01:42 file
```
**add execute permission to other** 
```bash 
ziad@ziadpc:~/sysadmin_playground$ without modifying other permissions
ziad@ziadpc:~/sysadmin_playground$ sudo chmod o+x file
[sudo] password for ziad: 
ziad@ziadpc:~/sysadmin_playground$ ls
total 0
-rw-rw-r-x 1 ziad ziad 0 أبر 15 01:42 file
ziad@ziadpc:~/sysadmin_playground$ 
```

**removing read and write permissions from the owner**
```bash
ziad@ziadpc:~/sysadmin_playground$ cat file
Some Text
ziad@ziadpc:~/sysadmin_playground$ #remove read permission
ziad@ziadpc:~/sysadmin_playground$ chmod u-r file
ziad@ziadpc:~/sysadmin_playground$ cat file
cat: file: Permission denied
ziad@ziadpc:~/sysadmin_playground$ #remove write permission
ziad@ziadpc:~/sysadmin_playground$ chmod u-w file
ziad@ziadpc:~/sysadmin_playground$ echo "new text" >>file
bash: file: Permission denied
```
**switch to another user to use the privileges of 'other'**
```bash
ziad@ziadpc:~/sysadmin_playground$ su d
Password: 
$ ls
file
$ cat file
Some Text
$ echo "hello" >> file
sh: 3: cannot create file: Permission denied
$ ls -ll
total 4
----rw-r-
```

use chown command

```bash
ziad@ziadpc:~/sysadmin_playground$ sudo chmod 077 file
ziad@ziadpc:~/sysadmin_playground$ cat file
cat: file: Permission denied
ziad@ziadpc:~/sysadmin_playground$ echo "print me" >> file
bash: file: Permission denied
ziad@ziadpc:~/sysadmin_playground$ ./file
bash: ./file: Permission denied
```

**no available permissions for the owner** 
```bash
ziad@ziadpc:~/sysadmin_playground$ #change ownership of the file and make the current user uses privileges of 'other'
ziad@ziadpc:~/sysadmin_playground$ sudo chown d:d file
ziad@ziadpc:~/sysadmin_playground$ cat file
Some Text
ziad@ziadpc:~/sysadmin_playground$ echo "print me" >> file
ziad@ziadpc:~/sysadmin_playground$ ./file
./file: line 1: Some: command not found
Warning: unknown mime-type for "me" -- using "application/octet-stream"
Error: no such file "me"
ziad@ziadpc:~/sysadmin_playground$ 
```
**2- use umask command**

```bash
ziad@ziadpc:~/sysadmin_playground$ umask
0002
ziad@ziadpc:~/sysadmin_playground$ umask 0222
ziad@ziadpc:~/sysadmin_playground$ touch new_file_rrr
ziad@ziadpc:~/sysadmin_playground$ ls -ll
total 0
-r--r--r-- 1 ziad ziad 0 أبر 15 01:52 new_file_rrr
ziad@ziadpc:~/sysadmin_playground$ mkdir new_dir_rxrxrx
ziad@ziadpc:~/sysadmin_playground$ ls -lh
total 4.0K
dr-xr-xr-x 2 ziad ziad 4.0K أبر 15 01:53 new_dir_rxrxrx
-r--r--r-- 1 ziad ziad    0 أبر 15 01:52 new_file_rrr
```

more about permissions from [here](https://www.redhat.com/sysadmin/linux-file-permissions-explained)

#### 4.2- Complex Permissions

4.2.1.  **Set user id**
If you are the owner of an executable file, with the help of SUID permission set, other users will be running the executable with your permission and not theirs.
<br>
for example: the passwd command:

```bash
ziad@ziadpc:~$ top #command to show the processes and who executed it
```

```
    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                                                
   2895 ziad      20   0   12.2g 617348 225012 S  37.3   7.7  47:54.54 firefox                                                                
   3402 ziad      20   0 2632464 211520  94104 S  33.0   2.6  16:00.34 Isolated Web Co                                                        
   1724 ziad      20   0 5975260 318532 136860 S  15.5   4.0  22:22.47 gnome-shell                                                            
   1893 ziad      20   0  323976  11956   7016 S   3.0   0.1   2:45.91 ibus-daemon                                                            
     41 root      20   0       0      0      0 S   2.0   0.0   1:29.67 ksoftirqd/4                                                            
  26850 ziad      20   0   22124   4352   3328 R   1.3   0.1   0:00.60 top    
  ```
we can see that the process `top` is executed by `ziad`, so it is limited to the privileges that the user `ziad` owns, (i.e. it can't edit the file that is owned by the root).

for `passwd` command it will edit the /etc/passwd and /etc/shadow files so this command should be executed by the root or by `sudo` command. but not all users belong to sudoers file so is that mean that the non-sudo users (which form the majority of users) can't change their password ?
the permission of set user id solves this issue, if this permission is given to any execrable **any user will execute this executable as its original owner** so for passwd command, **any user uses it as root**.

```bash
ziad@ziadpc:~$ passwd ziad
Changing password for ziad.
Current password: 
```
here user `ziad` runs passwd command.
if we run `ps aux | grep passwd` that shows the processes and grep for passwd:

```bash
ziad@ziadpc:~$ ps aux | grep passwd
root       27264  0.0  0.0  21228  4352 pts/4    S+   04:27   0:00 passwd ziad
```

we will find that the executor of passwd command is the `root` (the original owner of this command) not the original executor which is `ziad`.

**check the suid permission**

```bash
ziad@ziadpc:~$ ls /bin/passwd
-rwsr-xr-x 1 root root 59K فبر  6 14:54 /bin/passwd
```
letter S in the last field of owner section indicates SUID is set
-rwSr-xr-x means 
 - **owner**: read, write SUID
 - **group** read, execute
 - **other** read, execute
if there is execute permission for the owner and SUID together a lower case letter `s` is used 

-rwsr-xr-x means

 - **owner**: read, write, execute SUID
 - **group** read, execute
 - **other** read, execute

**set the suid permission**

 - to use characters, suid is S in the user 
	 `chmod u+s file`
	 
	 ```bash
	 ziad@ziadpc:~$ touch file
	ziad@ziadpc:~$ ls file
	-rw-rw-r-- 1 ziad ziad 0 أبر 15 04:34 file
	ziad@ziadpc:~$ chmod u+s file
	ziad@ziadpc:~$ ls file
	-rwSrw-r-- 1 ziad ziad 0 أبر 15 04:34 file
	 ```

 - to use octal values, suid is bit number 3   (suid sgid sb)(rwx)(rwx)(rwx)
 ```bash
	ziad@ziadpc:~$ chmod 4766 file
	ziad@ziadpc:~$ ls file
	-rwsrw-rw- 1 ziad ziad 0 أبر 15 04:34 file
 ```

**Note: setting suid to scripts will not work, it will be discussed in the next chapter**

4.2.2.  **Set group id**
same as suid, but it is for groups you can read about it from [here](https://www.slashroot.in/suid-and-sgid-linux-explained-examples)

**check the guid permission**

```bash
ziad@ziadpc:~$ ls 
-rwxr-Sr-x 1 root root 59K فبر  6 14:54 file
```
letter S in the last field of group section indicates SGID is set
-rw-r-Sr-x means 
 - **owner**: read, write
 - **group** read, SGID
 - **other** read, execute
if there is execute permission for the group and SGID together a lower case letter `s` is used 

-rwxr-sr-x means

 - **owner**: read, write, execute
 - **group** read, execute, SGID
 - **other** read, execute

**set the guid permission**

 - to use characters, guid is S in the group 
	 `chmod g+s file`


 - to use octal values, guid is bit number 2   (suid sgid sb)(rwx)(rwx)(rwx)
 ```bash
	ziad@ziadpc:~$ chmod 2766 file
	ziad@ziadpc:~$ ls file
	-rwxrwSrw- 1 ziad ziad 0 أبر 15 04:34 file
 ```

4.2.3.  **Sticky bit**
suppose the following scenario 

```bash
ziad@ziadpc:~$ mkdir shared_dir
ziad@ziadpc:~$ ls
total 112K
drwxrwxr-x 2 ziad ziad 4.0K أبر 15 04:40  shared_dir
ziad@ziadpc:~$ chmod 777 shared_dir/
ziad@ziadpc:~$ touch shared_dir/ziads_file
ziad@ziadpc:~$ ls shared_dir/
total 0
-rw-rw-r-- 1 ziad ziad 0 أبر 15 04:42 ziads_file
```

and another user (`b` for example) renamed the file that is created and owned by user `ziad`

```bash
b@ziadpc:/home/ziad/aaa$ cd /home/ziad/shared_dir
b@ziadpc:/home/ziad/shared_dir$ ls
ziads_file
b@ziadpc:/home/ziad/shared_dir$ mv ziads_file bs_file
b@ziadpc:/home/ziad/shared_dir$ ls
bs_file
b@ziadpc:/home/ziad/shared_dir$ 
```

to prevent any user to modify files not owned by him, the special permission of **Sticky bit is used**, if sticky bit is set in a directory it will restricts modifying files to its owners only.
it is clear that sticky bit is used for directories only.

**check the Sticky bit permission**

```bash
ziad@ziadpc:~$ ls 
drwxrwxrwt 2 ziad ziad 4.0K أبر 15 04:44  shared_dir
```
letter t in the last field of other section indicates sticky bit is set
-rw-r--r-T means 
 - **owner**: read, write
 - **group** read
 - **other** read, Sticky bit
if there is execute permission for the other and sticky bit together a lower case letter `t` is used 

-rwxr--r-t means

 - **owner**: read, write, execute
 - **group** read
 - **other** read, execute, sticky bit

**set the sticky bit permission**

 - to use characters, sticky bit is t in the others 
	 `chmod o+t shared_dir`


 - to use octal values, sticky_bit is bit number 1   (suid sgid sb)(rwx)(rwx)(rwx)

**testing the sticky bit permission**

```bash
ziad@ziadpc:~$ chmod o+t shared_dir
ziad@ziadpc:~$ ls
total 108K
drwxrwxrwt 2 ziad ziad 4.0K أبر 15 04:44  shared_dir
ziad@ziadpc:~$ ls shared_dir/
total 0
-rw-rw-r-- 1 ziad ziad 0 أبر 15 04:42 bs_file
ziad@ziadpc:~$ mv shared_dir/bs_file  shared_dir/ziads_file_again
ziad@ziadpc:~$ ls shared_dir/
total 0
-rw-rw-r-- 1 ziad ziad 0 أبر 15 04:42 ziads_file_again
```

**user b:**
```bash
b@ziadpc:/home/ziad/shared_dir$ mv ziads_file_again bs_file_again
mv: cannot move 'ziads_file_again' to 'bs_file_again': Operation not permitted
b@ziadpc:/home/ziad/shared_dir$ ls
ziads_file_again
b@ziadpc:/home/ziad/shared_dir$ 
```

