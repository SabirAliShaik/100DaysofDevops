# 100 Days of DevOps — Day 3
## Exercise: Secure Root SSH Access
## Challenge
Disable direct SSH root login on all app servers within the **Stratos Datacenter**.

### Steps Followed
1. **Verify SSH Root Login Access**
   ```bash
	thor@jumphost ~$ ssh root@172.16.238.10
	root@172.16.238.10's password: 
	Last login: Wed Sep 24 15:46:28 2025
	[root@stapp01 ~]#
	```
	✅ Root login was successful.
2. **Check SSH Daemon Config file for root login permit**
	```bash
	[root@stapp01 ~]# cat /etc/ssh/sshd_config | grep -i "permitroot"
	PermitRootLogin yes
	```
	🔎 Root login was explicitly allowed.
3. **Disable Root Login Access**
	Edit the config file and change directive as no:
	```bash
	[root@stapp01 ~]# nano /etc/ssh/sshd_config
	[root@stapp01 ~]# cat /etc/ssh/sshd_config | grep -i "permitroot"
	PermitRootLogin no
	```
4. **Restart SSH Service**
	On CentOS 9, the service name is sshd:
	```bash
	[root@stapp01 ~]# systemctl restart sshd
	[root@stapp01 ~]# systemctl enable sshd
	[root@stapp01 ~]# systemctl status sshd
		● sshd.service - OpenSSH server daemon
     	Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; preset: enabled)
     	Active: active (running) since Wed 2025-09-24 16:08:12 UTC; 47s ago
	```
5. **Verify Root Login is Disabled**
	```bash
	thor@jumphost ~$ ssh root@172.16.238.10
	root@172.16.238.10's password: 
	Permission denied, please try again.
	```
	❌ Root SSH login is now denied.

## Note: Since the root password was not set initially, I had to configure it first using the following command:
	```bash
	$sudo passwd root
	New Password : 
	Retype Password :
	```

