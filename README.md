

## Prerequisites

- Node Version 22


### 1. For Run This Applications
```bash
# install packages
npm install 

# Testing The Applications
npm check

# For Run the application
npm start
```


### Deployment Process
1. **Cleanup**: Removes existing process if running
   ```bash
   pm2 delete node-app || true
   ```

2. **Start Application**: Launches with absolute path
   ```bash
   pm2 start "./src/server.js" --name node-app
   ```

3. **Save Process List**: Persists PM2 configuration
   ```bash
   pm2 save
   ```


# Deploying to staging environment (manual deploy process to CICD Implementation as well as use nginx for port forwarding)

## 1. Manual Deployment
> Initially need to upload git to your project. Then after upload your project in git you need to login your server.Server would be local computer or Vpc or cloud  

### Steps:
```bash 
1. Firstly connect your server via ssh or ip and pass

#Create a Directory and move to the directory
2. mkdir first_deployemnt 

#Clone your project in your directory
3. git clone 

# install required dependency for example install node/update 
# firstly check nvm version a package manager.
4. nvm --version

   #if do not exist then execute below command
   a. curl -fsSL https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.4/install.sh | bash  
	b. source ~/.bashrc 
	c. nvm install 22  
	d. node -v 

# Install npm
5. install npm 

# Check your test case. This command will be different for your project wise
6. npm run check 

# run your application
7. npm start  

# Note: After running your application, it will be running on your terminal. If you close your terminal, then the application will be closed. To solve this problem, use pm2 - background process manager. It will keep your application running in the background.  
```

## 2. PM2 - Process Manager (run your application in the background and monitor the process)
### Steps: 

```bash
# Install pm2
1. npm install -g pm2

# Show status
2. pm2 status

# Start your application by pm2 // Here define your application start command and also define your application name as per your choice. 
3. pm2 start "npm start" --name="node_app"

# Some necessary commands below:
 	a. pm2 restart app_name  
	b. pm2 reload app_name  
	c. pm2 stop app_name  
	d. pm2 delete app_name  

#Note: You can run all types of applications in the background using this process manager.  
```

## 3. NginX (To access your application without specifying a port, we use Nginx for port forwarding)
### Steps:  
> HTTPS forwards to port 443, HTTP forwards to port 80, and SSH forwards to port 22.  

```bash
#First check NginX version
1. nginx -v

#If not installed then follow the below command:

#Install nginx
1. sudo apt-get install nginx  

#After installation, check if needed:  
	a. sudo systemctl status nginx #Check if nginx is running   
	b. sudo systemctl start nginx  #Start nginx    
	c. sudo systemctl restart nginx #Restart nginx   
	d. sudo nginx -t  #Check configuration errors   
	e. nginx: configuration file /etc/nginx/nginx.conf test is successful #If configuration is correct, you will see   
	f. sudo netstat -tulnp | grep nginx #Check nginx process running port   
	g. sudo tail -f /var/log/nginx/access.log #Check nginx logs error   

# Go to config file
2. cd /etc/nginx/sites-available

# Customize server for port forwarding -  
3.
`
location / {
proxy_pass http://127.0.0.1:5000;
proxy_set_header Host $host;
proxy_set_header X-Real-Ip $remote_addr;
proxy_set_header X-Forwarded-for $proxy_add_x_forwarded_for;
}
`
	
# After configuring restart the server
4. sudo service nginx restart all  

# SSL for HTTPS - using certbot 
``` 

#### Note: After completing a manual deployment, the main issue arises when changes need to be made to the application. You would have to redeploy manually by following:  
1. git pull to the directory  
2. npm install  
3. pm2 restart all  

So, managing this manually is cumbersome. To solve this, we implement **CI/CD**. Please follow the next steps.

## 4. Self-hosted runner setup for CI/CD

### Steps:  
	# Create a directory in the root of the application - .github/workflow/cicd.yml  
	# Write yml code instructions -  
		# Example of yml in my CICD implementation project  
		1. First, implement application tests - like writing test cases in the yml file.  
		2. Then, after the test, we need deployment. But currently, the VPS is not connected to Git, so the VPS doesn't understand when we push code to Git. We solve this problem by setting up a GitHub runner. We delete the previous application directory from the VPS server, create another directory, and execute some commands from the GitHub runner.  
		3. After executing all commands successfully, configure the GitHub runner.  
		4. Then, run it using the following command - `./run.sh` // However, this command has limitations. When you close the terminal, it stops automatically. To solve this, use `./svc.sh` or `pm2`. Since `./svc.sh` is GitHub's built-in process manager, we will use it.  
		5. Solve this problem by installing - `sudo ./svc.sh install`  
		6. After installation, start the runner - `sudo ./svc.sh start`  
		7. Now, write yml instructions for deployment after test execution. Example written in the **CI/CD implementation repository**.  
		8. After pushing the code, deployment happens automatically.  
		9. Sometimes, we need to build the application before deployment, e.g., in React/Next.js projects. Follow the build steps before deployment.  

## 5. Artifact (for managing build files before deployment)

### Steps:  
	# Write build yml instructions and use an artifact package to manage build files. Example given in the **CI/CD implementation repository**.  
	# We use artifact package:  
		1. **Upload artifact** - This is used in build yml instructions to upload build files.  
		2. **Download artifact** - This is used in deploy yml instructions to download and deploy the build.  

#### Note: Every instruction depends on the previous steps.  
- **Deployment depends on the build process**  
- **Build depends on testing**  





