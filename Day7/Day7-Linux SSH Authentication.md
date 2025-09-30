# 100 Days of DevOps — Day 7
## Exercise: Linux SSH Authentication

## Task
Set up password-less authentication from user thor on the jump host to all app servers through their respective sudo users.

## Solution Steps

### 1. Generate SSH Key on Jump Host
Use ssh-keygen to generate a new RSA key pair.
```bash
thor@jumphost ~$ ssh-keygen -t rsa -b 4096
```
Default path: /home/thor/.ssh/id_rsa
Public key: /home/thor/.ssh/id_rsa.pub
No passphrase set for password-less login.

✅ SSH key generated successfully.

### 2. Copy Public Key to Each App Server
Use ssh-copy-id to copy the public key to the target server.
Note : ssh-copy-id is a helper tool that sets up password-less SSH login. It takes public key from local machine (jump host) and appends it into file : ~/.ssh/authorized_keys on the remote server and also sets permissions to ~/.ssh(700) & ~/.ssh/authorized_keys(600)
Next time you ssh user@host, the remote server will use your private key in local machine (jump host) + public key (remote machine) to authenticate without need to password

```bash
thor@jumphost ~$ ssh-copy-id tony@172.16.238.10
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/thor/.ssh/id_rsa.pub"
The authenticity of host '172.16.238.10 (172.16.238.10)' can't be established.
ED25519 key fingerprint is SHA256:jmGPbs3ff0H+QKiyjcq6FJEWvF+U3vPwZfbpZOoJzpE.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
tony@172.16.238.10's password: 

Number of key(s) added: 1
```

✅ Key copied to remote server.

### 3. Verify Password-less SSH
Test SSH login:
```bash
thor@jumphost ~$ ssh tony@172.16.238.10
```
✅ Password-less login successful.

### 4. Allow Privilege Escalation Without Password
By default, sudo may ask for a password. Update the sudoers file to disable password prompt for user tony.
```bash
[tony@stapp01 ~]$ sudo visudo
Add the line:
tony ALL=(ALL) NOPASSWD:ALL
```
✅ Sudo privileges updated.

### 5. Verify Root Access Without Password
Reconnect to confirm root escalation works without password:
```bash
thor@jumphost ~$ ssh tony@172.16.238.10
[tony@stapp01 ~]$ sudo su -
[root@stapp01 ~]#
```
✅ Root access confirmed without password.

## Final Notes
Password-less SSH is established from thor@jumphost → tony@<app_server>.
User tony can escalate to root without password after sudoers update.