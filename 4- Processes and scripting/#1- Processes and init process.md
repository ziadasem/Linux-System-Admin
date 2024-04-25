### 1- Process

#### 1.3- init process
A program/command when executed, a special instance is provided by the system to the process. This instance consists of all the services/resources that may be utilized by the process under execution.

In Unix-based computer operating systems, init (short for initialization) is the first process started during booting of the operating system. Init is a daemon process that continues running until the system is shut down. It is automatically adopts all orphaned processes and parent of all process (direct parent or indirect parent).
the Init process is started by the kernel during the booting process, Init is typically assigned process identifier 1.

some of init process types :

 - systemV
 - systemd -used by Ubuntu
 
 more details about init process will be introduced in Embedded-Linux Module.
 this is process tree, we can see that every process has a parent, and there is a single process that has no parent and can be parent for the other process which is systemd (the init process)

```bash
ziad@ziadpc:~/sysadmin_playground$ pstree
```

```bash
systemd─┬─ModemManager───2*[{ModemManager}]
        .......
        ├─gdm3─┬─gdm-session-wor─┬─gdm-wayland-ses─┬─gnome-session-b───2*[{gnome-session-b}]
        │      │                 │                 └─2*[{gdm-wayland-ses}]
        │      │                 └─2*[{gdm-session-wor}]
        │      └─2*[{gdm3}]
      ......
        ├─systemd─┬─(sd-pam)
        │         ├─at-spi2-registr───2*[{at-spi2-registr}]
......
        │         ├─gnome-session-b─┬─anydesk───7*[{anydesk}]
        │         │                 ├─at-spi-bus-laun─┬─dbus-daemon
        │         │                 │                 └─3*[{at-spi-bus-laun}]
        │         │                 ├─evolution-alarm───5*[{evolution-alarm}]
        │         │                 ├─gsd-disk-utilit───2*[{gsd-disk-utilit}]
        │         │                 └─3*[{gnome-session-b}]
        │         ├─gnome-session-c───{gnome-session-c}
        │         ├─gnome-shell─┬─Xwayland───9*[{Xwayland}]
        │         │             ├─firefox─┬─Isolated Servic───27*[{Isolated Servic}]
.....
        │         │             │         └─172*[{firefox}]
        │         │             ├─gjs───11*[{gjs}]
        │         │             └─25*[{gnome-shell}]
        │         ├─gnome-shell-cal───5*[{gnome-shell-cal}]
        │         ├─gnome-terminal-─┬─bash───htop
        │         │                 ├─bash───pstree
        │         │                 └─3*[{gnome-terminal-}]
        │         ├─goa-daemon───3*[{goa-daemon}]
....
        │         ├─gsd-xsettings───10*[{gsd-xsettings}]
        │         ├─gvfs-afc-volume───3*[{gvfs-afc-volume}]
        │         ├─gvfs-goa-volume───2*[{gvfs-goa-volume}]
        │         ├─gvfs-gphoto2-vo───2*[{gvfs-gphoto2-vo}]
        │         ├─gvfs-mtp-volume───2*[{gvfs-mtp-volume}]
        │         ├─gvfs-udisks2-vo───3*[{gvfs-udisks2-vo}]
        │         ├─gvfsd─┬─gvfsd-trash───2*[{gvfsd-trash}]
        │         │       └─2*[{gvfsd}]
        │         ├─gvfsd-fuse───5*[{gvfsd-fuse}]
        │         ├─gvfsd-metadata───2*[{gvfsd-metadata}]
        │         ├─ibus-portal───2*[{ibus-portal}]
        │         ├─ibus-x11───2*[{ibus-x11}]
        │         ├─pipewire───{pipewire}
        │         ├─pipewire-media-───{pipewire-media-}
        │         ├─pulseaudio───3*[{pulseaudio}]
        │         ├─sh───ibus-daemon─┬─ibus-dconf───3*[{ibus-dconf}]
        │         │                  ├─ibus-engine-sim───2*[{ibus-engine-sim}]
        │         │                  ├─ibus-extension-───3*[{ibus-extension-}]
....
        ├─systemd-journal
        ├─systemd-logind
        ├─systemd-oomd
        ├─systemd-resolve
        ├─systemd-timesyn───{systemd-timesyn}
        ├─systemd-udevd
        ├─thermald───{thermald}
        ├─udisksd───4*[{udisksd}]
        ├─unattended-upgr───{unattended-upgr}
        ├─upowerd───2*[{upowerd}]
        └─wpa_supplicant
```

#### 1.4- Process Commands

1- `ps`
`ps` displays information about a selection of the active processes.  If you want a repetitive update of the selection and the displayed information, use `top` instead.

```bash
ziad@ziadpc:~/sysadmin_playground$ ps
    PID TTY          TIME CMD
   8425 pts/0    00:00:00 bash
   8860 pts/0    00:00:00 ps
ziad@ziadpc:~/sysadmin_playground$ #full information
ziad@ziadpc:~/sysadmin_playground$ ps -f
UID          PID    PPID  C STIME TTY          TIME CMD
ziad        8425    2722  0 17:20 pts/0    00:00:00 bash
ziad        8898    8425  0 17:29 pts/0    00:00:00 ps -f
ziad@ziadpc:~/sysadmin_playground$ #to show information about a process by its id
ziad@ziadpc:~/sysadmin_playground$ ps -f 1 #ps <process id>
UID          PID    PPID  C STIME TTY      STAT   TIME CMD
root           1       0  0 14:25 ?        Ss     0:03 /sbin/init splash

```
- **UID:** user who executes this process
 - **PID:** process id
 - **PPID:** parent process id
 -   **C**: CPU utilization of process
-   **STIME**: Process start time
-   **TTY**: Terminal type associated with the process
-   **TIME**: CPU time is taken by the process
-   **CMD**: The command that started this process

**Killing a process**
to kill a process use the command `kill`
 `kill` - send a signal to a process, the default signal is The  default signal for kill is TERM (terminate) so it is used to kill process, although it can send another signals
```bash
ziad@ziadpc:~$ kill 8425
```

it didn't kill the terminal, so use `- 9` option

```bash
ziad@ziadpc:~$ kill -9 8425
```

2- `top`
The top program provides a dynamic real-time view of a running system.  It can display system summary information as well as a list
       of processes or threads currently being managed by the Linux kernel.  The types of system summary information shown and the  types,
       order  and  size  of  information  displayed  for processes are all user configurable and that configuration can be made persistent
       across restarts.
```
Tasks: 322 total,   2 running, 319 sleeping,   0 stopped,   1 zombie
%Cpu(s):  2.4 us,  0.9 sy,  0.0 ni, 96.4 id,  0.2 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   7840.6 total,   1170.5 free,   3449.2 used,   3220.9 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   3610.9 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                                                
   1656 ziad      20   0 5601648 280592 133976 R  11.9   3.5   4:11.25 gnome-shell                                                            
   1487 ziad       9 -11 2294024  29348  21516 S   3.3   0.4   1:38.04 pulseaudio                                                             
   2722 ziad      20   0  570172  58896  42240 S   3.0   0.7   2:06.41 gnome-terminal-                                                        
   4698 ziad      20   0   12.4g   1.1g 210448 S   2.6  14.3   7:49.10 firefox                                                                
    169 root     -51   0       0      0      0 S   0.7   0.0   0:07.84 irq/87-AUI1667:00                                                      
   6612 ziad      20   0 2653828 242176  98228 S   0.7   3.0   0:22.86 Isolated Web Co                                                        
   9725 ziad      20   0   22088   4224   3328 R   0.7   0.1   0:00.06 top                                                                    
     41 root      20   0       0      0      0 S   0.3   0.0   0:15.69 ksoftirqd/4                                                            
    107 root       0 -20       0      0      0 I   0.3   0.0   0:05.87 kworker/u17:0-i915_flip                                                
    541 systemd+  20   0   14836   6800   6000 S   0.3   0.1   0:10.44 systemd-oomd                                                           
    817 message+  20   0   11144   6680   4116 S   0.3   0.1   0:03.26 dbus-daemon                                                            
    843 root      20   0 1836644  33800  20372 S   0.3   0.4   1:24.42 snapd                                                                  
   1524 ziad      20   0    9964   6008   4132 S   0.3   0.1   0:01.49 dbus-daemon                                                            
   4265 root      20   0       0      0      0 I   0.3   0.0   0:18.19 kworker/u16:2-events_unbound                                           
   4901 ziad      20   0 2476604 131576  85708 S   0.3   1.6   0:02.29 Privileged Cont                                                        
   5191 ziad      20   0 2586596 177572  94316 S   0.3   2.2   2:53.80 Isolated Web Co                                                        
   6917 ziad      20   0 2620520 201052  93952 S   0.3   2.5   0:22.27 Isolated Web Co                                                        
   9183 root      20   0       0      0      0 I   0.3   0.0   0:00.09 kworker/1:0-mm_percpu_wq                                               
   9511 root      20   0       0      0      0 I   0.3   0.0   0:00.01 kworker/0:0-events                                                     
      1 root      20   0  168284  13388   8352 S   0.0   0.2   0:03.13 systemd                                                                
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.01 kthreadd                                                               
      3 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_gp                                                                 
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_par_gp                                                             
      5 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 slub_flushwq                                                           
      6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 netns                                                                  
      8 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/0:0H-events_highpri                                            
     11 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 mm_percpu_wq                                                           
     12 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_kthread                                                      
     13 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_rude_kthread                                                 
     14 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_trac
```

more [commands](https://www.geeksforgeeks.org/processes-in-linuxunix/).


### 1- Process

#### 1.3- init process
A program/command when executed, a special instance is provided by the system to the process. This instance consists of all the services/resources that may be utilized by the process under execution.

In Unix-based computer operating systems, init (short for initialization) is the first process started during booting of the operating system. Init is a daemon process that continues running until the system is shut down. It is automatically adopts all orphaned processes and parent of all process (direct parent or indirect parent).
the Init process is started by the kernel during the booting process, Init is typically assigned process identifier 1.

some of init process types :

 - systemV
 - systemd -used by Ubuntu
 
 more details about init process will be introduced in Embedded-Linux Module.
 this is process tree, we can see that every process has a parent, and there is a single process that has no parent and can be parent for the other process which is systemd (the init process)

```bash
ziad@ziadpc:~/sysadmin_playground$ pstree
```

```bash
systemd─┬─ModemManager───2*[{ModemManager}]
        .......
        ├─gdm3─┬─gdm-session-wor─┬─gdm-wayland-ses─┬─gnome-session-b───2*[{gnome-session-b}]
        │      │                 │                 └─2*[{gdm-wayland-ses}]
        │      │                 └─2*[{gdm-session-wor}]
        │      └─2*[{gdm3}]
      ......
        ├─systemd─┬─(sd-pam)
        │         ├─at-spi2-registr───2*[{at-spi2-registr}]
......
        │         ├─gnome-session-b─┬─anydesk───7*[{anydesk}]
        │         │                 ├─at-spi-bus-laun─┬─dbus-daemon
        │         │                 │                 └─3*[{at-spi-bus-laun}]
        │         │                 ├─evolution-alarm───5*[{evolution-alarm}]
        │         │                 ├─gsd-disk-utilit───2*[{gsd-disk-utilit}]
        │         │                 └─3*[{gnome-session-b}]
        │         ├─gnome-session-c───{gnome-session-c}
        │         ├─gnome-shell─┬─Xwayland───9*[{Xwayland}]
        │         │             ├─firefox─┬─Isolated Servic───27*[{Isolated Servic}]
.....
        │         │             │         └─172*[{firefox}]
        │         │             ├─gjs───11*[{gjs}]
        │         │             └─25*[{gnome-shell}]
        │         ├─gnome-shell-cal───5*[{gnome-shell-cal}]
        │         ├─gnome-terminal-─┬─bash───htop
        │         │                 ├─bash───pstree
        │         │                 └─3*[{gnome-terminal-}]
        │         ├─goa-daemon───3*[{goa-daemon}]
....
        │         ├─gsd-xsettings───10*[{gsd-xsettings}]
        │         ├─gvfs-afc-volume───3*[{gvfs-afc-volume}]
        │         ├─gvfs-goa-volume───2*[{gvfs-goa-volume}]
        │         ├─gvfs-gphoto2-vo───2*[{gvfs-gphoto2-vo}]
        │         ├─gvfs-mtp-volume───2*[{gvfs-mtp-volume}]
        │         ├─gvfs-udisks2-vo───3*[{gvfs-udisks2-vo}]
        │         ├─gvfsd─┬─gvfsd-trash───2*[{gvfsd-trash}]
        │         │       └─2*[{gvfsd}]
        │         ├─gvfsd-fuse───5*[{gvfsd-fuse}]
        │         ├─gvfsd-metadata───2*[{gvfsd-metadata}]
        │         ├─ibus-portal───2*[{ibus-portal}]
        │         ├─ibus-x11───2*[{ibus-x11}]
        │         ├─pipewire───{pipewire}
        │         ├─pipewire-media-───{pipewire-media-}
        │         ├─pulseaudio───3*[{pulseaudio}]
        │         ├─sh───ibus-daemon─┬─ibus-dconf───3*[{ibus-dconf}]
        │         │                  ├─ibus-engine-sim───2*[{ibus-engine-sim}]
        │         │                  ├─ibus-extension-───3*[{ibus-extension-}]
....
        ├─systemd-journal
        ├─systemd-logind
        ├─systemd-oomd
        ├─systemd-resolve
        ├─systemd-timesyn───{systemd-timesyn}
        ├─systemd-udevd
        ├─thermald───{thermald}
        ├─udisksd───4*[{udisksd}]
        ├─unattended-upgr───{unattended-upgr}
        ├─upowerd───2*[{upowerd}]
        └─wpa_supplicant
```

#### 1.4- Process Commands

1- `ps`
`ps` displays information about a selection of the active processes.  If you want a repetitive update of the selection and the displayed information, use `top` instead.

```bash
ziad@ziadpc:~/sysadmin_playground$ ps
    PID TTY          TIME CMD
   8425 pts/0    00:00:00 bash
   8860 pts/0    00:00:00 ps
ziad@ziadpc:~/sysadmin_playground$ #full information
ziad@ziadpc:~/sysadmin_playground$ ps -f
UID          PID    PPID  C STIME TTY          TIME CMD
ziad        8425    2722  0 17:20 pts/0    00:00:00 bash
ziad        8898    8425  0 17:29 pts/0    00:00:00 ps -f
ziad@ziadpc:~/sysadmin_playground$ #to show information about a process by its id
ziad@ziadpc:~/sysadmin_playground$ ps -f 1 #ps <process id>
UID          PID    PPID  C STIME TTY      STAT   TIME CMD
root           1       0  0 14:25 ?        Ss     0:03 /sbin/init splash

```
- **UID:** user who executes this process
 - **PID:** process id
 - **PPID:** parent process id
 -   **C**: CPU utilization of process
-   **STIME**: Process start time
-   **TTY**: Terminal type associated with the process
-   **TIME**: CPU time is taken by the process
-   **CMD**: The command that started this process

**Killing a process**
to kill a process use the command `kill`
 `kill` - send a signal to a process, the default signal is The  default signal for kill is TERM (terminate) so it is used to kill process, although it can send another signals
```bash
ziad@ziadpc:~$ kill 8425
```

it didn't kill the terminal, so use `- 9` option

```bash
ziad@ziadpc:~$ kill -9 8425
```

2- `top`
The top program provides a dynamic real-time view of a running system.  It can display system summary information as well as a list
       of processes or threads currently being managed by the Linux kernel.  The types of system summary information shown and the  types,
       order  and  size  of  information  displayed  for processes are all user configurable and that configuration can be made persistent
       across restarts.
```
Tasks: 322 total,   2 running, 319 sleeping,   0 stopped,   1 zombie
%Cpu(s):  2.4 us,  0.9 sy,  0.0 ni, 96.4 id,  0.2 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   7840.6 total,   1170.5 free,   3449.2 used,   3220.9 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   3610.9 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                                                
   1656 ziad      20   0 5601648 280592 133976 R  11.9   3.5   4:11.25 gnome-shell                                                            
   1487 ziad       9 -11 2294024  29348  21516 S   3.3   0.4   1:38.04 pulseaudio                                                             
   2722 ziad      20   0  570172  58896  42240 S   3.0   0.7   2:06.41 gnome-terminal-                                                        
   4698 ziad      20   0   12.4g   1.1g 210448 S   2.6  14.3   7:49.10 firefox                                                                
    169 root     -51   0       0      0      0 S   0.7   0.0   0:07.84 irq/87-AUI1667:00                                                      
   6612 ziad      20   0 2653828 242176  98228 S   0.7   3.0   0:22.86 Isolated Web Co                                                        
   9725 ziad      20   0   22088   4224   3328 R   0.7   0.1   0:00.06 top                                                                    
     41 root      20   0       0      0      0 S   0.3   0.0   0:15.69 ksoftirqd/4                                                            
    107 root       0 -20       0      0      0 I   0.3   0.0   0:05.87 kworker/u17:0-i915_flip                                                
    541 systemd+  20   0   14836   6800   6000 S   0.3   0.1   0:10.44 systemd-oomd                                                           
    817 message+  20   0   11144   6680   4116 S   0.3   0.1   0:03.26 dbus-daemon                                                            
    843 root      20   0 1836644  33800  20372 S   0.3   0.4   1:24.42 snapd                                                                  
   1524 ziad      20   0    9964   6008   4132 S   0.3   0.1   0:01.49 dbus-daemon                                                            
   4265 root      20   0       0      0      0 I   0.3   0.0   0:18.19 kworker/u16:2-events_unbound                                           
   4901 ziad      20   0 2476604 131576  85708 S   0.3   1.6   0:02.29 Privileged Cont                                                        
   5191 ziad      20   0 2586596 177572  94316 S   0.3   2.2   2:53.80 Isolated Web Co                                                        
   6917 ziad      20   0 2620520 201052  93952 S   0.3   2.5   0:22.27 Isolated Web Co                                                        
   9183 root      20   0       0      0      0 I   0.3   0.0   0:00.09 kworker/1:0-mm_percpu_wq                                               
   9511 root      20   0       0      0      0 I   0.3   0.0   0:00.01 kworker/0:0-events                                                     
      1 root      20   0  168284  13388   8352 S   0.0   0.2   0:03.13 systemd                                                                
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.01 kthreadd                                                               
      3 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_gp                                                                 
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_par_gp                                                             
      5 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 slub_flushwq                                                           
      6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 netns                                                                  
      8 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/0:0H-events_highpri                                            
     11 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 mm_percpu_wq                                                           
     12 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_kthread                                                      
     13 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_rude_kthread                                                 
     14 root      20   0       0      0      0 I   0.0   0.0   0:00.00 rcu_tasks_trac
```

more [commands](https://www.geeksforgeeks.org/processes-in-linuxunix/).


