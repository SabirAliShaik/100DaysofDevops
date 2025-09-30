# 100 Days of DevOps — Day 5
## Exercise: Create a Cron Job

## Task
As part of server automation and scheduling, configure a **cron job** on **App Server 1** in the Stratos Datacenter.  

Requirements:  
1. Ensure the cron service (`cronie`) is installed.  
2. Enable and start the `crond` service.  
3. Add a cron job that runs every **5 minutes**, writing `hello` into `/tmp/cron_text`.  
4. Verify that the cron job has been scheduled.  

## Solution Steps

### 1. Install cronie
```bash
[tony@stapp01 ~]$ sudo yum install -y cronie
CentOS Stream 9 - BaseOS                                                   32 kB/s | 7.0 kB     00:00    
CentOS Stream 9 - BaseOS                                                  9.7 MB/s | 8.8 MB     00:00    
CentOS Stream 9 - AppStream                                                34 kB/s | 7.1 kB     00:00    
CentOS Stream 9 - AppStream                                                42 MB/s |  25 MB     00:00    
CentOS Stream 9 - Extras packages                                          39 kB/s | 8.0 kB     00:00    
CentOS Stream 9 - Extras packages                                          46 kB/s |  20 kB     00:00    
Extra Packages for Enterprise Linux 9 - x86_64                            114 kB/s |  34 kB     00:00    
Extra Packages for Enterprise Linux 9 - x86_64                            9.1 MB/s |  20 MB     00:02    
Extra Packages for Enterprise Linux 9 openh264 (From Cisco) - x86_64      3.5 kB/s | 993  B     00:00    
Extra Packages for Enterprise Linux 9 - Next - x86_64                      96 kB/s |  24 kB     00:00    
Package cronie-1.5.7-14.el9.x86_64 is already installed.
Dependencies resolved.
Nothing to do.
Complete!
```
✅ Cronie is installed.
### 2. Enable and start crond service
```bash
[tony@stapp01 ~]$ sudo systemctl enable crond
[tony@stapp01 ~]$ sudo systemctl start crond
[tony@stapp01 ~]$ sudo systemctl status crond
● crond.service - Command Scheduler
     Loaded: loaded (/usr/lib/systemd/system/crond.service; enabled; preset: enabled)
     Active: active (running) since Sat 2025-09-27 16:58:33 UTC; 7min ago
   Main PID: 1018 (crond)
      Tasks: 2 (limit: 411434)
     Memory: 2.0M
     CGroup: /docker/dd9a8908d92d3e24478409f6a4775b30e18c4c614815b787e757333a8e20bef4/system.slice/crond.service
             ├─1018 /usr/sbin/crond -n
             └─1331 /usr/sbin/anacron -s
Sep 27 17:01:01 stapp01.stratos.xfusioncorp.com anacron[1331]: Jobs will be executed sequentially
Sep 27 17:01:01 stapp01.stratos.xfusioncorp.com CROND[1313]: (root) CMDEND (run-parts /etc/cron.hourly)
Sep 27 17:05:31 stapp01.stratos.xfusioncorp.com systemd[1]: crond.service: Changed dead -> running
Sep 27 17:05:31 stapp01.stratos.xfusioncorp.com systemd[1]: crond.service: Failed to reset devices.allow/devices.deny: Operation not permitted
Sep 27 17:05:31 stapp01.stratos.xfusioncorp.com systemd[1]: crond.service: Failed to set 'trusted.invocation_id' xattr on control group /docker/dd9a8908d92d3e24478409f6a4775b30e18c4c614815b787e757333a8e20bef4/system.slice/crond.service, ignoring: Operation not permitted
Sep 27 17:05:31 stapp01.stratos.xfusioncorp.com systemd[1]: crond.service: Failed to remove 'trusted.delegate' xattr flag on control group /docker/dd9a8908d92d3e24478409f6a4775b30e18c4c614815b787e757333a8e20bef4/system.slice/crond.service, ignoring: Operation not permitted
Sep 27 17:06:18 stapp01.stratos.xfusioncorp.com systemd[1]: crond.service: Trying to enqueue job crond.service/start/replace
Sep 27 17:06:18 stapp01.stratos.xfusioncorp.com systemd[1]: crond.service: Installed new job crond.service/start as 238
Sep 27 17:06:18 stapp01.stratos.xfusioncorp.com systemd[1]: crond.service: Enqueued job crond.service/start as 238
Sep 27 17:06:18 stapp01.stratos.xfusioncorp.com systemd[1]: crond.service: Job 238 crond.service/start finished, result=done
```
✅ Cron service is running.

### 3. Add a cron job
To edit root’s crontab:
```bash
[tony@stapp01 ~]$ sudo crontab -e -u root
```
Add the following line:
```bash
*/5 * * * * echo hello > /tmp/cron_text
```
This schedules a job to run every 5 minutes, writing hello to /tmp/cron_text.

### 4. Verify cron jobs
List cron jobs for root:
```bash
[tony@stapp01 ~]$ sudo crontab -l -u root
*/5 * * * * echo hello > /tmp/cron_text
```
To list cron jobs for the current user:
```bash
[tony@stapp01 ~]$ crontab -l
```
✅ Cron job successfully added and verified.

## Final Notes
cronie package was installed and verified.
crond service is enabled and running.
Cron job runs every 5 minutes to write hello into /tmp/cron_text.