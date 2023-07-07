## CLIENT-SERVER ARCHITECTURE WITH MYSQL

Client-Server refers to an architecture in which two or more computers are connected together over a network to send and receive requests between one another. In their communication, each machine has its own role: the machine sending requests is usually referred as “Client” and the machine responding (serving) is called “Server”. A simple diagram of Web Client-Server architecture is presented below:

![image](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/07f6255f-4fdf-4fac-86d4-62e550c9d214)

In the example above, a machine that is trying to access a Web site using Web browser or simply ‘curl’ command is a client and it sends HTTP requests to a Web server (Apache, Nginx, IIS or any other) over the Internet. If we extend this concept further and add a Database Server to our architecture, we can get this picture:

![image](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/7d91f799-01c9-4e93-835d-50da0e510c8f)


In this case, our Web Server has a role of a “Client” that connects and reads/writes to/from a Database (DB) Server (MySQL, MongoDB, Oracle, SQL Server or any other), and the communication between them happens over a Local Network (it can also be Internet connection, but it is a common practice to place Web Server and DB Server close to each other in local network). The setup on the diagram above is a typical generic Web Stack architecture that you have already implemented in my previous projects (LAMP, LEMP, MEAN, MERN), this architecture can be implemented with many other technologies – various Web and DB servers, from small Single-page applications SPA to large and complex portals.

### IMPLEMENT A CLIENT SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT SYSTEM (DBMS)

To demonstrate a basic client-server using MySQL Relational Database Management System (RDBMS), Create and configure two Linux-based virtual servers (EC2 instances in AWS).

Server A name - `mysql server`
Server B name - `mysql client`

![2 instances for mysql server and mysql client](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/95a19870-66b4-4f03-898e-b677391bb66b)

On mysql server Linux Server install MySQL Server software

On mysql client Linux Server install MySQL Client software.

![Install mysql server and mysql client](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c8cf0269-0995-4352-a98e-3efdd94c5605)

By default, both of your EC2 virtual servers are located in the same local virtual network, so they can communicate to each other using local IP addresses. Use mysql server's local IP address to connect from mysql client. MySQL server uses TCP port 3306 by default, so you will have to open it by creating a new entry in ‘Inbound rules’ in ‘mysql server’ Security Groups. For extra security, do not allow all IP addresses to reach your ‘mysql server’ – allow access only to the specific local IP address of your ‘mysql client’.

![edit inbound rule oF SG on mysql server](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/40b62c7b-81ec-4cdc-b38a-24905b57a397)

You might need to configure MySQL server to allow connections from remote hosts. Run the command below

`sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf`

Replace ‘127.0.0.1’ of the bind-address to ‘0.0.0.0’ like this:

![command to allow connections from remote host](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/2932e12e-7082-48f2-bd6a-bd54e3893220)

![configure mysql server to allow connections from remote hosts](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/d4324347-be4f-421e-92ea-5e90d56e1316)

set up a user, create a database and grant all privileges to the user created on mysql server

![create database create user and grant all privileges to user on database](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/ab2f6d60-b3b1-4300-a63e-245a5a9ca78a)


From mysql client Linux Server connect remotely to mysql server Database Engine without using SSH. You must use the mysql utility to perform this action.

![connect remotely from mysql client to mysql server](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/b33dd095-366d-415f-8fea-7b4820b71530)

Check that you have successfully connected to a remote MySQL server and can perform SQL queries, run the command

`Show databases;`

If you see an output similar to the below image, then you have successfully completed this project – you have deloyed a fully functional MySQL Client-Server set up.

![checking if we can perform SQL queries](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/065d626a-9a27-4310-b6fc-087f0a93333d)

