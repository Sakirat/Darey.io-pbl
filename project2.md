# WEB STACK IMPLEMENTATION (LEMP STACK)
## LINUX + NGINX + MYSQL + PHP
Project 2 helps us cement the skills of deploying Web Solutions using LEMP Stacks
Project 1 used the Apache2 Web server while project 2 will be using the Nginx web server which is also popular and widely used as Apache

### Setting a virtual webserver (EC2) witrh Ubuntu OS using the free tier T2.micro family of instances and Connecting to NGINX WEB Server

In order to display web pages to our site visitors, we are going to employ Nginx, a high-performance web server. We’ll use the apt package manager to install this package.
Since this is our first time using apt for this session, start off by updating your server’s package index. Following that, you can use apt install to get Nginx installed:

$ sudo apt update

$ sudo apt install nginx

![connecting to EC2 instance using GIT BASH](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/315a607a-dd90-47a2-891c-21706b32c73e)

When prompted, enter Y to confirm that you want to install Nginx. Once the installation is finished, the Nginx web server will be active and running on your Ubuntu 20.04 server. To verify that nginx was successfully installed and is running as a service in Ubuntu, run:

$ sudo systemctl status nginx

If it is green and running, then you did everything correctly. Before we can receive any traffic by our Web Server, we need to open TCP port 80 which is default port that web browsers use to access web pages in the Internet. As we know, we have TCP port 22 open by default on our EC2 machine to access it via SSH, so we need to add a rule to EC2 configuration to open inbound connection through port 80:

![adding inbound rule to open TCP port 80](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/4c0930ad-3983-4d9e-9421-a5651e29465e)

Our server is running and we can access it locally and from the Internet (Source 0.0.0.0/0 means ‘from any IP address’). First, let us try to check how we can access it locally in our Ubuntu shell, run:

 $ curl http://localhost:80
or
$ curl http://127.0.0.1:80

Now it is time for us to test how our Nginx server can respond to requests from the Internet. Open a web browser of your choice and try to access following url

http://<Public-IP-Address>:80 

Another way to retrieve your Public IP address, other than to check it in AWS Web console, is to use following command:

curl -s http://169.254.169.254/latest/meta-data/public-ipv4
