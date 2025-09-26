# 100 Days of DevOps — Day 3
## Exercise: Script Execution Permissions

## Task
Your task is to grant executable permissions to the `/tmp/xfusioncorp.sh` script on **App Server 2**. 
Additionally, ensure that all users have the capability to execute it.


## Solution Steps

### 1. Login to root user and verify file permission
```bash
[root@stapp01 tmp]# ls -l
total 16
drwx------ 3 root root 4096 Sep 26 13:58 systemd-private-99e97dd587034cd682640d4177861fcd-dbus-broker.service-uvqUEI
drwx------ 3 root root 4096 Sep 26 13:59 systemd-private-99e97dd587034cd682640d4177861fcd-systemd-hostnamed.service-OnUrxH
drwx------ 3 root root 4096 Sep 26 13:58 systemd-private-99e97dd587034cd682640d4177861fcd-systemd-logind.service-6RRkJR
---------- 1 root root   40 Sep 26 13:58 xfusioncorp.sh
```

This script file has **no permissions at all**.

---

### 2. Grant execute permissions for all users (owner, group, and others)
Let us try to grant execute permissions using the **chmod** command with `a+x`  
(`a = all users`, `x = execute permission`):

```bash
[root@stapp01 tmp]# chmod a+x xfusioncorp.sh
[root@stapp01 tmp]# ls -l
total 12
drwx------ 3 root root 4096 Sep 26 13:58 systemd-private-99e97dd587034cd682640d4177861fcd-dbus-broker.service-uvqUEI
drwx------ 3 root root 4096 Sep 26 13:58 systemd-private-99e97dd587034cd682640d4177861fcd-systemd-logind.service-6RRkJR
---x--x--x 1 root root   40 Sep 26 13:58 xfusioncorp.sh
```

Here we can observe that **execute (x)** permission is granted for all users.  
But since there is **no read permission**, the shell won’t be able to read the script file to run it.  

➡️ We will see `Permission denied` error when we execute the script.  
For shell scripts, the minimum working permission is usually **read + execute**.

---

### 3. Now grant the read & execute permissions to all users using `chmod 755`
```bash
[root@stapp01 ~]# chmod 755 /tmp/xfusioncorp.sh
[root@stapp01 ~]# cd /tmp/
[root@stapp01 tmp]# ls -l
total 12
drwx------ 3 root root 4096 Sep 26 14:16 systemd-private-2b1673151d5b42cebe62828a4d89d10e-dbus-broker.service-ohLssz
drwx------ 3 root root 4096 Sep 26 14:16 systemd-private-2b1673151d5b42cebe62828a4d89d10e-systemd-logind.service-yf7Ops
-rwxr-xr-x 1 root root   40 Sep 26 14:16 xfusioncorp.sh
```

---

### Permissions Breakdown:
- `4` → read  
- `2` → write  
- `1` → execute  

So, `755` means:
- Owner: **rwx**  
- Group: **r-x**  
- Others: **r-x**  

This command gives **read, write, execute** permissions to the user, and **read + execute** permissions to group & others.

Alternatively, you can also use:
```bash
chmod a+rx xfusioncorp.sh
```
This command grants **read & execute** permissions to all users in the system.

---
