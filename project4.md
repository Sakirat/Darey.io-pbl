## MEAN Stack Deployment to Ubuntu in AWS

Now, we have already learned how to deploy LAMP, LEMP and MERN Web stacks – it is time to get ourself familiar with MEAN stack and deploy it to Ubuntu server.

### MEAN STACK = MongoDB + ExpressJS + AngularJS + NodeJS
MongoDB = Document Database
Express = Back-end application framework
Angular = Front-end application framework
Node.Js = Javascript runtime enviroment

In order to complete this project we will need an AWS account and a virtual server with Ubuntu Server OS.

![setting up instance for project4](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/9f0d01eb-fbc9-47f7-a8e7-bd43056d4d87)

![connecting to instance on terminal](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/00a492b9-13db-485b-b1da-f7c51a6f4abc)

### Install NodeJs

Node.js is a JavaScript runtime built on Chrome’s V8 JavaScript engine. Node.js is used in this project to set up the Express routes and AngularJS controllers.

run the following command to get ubuntu ready for use

'sudo apt update'
'sudo apt upgrade'

add certificates and dependencies using the command below

'sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates'
'curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -'
