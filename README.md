#guide #server #aws #caddy #docker #docker-compose #documentation 

# Getting n8n Setup
1. Spin up an EC2 instance on AWS in the desired region.
	1. Select ubuntu and leave the default options
2. Select your Instance Type based on what you are needing. A t2 small should be more than enough. t2 micro would be fine for very basic use though
3. Proceed without a key pair - need to research this later and make sure it's not like super insecure. I don't think it is though for this use case.
4. In Network settings make sure to select the following:
	1. allow ssh traffic from anywhere
	2. allow https traffic from the internet
	3. allow http traffic from the internet
5. Default volume settings should be enough
6. Click on Launch Instance
7. The instance will boot up then it will be time to install docker-compose, docker, and caddy
8. run `sudo apt update` and `sudo apt upgrade`
9. Follow the instructions here to install docker https://docs.docker.com/engine/install/ubuntu/ 
10. Follow these commands to install docker compose replace `2.14.2` with whatever version is on their GitHub page in the versions section: https://github.com/docker/compose
``` bash
sudo apt update
sudo curl -L "https://github.com/docker/compose/releases/download/2.14.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo apt upgrade -y
```
10. Go to your DNS provider and add an A record pointing to the EC2 public IP address. 
```
Type: A
Name: n8n (or the desired subdomain)
IP address: <IP_OF_YOUR_SERVER>
```
11. Run this command to get the files for using caddy. This gives you files for a quick start on getting this setup.
```` bash
git clone https://github.com/bruddlee/n8n-docker-caddy
````
12. Then run `cd n8n-docker-caddy` or change directory (cd) into whatever the name of the repo is that was cloned
13. Create a docker volume for the caddy_data `docker volume create caddy_data`
14. run `nano .env`  
	1. follow the comments in the file to change out the credentials for the database, the credentials for the n8n instance, your domain name, and the credentials for the email you will be using to send out the invitations and password reset emails for users of the n8n instance.
17. Run `nano caddy_config/Caddyfile`
	1. In this file replace domain and suffix with yourwebsite.com or whatever your website name is. If you didn't opt for using n8n as the subdomain, then change that as well. Yours should look something like this when completed:
```
n8n.test.com {
    reverse_proxy n8n:5678 {
      flush_interval -1
    }
}
```
18. Now you should be able to run the command `docker-compose up -d` to build your container and get it up and running.
19. In your web browser open up the URL that you setup the instance to run on and see what happens. Enter the user name and password you defined in the .env file for the variables for `N8N_BASIC_AUTH_USER` and `N8N_BASIC_AUTH_PASSWORD` Once you do that you are all set to go.
## How to get email invites working for adding users
- I've successfully got an n8n instance working on aws with email smtp using gmail and without any trouble. I just used docker compose and followed there instructions. Now to try and do it in queue mode to get that to function more reliably when things are very high volume
	- Steps to get this working:
		- in admin.google.com go to security -> Less secure apps -> allow users to enable
		- then go into manage my google account when you are logged into gmail in the top right, then security then less secure apps enabled.
	- This is good for temp purposes, but I am going to look for a more secure long term solution to this.
