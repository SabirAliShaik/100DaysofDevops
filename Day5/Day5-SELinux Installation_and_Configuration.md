# 100 Days of DevOps — Day 5
## Exercise: SELinux Installation and Configuration

## Task
Following a security audit, the **xFusionCorp Industries** security team has decided to enhance application and server security using **SELinux**.  
For testing purposes, the following requirements were set for **App Server 2** in the Stratos Datacenter:

1. Install the required SELinux packages.  
2. Permanently disable SELinux for the time being (to be re-enabled later after necessary configuration).  
3. Do not reboot the server immediately (a scheduled maintenance reboot is already planned for tonight).  
4. Ignore the current status of SELinux via the command line; the final status **after reboot** should be **disabled**.  


## Solution Steps

### 1. Verify if SELinux is installed
```bash
[root@stapp02 ~]# selinux
-bash: selinux: command not found
```
SELinux was not installed.

### 2. Install SELinux packages
```bash
[root@stapp02 ~]# dnf install selinux-policy selinux-policy-targeted policycoreutils policycoreutils-python-utils
Last metadata expiration check: 0:15:33 ago on Fri Sep 26 16:33:45 2025.
Dependencies resolved.
==========================================================================================================
 Package                               Architecture    Version                   Repository          Size
==========================================================================================================
Installing:
 policycoreutils                       x86_64          3.6-3.el9                 baseos             239 k
 policycoreutils-python-utils          noarch          3.6-3.el9                 appstream           76 k
 selinux-policy                        noarch          38.1.65-1.el9             baseos              42 k
 selinux-policy-targeted               noarch          38.1.65-1.el9             baseos             6.9 M
Installing dependencies:
 checkpolicy                           x86_64          3.6-1.el9                 appstream          353 k
 diffutils                             x86_64          3.7-12.el9                baseos             397 k
 libselinux-utils                      x86_64          3.6-3.el9                 baseos             190 k
 python3-audit                         x86_64          3.1.5-7.el9               appstream           79 k
 python3-distro                        noarch          1.5.0-7.el9               appstream           37 k
 python3-libselinux                    x86_64          3.6-3.el9                 appstream          188 k
 python3-libsemanage                   x86_64          3.6-5.el9                 appstream           79 k
 python3-policycoreutils               noarch          3.6-3.el9                 appstream          2.1 M
 python3-setools                       x86_64          4.4.4-1.el9               baseos             605 k
 python3-setuptools                    noarch          53.0.0-15.el9             baseos             936 k
 rpm-plugin-selinux                    x86_64          4.16.1.3-38.el9           baseos              16 k

Transaction Summary
==========================================================================================================
Install  15 Packages

Total download size: 12 M
Installed size: 36 M
Is this ok [y/N]: y
Downloading Packages:
(1/15): libselinux-utils-3.6-3.el9.x86_64.rpm                             434 kB/s | 190 kB     00:00    
(2/15): policycoreutils-3.6-3.el9.x86_64.rpm                              542 kB/s | 239 kB     00:00    
(3/15): diffutils-3.7-12.el9.x86_64.rpm                                   841 kB/s | 397 kB     00:00    
(4/15): rpm-plugin-selinux-4.16.1.3-38.el9.x86_64.rpm                      63 kB/s |  16 kB     00:00    
(5/15): python3-setools-4.4.4-1.el9.x86_64.rpm                            1.3 MB/s | 605 kB     00:00    
(6/15): python3-setuptools-53.0.0-15.el9.noarch.rpm                       1.8 MB/s | 936 kB     00:00    
(7/15): selinux-policy-38.1.65-1.el9.noarch.rpm                           134 kB/s |  42 kB     00:00    
(8/15): policycoreutils-python-utils-3.6-3.el9.noarch.rpm                 192 kB/s |  76 kB     00:00    
(9/15): selinux-policy-targeted-38.1.65-1.el9.noarch.rpm                   11 MB/s | 6.9 MB     00:00    
(10/15): checkpolicy-3.6-1.el9.x86_64.rpm                                 587 kB/s | 353 kB     00:00    
(11/15): python3-audit-3.1.5-7.el9.x86_64.rpm                             542 kB/s |  79 kB     00:00    
(12/15): python3-distro-1.5.0-7.el9.noarch.rpm                            380 kB/s |  37 kB     00:00    
(13/15): python3-libsemanage-3.6-5.el9.x86_64.rpm                         551 kB/s |  79 kB     00:00    
(14/15): python3-libselinux-3.6-3.el9.x86_64.rpm                          974 kB/s | 188 kB     00:00    
(15/15): python3-policycoreutils-3.6-3.el9.noarch.rpm                     1.6 MB/s | 2.1 MB     00:01    
----------------------------------------------------------------------------------------------------------
Total                                                                     670 kB/s |  12 MB     00:18     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Running scriptlet: selinux-policy-targeted-38.1.65-1.el9.noarch                                     1/1 
  Preparing        :                                                                                  1/1 
  Installing       : python3-libselinux-3.6-3.el9.x86_64                                             1/15 
  Installing       : python3-setuptools-53.0.0-15.el9.noarch                                         2/15 
  Installing       : python3-setools-4.4.4-1.el9.x86_64                                              3/15 
  Installing       : python3-distro-1.5.0-7.el9.noarch                                               4/15 
  Installing       : python3-libsemanage-3.6-5.el9.x86_64                                            5/15 
  Installing       : python3-audit-3.1.5-7.el9.x86_64                                                6/15 
  Installing       : checkpolicy-3.6-1.el9.x86_64                                                    7/15 
  Installing       : libselinux-utils-3.6-3.el9.x86_64                                               8/15 
  Installing       : diffutils-3.7-12.el9.x86_64                                                     9/15 
  Installing       : policycoreutils-3.6-3.el9.x86_64                                               10/15 
  Running scriptlet: policycoreutils-3.6-3.el9.x86_64                                               10/15 
Created symlink /etc/systemd/system/sysinit.target.wants/selinux-autorelabel-mark.service → /usr/lib/systemd/system/selinux-autorelabel-mark.service.

  Installing       : selinux-policy-38.1.65-1.el9.noarch                                            11/15 
  Running scriptlet: selinux-policy-38.1.65-1.el9.noarch                                            11/15 
  Running scriptlet: selinux-policy-targeted-38.1.65-1.el9.noarch                                   12/15 
  Installing       : selinux-policy-targeted-38.1.65-1.el9.noarch                                   12/15 
  Running scriptlet: selinux-policy-targeted-38.1.65-1.el9.noarch                                   12/15 
  Installing       : python3-policycoreutils-3.6-3.el9.noarch                                       13/15 
  Installing       : policycoreutils-python-utils-3.6-3.el9.noarch                                  14/15 
  Installing       : rpm-plugin-selinux-4.16.1.3-38.el9.x86_64                                      15/15 
  Running scriptlet: selinux-policy-targeted-38.1.65-1.el9.noarch                                   15/15 
  Running scriptlet: rpm-plugin-selinux-4.16.1.3-38.el9.x86_64                                      15/15 
  Verifying        : diffutils-3.7-12.el9.x86_64                                                     1/15 
  Verifying        : libselinux-utils-3.6-3.el9.x86_64                                               2/15 
  Verifying        : policycoreutils-3.6-3.el9.x86_64                                                3/15 
  Verifying        : python3-setools-4.4.4-1.el9.x86_64                                              4/15 
  Verifying        : python3-setuptools-53.0.0-15.el9.noarch                                         5/15 
  Verifying        : rpm-plugin-selinux-4.16.1.3-38.el9.x86_64                                       6/15 
  Verifying        : selinux-policy-38.1.65-1.el9.noarch                                             7/15 
  Verifying        : selinux-policy-targeted-38.1.65-1.el9.noarch                                    8/15 
  Verifying        : checkpolicy-3.6-1.el9.x86_64                                                    9/15 
  Verifying        : policycoreutils-python-utils-3.6-3.el9.noarch                                  10/15 
  Verifying        : python3-audit-3.1.5-7.el9.x86_64                                               11/15 
  Verifying        : python3-distro-1.5.0-7.el9.noarch                                              12/15 
  Verifying        : python3-libselinux-3.6-3.el9.x86_64                                            13/15 
  Verifying        : python3-libsemanage-3.6-5.el9.x86_64                                           14/15 
  Verifying        : python3-policycoreutils-3.6-3.el9.noarch                                       15/15 

Installed:
  checkpolicy-3.6-1.el9.x86_64                           diffutils-3.7-12.el9.x86_64                      
  libselinux-utils-3.6-3.el9.x86_64                      policycoreutils-3.6-3.el9.x86_64                 
  policycoreutils-python-utils-3.6-3.el9.noarch          python3-audit-3.1.5-7.el9.x86_64                 
  python3-distro-1.5.0-7.el9.noarch                      python3-libselinux-3.6-3.el9.x86_64              
  python3-libsemanage-3.6-5.el9.x86_64                   python3-policycoreutils-3.6-3.el9.noarch         
  python3-setools-4.4.4-1.el9.x86_64                     python3-setuptools-53.0.0-15.el9.noarch          
  rpm-plugin-selinux-4.16.1.3-38.el9.x86_64              selinux-policy-38.1.65-1.el9.noarch              
  selinux-policy-targeted-38.1.65-1.el9.noarch          
Complete!
```
15 packages were installed successfully, including:
selinux-policy, selinux-policy-targeted, policycoreutils, policycoreutils-python-utils
Dependencies like checkpolicy, libselinux-utils, python3-
✅ Installation completed successfully.

### 3. Check current SELinux configuration
```bash
[root@stapp02 selinux]# cat /etc/selinux/config | grep SELINUX=
SELINUX=enforcing
```
Current SELinux mode is enforcing.
### 4. Permanently disable SELinux
Edit the config file:
```bash
[root@stapp02 selinux]# vi /etc/selinux/config
[root@stapp02 selinux]# cat /etc/selinux/config | grep SELINUX=
SELINUX=disabled
```
✅ SELinux set to disabled (permanent after next reboot).
Current SELinux mode is enforcing.
- Reboot the server to fully disable SElinux
---
### 5. Verify current SELinux mode (before reboot)
Edit the config file:
```bash
[root@stapp02 selinux]# getenforce
Disabled
```
Since the config was updated, SELinux reports Disabled.

Attempting to switch modes has no effect:
```bash
[root@stapp02 selinux]# setenforce 0
setenforce: SELinux is disabled
```
### Final Notes:
SELinux packages have been installed successfully.
SELinux has been permanently disabled in /etc/selinux/config.
Current mode shows Disabled, and after the scheduled reboot, SELinux will remain disabled.
