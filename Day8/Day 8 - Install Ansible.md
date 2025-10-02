# 100 Days of DevOps — Day 8
## Exercise: Install Ansible

## Task
During the weekly meeting, the Nautilus DevOps team discussed automation and configuration management solutions. After considering several options, the team decided to use **Ansible** because of its simple setup and minimal prerequisites.  

They plan to use the **jump host as the Ansible controller** to test different tasks on the rest of the servers.  

**Requirement:**  
Install **Ansible version 4.7.0** on the jump host using **pip3 only**. The Ansible binary must be **globally accessible**, so all users on the system should be able to run Ansible commands.

## Solution Steps

### 1. Verify prerequisites
Check if Python3 and pip3 are installed.
```bash
thor@jumphost ~$ python3 --version
Python 3.9.18

thor@jumphost ~$ pip3 --version
pip 24.0 from /usr/local/lib/python3.9/site-packages/pip (python 3.9)
```

✅ Since both are already installed, no additional setup is needed.

### 2. Install Ansible 4.7.0 with pip3

Run the installation with sudo so it applies system-wide.

```bash
sudo pip3 install ansible==4.7.0
```

✅ Ansible is istalled

### 3. Verify Ansible installation
Confirm the installed version and its details
```bash
ansible --version
ansible [core 2.11.12]
  config file = None
  configured module search path = ['/home/thor/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.9/site-packages/ansible
  ansible collection location = /home/thor/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.9.18 (main, Jan 24 2024, 00:00:00) [GCC 11.4.1 20231218 (Red Hat 11.4.1-3)]
  jinja version = 3.1.6
  libyaml = True
```

### 4. Ensure Ansible is globally accessible
By default, pip places Ansible binaries under /usr/local/bin/, which is already included in the system $PATH.
```bash
[tony@stapp01 ~]$ whereis ansible
# Output: ansible: /usr/local/bin/ansible

echo $PATH
# Output includes: /usr/local/bin
```

### Note:
If for some reason, ansible binary is not present in /usr/local/bin or if /usr/local/bin is not present. we can add the binary path in /etc/profile so all users can get it:
```bash
echo 'export PATH=$PATH:/usr/local/bin' | sudo tee -a /etc/profile
source /etc/profile
```
