# WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS
A technology stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically chosen to work together in creating a well-functioning software. They are acronymns for individual technologies used together for a specific technology product. some examples are LAMP, LEMP, MERN and MEAN
## Set up AWS account and spin up an EC2 Instance
![image](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/26f1ac77-bb60-4146-825f-18259e44a54a)
connect to the EC2 terminal using Ubuntu installed on Windows 10 by ssh
  
 the setup looks like this 
  
  ![image](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/4f70859c-0d38-403b-bf27-ad1dde56a2e4)

## installing apache and updating the firewall
  
APACHE HTTP SERVER is the most widely used web server software. Developed and maintained by Apache Software Foundation, Apache is an open source software available for free. It runs on 67% of all webservers in the world. It is fast, reliable, and secure. It can be highly customized to meet the needs of many different environments by using extensions and modules. Most WordPress hosting providers use Apache as their web server software. However, websites and other applications can run on other web server software as well. Such as  Nginx and Microsoft IIS

Install Apache using Ubuntu’s package manager  'apt'

 sudo apt update
 
 run apache2 package installation
 
 sudo apt install apache2
 
 To verify that apache2 is running as a Service in our OS, use following command
 
 sudo systemctl status apache2
 
 this should be green and running, I have just launched my first Web Server in the Clouds

Before I can receive any traffic by my Web Server, I need to open TCP port 80 which is the default port that web browsers use to access web pages on the Internet

i need to add a rule to EC2 configuration to open inbound connection through port 80:
