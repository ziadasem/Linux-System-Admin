### 2- Groups

> -   Group: A group is a collection of users who can share files and other system resources
>-   Primary Group: Specifies a group that the operating system assigns to files that the user creates. Naturally, each user must belong to a primary group
>-   Secondary Group: Specifies one or more groups to which a user also belongs. In addition, users can belong to up to 15 secondary groups

#### 2.2 Groups Configuration files
**1- /etc/group**

	The `/etc/group` file is world-readable and contains a list of groups, each on a separate line. Each line is a four field, colon delimited list including the following information:

-   _Group name_ — The name of the group. Used by various utility programs as a human-readable identifier for the group.
    
-   _Group password_ — If set, this allows users that are not part of the group to join the group by using the `newgrp` command and typing the password stored here. If a lower case `x` is in this field, then shadow group passwords are being used.
    
-   _Group ID_ (_GID_) — The numerical equivalent of the group name. It is used by the operating system and applications when determining access privileges.
    
-   _Member list_ — A comma delimited list of the users belonging to the group.

**we can add users to a group by modifying this file as shown in the next section.**

**2- /etc/gshadow**

The `/etc/gshadow` file is readable only by the root user and contains an encrypted password for each group, as well as group membership and administrator information. Just as in the `/etc/group` file, each group's information is on a separate line. Each of these lines is a colon delimited list including the following information:

-   _Group name_ — The name of the group. Used by various utility programs as a human-readable identifier for the group.
    
-   _Encrypted password_ — The encrypted password for the group. If set, non-members of the group can join the group by typing the password for that group using the `newgrp` command. If the value of this field is `!`, then no user is allowed to access the group using the `newgrp` command. A value of `!!` is treated the same as a value of `!` — however, it also indicates that a password has never been set before. If the value is null, only group members can log into the group.
    
-   _Group administrators_ — Group members listed here (in a comma delimited list) can add or remove group members using the `gpasswd` command.
    
-   _Group members_ — Group members listed here (in a comma delimited list) are regular, non-administrative members of the group.
