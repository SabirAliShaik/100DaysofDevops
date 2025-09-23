# 100 Days of DevOps — Day 1
## Exercise: Linux User Setup with Non-Interactive Shell

### Steps
1. **Login to server from jump server using user credentials**
   ```bash
   ssh username@server-ip-address
	```
2. **Switch to root user**
	```bash
	sudo su -
	```
3. **Create a user anita with a non-interactive shell**
	```bash
	useradd -m -s /bin/false anita
	```
4. **Verify if the user is created in /etc/passwd file**
	```bash
	cat /etc/passwd | grep anita
	
	Example output:
	anita:x:1002:1002::/home/anita:/bin/false
	```
✅ Result: User anita created successfully with a non-interactive shell (/bin/false).