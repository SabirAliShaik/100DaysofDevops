# 100 Days of DevOps — Day 9
## Exercise: MariaDB Troubleshooting

## Task
There is a critical issue with the Nautilus application in Stratos DC. The production support team identified that the application is unable to connect to the database. The issue was traced to MariaDB service being down on the database server.

**Goal:** Look into the issue and fix it.

## Solution Steps

### 1. Login to database server
Login from the jump host using SSH.

```bash
thor@jumphost ~$ ssh peter@172.16.239.10
# Confirm host authenticity and provide password
```

### 2. Verify if MariaDB is installed
```bash
[peter@stdb01 ~]$ command -v mariadb
# Output: /usr/bin/mariadb
```

✅ MariaDB binary is present in `/usr/bin/mariadb`

### 3. Check MariaDB service status
```bash
[peter@stdb01 ~]$ systemctl status mariadb
○ mariadb.service - MariaDB 10.5 database server
     Loaded: loaded (/usr
/lib/systemd/system/mariadb.service; enabled; preset: disabled)
     Active: inactive (dead) since Wed 2025-10-01 16:59:10 UTC; 9min ago
   Duration: 5.772s
       Docs: man:mariadbd(8)
             https://mariadb.com/kb/en/library/systemd/
    Process: 867 ExecStartPre=/usr/libexec/mariadb-check-socket (code=exited, status=0/SUCCESS)
    Process: 1074 ExecStartPre=/usr/libexec/mariadb-prepare-db-dir mariadb.service (code=exited, status=0
/SUCCESS)
    Process: 1440 ExecStart=/usr/libexec/mariadbd --basedir=/usr $MYSQLD_OPTS $_WSREP_NEW_CLUSTER (code=e
xited, status=0/SUCCESS)
    Process: 1490 ExecStartPost=/usr/libexec/mariadb-check-upgrade (code=exited, status=0/SUCCESS)
   Main PID: 1440 (code=exited, status=0/SUCCESS)
     Status: "MariaDB server is down"
```
Sample output indicates MariaDB is **installed and enabled**, but currently **inactive (dead)**.

### 4. Attempt to start MariaDB service

```bash
[peter@stdb01 ~]$ sudo systemctl start mariadb
```
we can see below message in the response

"Job for mariadb.service failed because the control process exited with error code.
See "systemctl status mariadb.service" and "journalctl -xeu mariadb.service" for details.
"
✅ systemctl start command was unable to start the mariadb service

### 5. Check MariaDB logs for errors

```bash
[peter@stdb01 ~]$ sudo cat /var/log/mariadb/mariadb.log
2025-10-01 17:17:32 0 [ERROR] InnoDB: Operating system error number 13 in a file operation.
2025-10-01 17:17:32 0 [ERROR] InnoDB: The error means mysqld does not have the access rights to the directory.
2025-10-01 17:17:32 0 [ERROR] InnoDB: Operating system error number 13 in a file operation.
2025-10-01 17:17:32 0 [ERROR] InnoDB: The error means mysqld does not have the access rights to the directory.
2025-10-01 17:17:32 0 [ERROR] InnoDB: Cannot open datafile './ibtmp1'
2025-10-01 17:17:32 0 [ERROR] InnoDB: Unable to create the shared innodb_temporary
2025-10-01 17:17:32 0 [ERROR] InnoDB: Plugin initialization aborted with error Cannot open a file
2025-10-01 17:17:32 0 [Note] InnoDB: FTS optimize thread exiting.
2025-10-01 17:17:32 0 [Note] InnoDB: Starting shutdown...
2025-10-01 17:17:32 0 [ERROR] Plugin 'InnoDB' registration as a STORAGE ENGINE failed.
2025-10-01 17:17:32 0 [Note] Plugin 'FEEDBACK' is disabled.
2025-10-01 17:17:32 0 [ERROR] Unknown/unsupported storage engine: InnoDB
2025-10-01 17:17:32 0 [ERROR] Aborting
```

✅ MariaDB (mysqld) doesn’t have permission to access its data directory (/var/lib/mysql by default) or the temporary files like ibtmp1. This is why InnoDB cannot start.

### 6. Verify ownership of the data directory
```bash
[peter@stdb01 ~]$ sudo ls -ld /var/lib/mysql
# Output shows owner is root:mysql (incorrect)
```
- The owner of /var/lib/mysql is root, but it must be mysql:mysql. This is exactly why MariaDB fails with error 13 (permission denied). MariaDB requires ownership by `mysql:mysql`.

### 7. Fix ownership and permissions
```bash
sudo chown -R mysql:mysql /var/lib/mysql
sudo chmod -R 750 /var/lib/mysql
```
Verify the changes:
```bash
[peter@stdb01 ~]$ cd /var/lib/mysql
[peter@stdb01 mysql]$ ls -l
total 110640
-rw-rw---- 1 mysql mysql     24576 Oct  1 17:18 aria_log.00000001
-rw-rw---- 1 mysql mysql        52 Oct  1 17:18 aria_log_control
-rw-rw---- 1 mysql mysql       976 Oct  1 16:59 ib_buffer_pool
-rw-rw---- 1 mysql mysql 100663296 Oct  1 17:18 ib_logfile0
-rw-rw---- 1 mysql mysql  12582912 Oct  1 16:59 ibdata1
-rw-rw---- 1 mysql mysql         0 Oct  1 16:59 multi-master.info
drwx------ 2 mysql mysql      4096 Oct  1 16:59 mysql
-rw-rw---- 1 mysql mysql        16 Oct  1 16:59 mysql_upgrade_info
drwx------ 2 mysql mysql      4096 Oct  1 16:59 performance_schema
```
All files and directories should now belong to `mysql:mysql` with proper permissions.


### 8. Restart MariaDB service and verify
```bash
[peter@stdb01 mysql]$ sudo systemctl restart mariadb
[peter@stdb01 mysql]$ sudo systemctl status mariadb
● mariadb.service - MariaDB 10.5 database server
     Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; preset: disabled)
     Active: active (running) since Wed 2025-10-01 17:39:45 UTC; 7s ago
       Docs: man:mariadbd(8)
             https://mariadb.com/kb/en/library/systemd/
    Process: 3021 ExecStartPre=/usr/libexec/mariadb-check-socket (code=exited, status=0/SUCCESS)
    Process: 3055 ExecStartPre=/usr/libexec/mariadb-prepare-db-dir mariadb.service (code=exited, status=0/SUCCESS)
    Process: 3184 ExecStartPost=/usr/libexec/mariadb-check-upgrade (code=exited, status=0/SUCCESS)
   Main PID: 3150 (mariadbd)
     Status: "Taking your SQL requests now..."
      Tasks: 15 (limit: 411434)
     Memory: 97.4M
     CGroup: /docker/edb6712332a72e11b5a7c4eedb32b0e79abbf2cd81296b97aaaaa0e488467488/system.slice/mariadb.service
             └─3150 /usr/libexec/mariadbd --basedir=/usr

```
Expected output indicates **MariaDB is active and running**.

✅ The issue is resolved, and the Nautilus application can now connect to the database.
