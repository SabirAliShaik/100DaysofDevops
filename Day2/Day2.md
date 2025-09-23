# 100 Days of DevOps — Day 2
## Exercise: Linux User Setup with Non-Interactive Shell

### Steps
1. **Login to server from jump server using user credentials**
   ```bash
   $ssh username@server-ip-address
	```
2. **Switch to root user**
	```bash
	$sudo su -
	```
3. **Create a user javed with expiry date as 2023-12-07**
	```bash
	#useradd -m -e 2023-12-07 -s /bin/bash javed
	```
	Note:
	General syntax for user with expiry date:
	```bash
	#useradd -e YYYY-MM-DD username
	```
4. **Verify if the user is created in /etc/passwd file**
	```bash
	#cat /etc/passwd | grep javed
	
	Example output:
	javed:x:1002:1002::/home/javed:/bin/bash
	```
5. **Verify user account expiry date**
	```bash
	#chage -l javed
	```
	------------------------------------------------------------------------
	Last password change                                    : Sep 23, 2025
	Password expires                                        : never
	Password inactive                                       : never
	Account expires                                         : Dec 07, 2023
	Minimum number of days between password change          : 0
	Maximum number of days between password change          : 99999
	Number of days of warning before password expires       : 7
	------------------------------------------------------------------------
