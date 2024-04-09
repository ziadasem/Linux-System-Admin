### 3- Links
#### 3.1 Hard Links
Every file on the Linux filesystem starts with a single hard link. The _link_ is between the filename and the actual data stored on the filesystem. so creating new hard-link means you create a new reference equals to original address.
`ln (original file path) (new file path)`.

in the following example, I will create a hard-link to a file and edit the hard link, it should modify the original file.
 
```bash
ziad@ziadpc:~/sysadmin_playground/dir5$ ls
total 0
ziad@ziadpc:~/sysadmin_playground/dir5$ ls ../dir4
total 4.0K
-rw-rw-r-- 1 ziad ziad 7 Apr 10 01:31 text_file
ziad@ziadpc:~/sysadmin_playground/dir5$ cat ../dir4/text_file 
hello 
ziad@ziadpc:~/sysadmin_playground/dir5$ ln ../dir4/text_file hardlink_file
ziad@ziadpc:~/sysadmin_playground/dir5$ ls
total 4.0K
-rw-rw-r-- 2 ziad ziad 7 Apr 10 01:31 hardlink_file
ziad@ziadpc:~/sysadmin_playground/dir5$ echo "append new text" >> hardlink_file 
ziad@ziadpc:~/sysadmin_playground/dir5$ cat ../dir4/text_file 
hello 
append new text
ziad@ziadpc:~/sysadmin_playground/dir5$ 
```
as expected; the file in ../dir4 is modified by modifying the file in dir5, since it is hard link to it.

**Number of hard links:**
```ziad@ziadpc:~/sysadmin_playground/dir5$ ls -lh
total 4.0K
-rw-rw-r-- 2 ziad ziad 23 Apr 10 01:32 hardlink_file
```
after doing ls -lh we see (2) before the name of the owner of the file, this (2) indicates the number of hard links to this data.

**Deleting a hard link:**

what about deleting file in ../dir4/text_file, does the hard link will get affected?
if a hard link is another pointer to same data, if the same pointer is deleted the data should still exists since it is pointed by another pointer.

```bash
ziad@ziadpc:~/sysadmin_playground/dir5$ rm ../dir4/text_file 
ziad@ziadpc:~/sysadmin_playground/dir5$ ls
total 4.0K
-rw-rw-r-- 1 ziad ziad 23 Apr 10 01:32 hardlink_file
ziad@ziadpc:~/sysadmin_playground/dir5$ cat hardlink_file 
hello 
append new text
ziad@ziadpc:~/sysadmin_playground/dir5$ 
``` 
as expected the file still exists and the count is decremented to (1)

**Hard limits**
 - they can only be created for regular files (not directories or special files). 
 - a hard link cannot span multiple filesystems. They only work when the new hard link exists on the same filesystem as the original.
 
 #### 3.2 Soft/symbolic links
 
 Commonly referred to as _symbolic links_, **soft links link together non-regular and regular files**. They can also span multiple filesystems. By definition, a soft link is not a standard file, but a special file that points to an existing file. Let's look at how to create a soft link. 
 it can be considered as a pointer to pointer.
 ``ln -s (file path you want to point to) (new file path)``

in the following example I create a symbolic link that points to an existing file and edited the original file through the symbolic link.

```bash
ziad@ziadpc:~/sysadmin_playground/dir5$ cat ../dir4/original_file 
hello there!
ziad@ziadpc:~/sysadmin_playground/dir5$ ln -s ../dir4/original_file s_link
ziad@ziadpc:~/sysadmin_playground/dir5$ ls -lh
total 0
lrwxrwxrwx 1 ziad ziad 21 Apr 10 01:44 s_link -> ../dir4/original_file
ziad@ziadpc:~/sysadmin_playground/dir5$ echo "new lines" >> s_link
ziad@ziadpc:~/sysadmin_playground/dir5$ cat s_link 
hello there!
new lines
ziad@ziadpc:~/sysadmin_playground/dir5$ cat ../dir4/original_file 
hello there!
new lines
ziad@ziadpc:~/sysadmin_playground/dir5$ 
```

**Deleting in soft link:**
what will happen to a soft link if the original file is deleted?
since soft link acts as pointer to a data and this data is freed, then it will be dangling pointer.
```bash
ziad@ziadpc:~/sysadmin_playground/dir5$ rm ../dir4/original_file 
ziad@ziadpc:~/sysadmin_playground/dir5$ ls -lh
total 0
lrwxrwxrwx 1 ziad ziad 21 Apr 10 01:44 s_link -> ../dir4/original_file
ziad@ziadpc:~/sysadmin_playground/dir5$ cat s_link 
cat: s_link: No such file or directory
ziad@ziadpc:~/sysadmin_playground/dir5$ 
```
after deleting the original file, the symbolic link became broken link which behave as expected.

