# WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS
A technology stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically chosen to work together in creating a well-functioning software. They are acronymns for individual technologies used together for a specific technology product. some examples are LAMP, LEMP, MERN and MEAN

## LAMP STACK set up involve setting up (Linux + Apache + Mysql + PHP)

## 1. Set up AWS account and spin up an EC2 Instance (LINUX Server)
![image](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/26f1ac77-bb60-4146-825f-18259e44a54a)
connect to the EC2 terminal using Ubuntu installed on Windows 10 by ssh
 the setup looks like this 
  
  ![image](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/4f70859c-0d38-403b-bf27-ad1dde56a2e4)

## 2. installing apache and updating the firewall
  
APACHE HTTP SERVER is the most widely used web server software. Developed and maintained by Apache Software Foundation, Apache is an open source software available for free. It runs on 67% of all webservers in the world. It is fast, reliable, and secure. It can be highly customized to meet the needs of many different environments by using extensions and modules. Most WordPress hosting providers use Apache as their web server software. However, websites and other applications can run on other web server software as well. Such as  Nginx and Microsoft IIS

Install Apache using Ubuntu’s package manager  'apt'

 `sudo apt update`
 
 run apache2 package installation
 
 `sudo apt install apache2`
 
 To verify that apache2 is running as a Service in my OS, i used following command
 
 `sudo systemctl status apache2`
 
 this should be green and running, I have just launched my first Web Server in the Clouds

Before I can receive any traffic by my Web Server, I need to open TCP port 80 which is the default port that web browsers use to access web pages on the Internet
i need to add a rule to EC2 configuration to open inbound connection through port 80:

![installing Apache2](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/c25360b6-e7dd-469f-a7ea-a5685765d4c0)


time to test how my Apache HTTP server can respond to requests from the Internet.

Open a web browser of your choice and try to access following url

http://<Public-IP-Address>:80
  
![image](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/dc331ece-71f3-4c4f-8619-e16c8af25b28)
  
  ## 3. Installing MYSQL
  
  Now that I have a web server up and running, I need to install a Database Management System (DBMS) to be able to store and manage data for my site in a relational database. MySQL is a popular relational database management system used within PHP environments, so I will use it in this project.

Again, I use ‘apt’ to acquire and install this software:
  
  `$ sudo apt install mysql-server`
  
  i will log into mysql console by typing
  
  `$ sudo mysql`

  It’s recommended that you run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system. Before running the script you will set a password for the root user, using mysql_native_password as default authentication method. change the password to a strong password and then test if mysql works afterwards
  
`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`
  
  exit mysql with
  
 `mysql> exit`
  
  I start interactive script by running:
  
  `$ sudo mysql_secure_installation`

  ![installing mysql](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/3767b650-3925-4f90-ba44-22b17a187dbb)

  this will ask if i want to validate password pligin, i answer Y for Yes
  Regardless of whether I chose to set up the VALIDATE PASSWORD PLUGIN, my server will next ask me to select and confirm a password for the MySQL root user. This is not to be confused with the system root. The database root user is an administrative user with full privileges over the database system. Even though the default authentication method for the MySQL root user dispenses the use of a password, even when one is set, you should define a strong password here as an additional safety measure.
If I enabled password validation, I’ll be shown the password strength for the root password I just entered and my server will ask if I want to continue with that password. If I'm happy with my current password, enter Y for “yes” at the prompt:
   For the rest of the questions, I press Y and hit the ENTER key at each prompt. This will prompt ME to change the root password, remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MySQL immediately respects the changes you have made. When finished, I test if I'm able to log in to the MySQL console by typing: 
  
  `$ sudo mysql -p`
  
  Notice the -p flag in this command, which will prompt me for the password used after changing the root user password.To exit the MySQL console, type:
  
    `mysql> exit`

    ![setting and changing root user password on MYSQL](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/948791eb-9a78-4c4a-9406-a32e4d749a32)

  
 ## 4. Installing PHP 
  
  I have Apache installed to serve my content and MySQL installed to store and manage my data. PHP is the component of my setup that will process code to display dynamic content to the end user. In addition to the php package, I’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. I’ll also need libapache2-mod-php to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies.

To install these 3 packages at once, I run the code
  
  `sudo apt install php libapache2-mod-php php-mysql`
  
  Once the installation is finished, I can run the following command to confirm my PHP version:
  
  `php -v`

  ![installing PHP](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/26661dbd-76b8-4c7b-a302-1cf628437246)


  ![checking PHP Version](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/748534b4-8397-4122-9504-1046205ccf57)

  
  At this point, my LAMP stack is completely installed and fully operational.

Linux (Ubuntu)
Apache HTTP Server
MySQL
PHP
  
To test my setup with a PHP script, it’s best to set up a proper Apache Virtual Host to hold my website’s files and folders. Virtual host allows me to have multiple websites located on a single machine and users of the websites will not even notice it.
  
  ## Creating a virtual host for my website using Apache
  
  Here I will set up a domain called projectlamp

Apache on Ubuntu 20.04 has one server block enabled by default that is configured to serve documents from the /var/www/html directory.
I will leave this configuration as is and will add my own directory next to the default one.

I will Create the directory for projectlamp using ‘mkdir’ command as follows:
  
  `sudo mkdir /var/www/projectlamp`
  
Then,I'll create and open a new configuration file in Apache’s sites-available directory using my preferred command-line editor. Here, I’ll be using vi or vim (They are the same by the way):  
  
  `sudo vi /etc/apache2/sites-available/projectlamp.conf`
  
  This will create a new blank file. Paste in the following bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and paste the text:

![using ls to show the new files in the sites-available directory](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/cc0ff541-a87c-4bb1-b06c-3d40d3e14951)

<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
  
  To save and close the file, I simply follow the steps below:

Hit the esc button on the keyboard
Type :
Type wq. w for write and q for quit
I Hit ENTER to save the file
You can use the ls command to show the new file in the sites-available directory

  ![make directory project lamp using vi or vim](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/d36de5b1-6d45-4487-bb86-42828446acd4)

  `sudo ls /etc/apache2/sites-available`
  
  I will see something like this;
  
000-default.conf  default-ssl.conf  projectlamp.conf
  
  With this VirtualHost configuration, I'm telling Apache to serve projectlamp using /var/www/projectlampl as its web root directory. If I would like to test Apache without a domain name, I can remove or comment out the options ServerName and ServerAlias by adding a # character in the beginning of each option’s lines. Adding the # character there will tell the program to skip processing the instructions on those lines.
  
  I can now use a2ensite command to enable the new virtual host:
  
  `sudo a2ensite projectlamp`
  
  I might want to disable the default website that comes installed with Apache. This is required if I'm not using a custom domain name, because in this case Apache’s default configuration would overwrite my virtual host. To disable Apache’s default website use a2dissite command , type:
  
  `sudo a2dissite 000-default`
  
  To make sure my configuration file doesn’t contain syntax errors, run:
  
  `sudo apache2ctl configtest`
  
  Finally, reload Apache so these changes take effect:
  
  `sudo systemctl reload apache2`
  
  my new website is now active, but the web root /var/www/projectlamp is still empty. Create an index.html file in that location so that i can test that the virtual host works as expected:
  
  `sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html`
  
  Now I go to your browser and try to open your website URL using IP address:
  
  http://<Public-IP-Address>:80

  ![Hello LAMP from hostname on browser](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/b932ed80-95e7-4cc4-81c3-f85108006a09)

  
  I will see the text from the echo command
In the output I will see my server’s public hostname (DNS name) and public IP address. i can also access my website in my browser by public DNS name, not only by IP – (port is optional)
  
  http://<Public-DNS-Name>:80

![accessing website using public DNS name](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/9b3d6e93-789f-4476-acb9-846c3c55e04f)

  
  I can leave this file in place as a temporary landing page for your application until you set up an index.php file to replace it. Once I do that, I'll make sure I remember to remove or rename the index.html file from my document root, as it would take precedence over any index.php file by default.
  
  ## Enable PHP on the website
  
 With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. This is useful for setting up maintenance pages in PHP applications, by creating a temporary index.html file containing an informative message to visitors. Because this page will take precedence over the index.php page, it will then become the landing page for the application. Once maintenance is over, the index.html is renamed or removed from the document root, bringing back the regular application page.
In case I want to change this behavior, I’ll need to edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive: 
  
  `sudo vim /etc/apache2/mods-enabled/dir.conf`
  
  <IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
  
  After saving and closing the file, you will need to reload Apache so the changes take effect:
  
  `sudo systemctl reload apache2`
  
  Finally, we will create a PHP script to test that PHP is correctly installed and configured on your server.
Now that you have a custom location to host your website’s files and folders, we’ll create a PHP test script to confirm that Apache is able to handle and process requests for PHP files.
Create a new file named index.php inside your custom web root folder:

`vim /var/www/projectlamp/index.php`
  
This will open a blank file. Add the following text, which is valid PHP code, inside the file:

When you are finished, save and close the file, refresh the page and you will see a page similar to this

![image](https://github.com/Sakirat/Darey.io-pbl/assets/110112922/7f1f3775-7eae-4cce-ba0a-6ca212d75c7a)

This page provides information about your server from the perspective of PHP. It is useful for debugging and to ensure that your settings are being applied correctly.
If you can see this page in your browser, then your PHP installation is working as expected.
After checking the relevant information about your PHP server through that page, it’s best to remove the file you created as it contains sensitive information about your PHP environment -and your Ubuntu server. You can use rm to do so:

`sudo rm /var/www/projectlamp/index.php`

You can always recreate this page if you need to access the information again later. Congratulations! You have finished your very first REAL LIFE PROJECT by deploying a LAMP stack website in AWS Cloud!
