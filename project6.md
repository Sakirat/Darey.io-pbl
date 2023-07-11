## WEB SOLUTION WITH WORDPRESS

In this project you will be tasked to prepare storage infrastructure on two Linux servers and implement a basic web solution using WordPress. WordPress is a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS).
The project consistes of two parts:
1. Configure storage subsystem for Web and Database servers based on Linux OS. The focus of this part is to give us practical experience of working with disks, partitions and volumes in Linux.
2. Install WordPress and connect it to a remote MySQL database server. This part of the project will solidify our skills of deploying Web and DB tiers of Web solution.

#### Three-tier Architecture
Generally, web, or mobile solutions are implemented based on what is called the Three-tier Architecture. Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers.

![image](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/2962979a-8b48-4bd7-a22a-44a8985331e6)

1. Presentation Layer (PL): This is the user interface such as the client server or browser on your laptop.
2. Business Layer (BL): This is the backend program that implements business logic. Application or Webserver
3. Data Access or Management Layer (DAL): This is the layer for computer data storage and data access. Database Server or File System Server such as FTP server, or NFS Server

   In this project, we will have the hands-on experience that showcases Three-tier Architecture while also ensuring that the disks used to store files on the Linux servers are adequately partitioned and managed through programs such as gdisk and LVM respectively. We will be working working with several storage and disk management concepts.

   The 3-Tier Setup
1. A Laptop or PC to serve as a client
2. An EC2 Linux Server as a web server (This is where you will install WordPress)
3. An EC2 Linux server as a database (DB) server

In previous projects we used ‘Ubuntu’, but it is better to be well-versed with various Linux distributions, thus, for this projects we will use very popular distribution called ‘RedHat’ (it also has a fully compatible derivative – CentOS). For Ubuntu server, when connecting to it via SSH/Putty or any other tool, we used ubuntu user, but for RedHat you will need to use ec2-user user. Connection string will look like ec2-user@<Public-IP>

### LAUNCH AN EC2 INSTANCE THAT WILL SERVE AS “WEB SERVER"

Launch an EC2 instance that will serve as “Web Server”. Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB.

![spin up an EC2 to serve as web server](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/446c539e-e61a-431d-8970-43148b1e0434)

![Creating 3 volumes in AWS](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c5d15ea8-20ef-4e78-92e0-39de68e0d8e4)

![attach 3 volumes to the instance created](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/562ab70e-f9ac-4e19-90c8-6a5d6205868e)

Open up the Linux terminal to begin configuration

![connecting to our instance_server](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/87eb3531-593e-43ce-b947-3f2020f568a0)

Use lsblk command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their names will likely be xvdf, xvdh, xvdg.

![use ls dev to inspect newly created block devices](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/75b2ef8b-c66f-4973-b6f6-167bbaff1988)

![use lsblk to inspeck block devices are attached](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/4f423a2e-f4d7-4e76-a9f8-ceec1779ad6a)

Use df -h command to see all mounts and free space on your server

![use df -h to see all mounts and free space on server](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/4744ab24-4617-4d95-875f-33f2d34a0881)

Use gdisk utility to create a single partition on each of the 3 disks

`sudo gdisk /dev/xvdf`

![creating partition on each of the 3 disks](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/ca7a1696-c6c0-48d2-9bcd-2f4a3c4ae28f)

Use lsblk utility to view the newly configured partition on each of the 3 disks.

![viewing the partitions created](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/86e0414a-6250-4af4-9e2d-5e2726d7dbc5)

Install lvm2 package using sudo yum install lvm2. Run sudo lvmdiskscan command to check for available partitions.

`sudo yum install lvm2`

`sudo lvmdiskscan`

![installing lvm2](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/f6db8491-607f-4d75-b4e3-bf7c0714fdbd)

Previously, in Ubuntu we used apt command to install packages, in RedHat/CentOS a different package manager is used, so we shall use yum command instead.

Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

`sudo pvcreate /dev/xvdf1`
`sudo pvcreate /dev/xvdg1`
`sudo pvcreate /dev/xvdh1`

![using pvcreate to mark each disks as physical volumes to be used by LVM](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/d40b311c-d8ef-43c2-a49e-e6b658999f28)

Verify that your Physical volume has been created successfully by running 

`sudo pvs`

![verifying PV has been created by running sudo pvs](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c65ed9be-fc40-4bee-bde7-031c0de2b8c2)


Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`

![creating volume group VG and adding all 3 PVs to it](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/5f02928f-6c8a-4dbe-9da9-49c0049a6aaa)

Verify that your VG has been created successfully by running 

`sudo vgs`

![confirm VG was created successfully](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/b98a2565-14f8-4c8c-ae20-e8e47ebe1ab2)


Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.

`sudo lvcreate -n apps-lv -L 14G webdata-vg`
`sudo lvcreate -n logs-lv -L 14G webdata-vg`

Verify that your Logical Volume has been created successfully by running 

`sudo lvs`

![creating 2 logical volumesfrom the VG](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/3e218d3f-29b7-4b4b-8be8-d1ce2a233b30)


![confirm LVs have been created](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/e3de8092-3a7d-417f-9a08-d2f1ccf1f78e)


Verify the entire setup

`sudo vgdisplay -v`

#view complete setup - VG, PV, and LV

![verify the entire setup using vgdisplay to see VG PVs and LVs](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/4dd6d069-6099-48e0-ac16-72d958c086df)

`sudo lsblk`

![verify the entire setup using lsblk to see VG PVs and LVs](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/f2d03828-e1e7-49cb-84e8-3b49ad733fc4)

Use mkfs.ext4 to format the logical volumes with ext4 filesystem

`sudo mkfs -t ext4 /dev/webdata-vg/apps-lv`

`sudo mkfs -t ext4 /dev/webdata-vg/logs-lv`

![format the LV with ext4 filesystem](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/8ffee82f-4d2c-48dc-9304-03e2d417377c)

Create /var/www/html directory to store website files

`sudo mkdir -p /var/www/html`

Create /home/recovery/logs to store backup of log data

`sudo mkdir -p /home/recovery/logs`

Mount /var/www/html on apps-lv logical volume

`sudo mount /dev/webdata-vg/apps-lv /var/www/html/`

![mkdir to store website files and backup of log files then mount on LVs](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/8ae22416-5b63-47e0-890a-320c849bfd0f)

Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)

`sudo rsync -av /var/log/. /home/recovery/logs/`

Mount /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 15 above is very important)

`sudo mount /dev/webdata-vg/logs-lv /var/log`

Restore log files back into /var/log directory

`sudo rsync -av /home/recovery/logs/log/. /var/log`

Update /etc/fstab file so that the mount configuration will persist after restart of the server.
The UUID of the device will be used to update the /etc/fstab file;

`sudo blkid`

`sudo vi /etc/fstab`

Update /etc/fstab in this format using your own UUID and rememeber to remove the leading and ending quotes.

![get the UUIDs of app and log to update etc fstab](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/a5eebfa4-3f73-4ba7-b1c0-d9bbcfe8bf21)

![updating etc fstab using my UUIDs](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/3f162060-93f2-4011-82c9-b88f155cc51e)


Test the configuration and reload the daemon

`sudo mount -a`

`sudo systemctl daemon-reload`

![test config and reload daemon](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/9d16b441-f39d-470e-8708-b7fa8ec6eed6)


Verify your setup by running df -h, output must look like this:

![verify setup by runninh df -h](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/fa8f1692-f9d8-43ae-a958-8556f877fcfa)


## PREPARE THE DATABASE SERVER

Launch a second RedHat EC2 instance that will have a role – ‘DB Server’ Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/.

![db server 1](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/9f4302ab-5254-4d78-af00-27a8064d7a9e)

![db server 2](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/f0f4a5aa-ecfd-409e-8768-bf6565b687dd)

![db server 3](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/e5744512-3eb8-46ec-b0a5-c3e5f3eb81a2)

![db server 4](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/aa4bd1fd-f58a-4241-a085-407a322b406d)



##  Install WordPress on your Web Server EC2

Update the repository

`sudo yum -y update`

Install wget, Apache and it’s dependencies

`sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`

Start Apache

`sudo systemctl enable httpd`

`sudo systemctl start httpd`

To install PHP and it’s depemdencies

`sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`

`sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm`

`sudo yum module list php`

`sudo yum module reset php`

`sudo yum module enable php:remi-7.4`

`sudo yum install php php-opcache php-gd php-curl php-mysqlnd`

`sudo systemctl start php-fpm`

`sudo systemctl enable php-fpm`

`setsebool -P httpd_execmem 1`

Restart Apache

`sudo systemctl restart httpd`

confirm apache is up and running from browser

![apache working fine on browser](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/79972e64-b82e-43d1-9638-c5ab3489960d)

Download wordpress and copy wordpress to var/www/html

  `mkdir wordpress`
  
  `cd wordpress`
  
  `sudo wget http://wordpress.org/latest.tar.gz`
  
  `sudo tar xzvf latest.tar.gz`
  
  `sudo rm -rf latest.tar.gz`
  
  `cp wordpress/wp-config-sample.php wordpress/wp-config.php`
  
  `cp -R wordpress /var/www/html/`

  ![updating vi into wp config php](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/6d99312c-9ff4-4631-b0cc-85418525ac48)
  
Configure SELinux Policies

  `sudo chown -R apache:apache /var/www/html/wordpress`
  
  `sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R`
  
  `sudo setsebool -P httpd_can_network_connect=1`

  ![sudo chown -R](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/2aa3545e-fddf-445e-a463-cf3d78e8791b)

![1](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/b9fb438e-5661-4009-a128-560b880bdc2a)

![2](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/2cc87e80-0b7f-4cc9-85ff-2f484fa55783)

![3](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/80ccbdb7-c578-407b-ab0d-cfafbd9fc6b6)

![4](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/14c8c2b2-d310-429e-860e-c102852249f5)



  ## Install MySQL on your DB Server EC2

  `sudo yum update`
  
  `sudo yum install mysql-server`

  ![db server 5](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/4a246460-a56f-4c20-aed8-deb807c051b7)


Verify that the service is up and running by using sudo systemctl status mysqld, if it is not running, restart the service and enable it so it will be running even after reboot:

`sudo systemctl restart mysqld`

`sudo systemctl enable mysqld`

![db server 6](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/8ad59681-a510-4d04-b40d-c239751fe25f)


## Configure DB to work with WordPress

`sudo mysql`

`CREATE DATABASE wordpress;`

`CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';`

`GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';`

`FLUSH PRIVILEGES;`

`SHOW DATABASES;`

`exit`

![db server 7](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/3f25952b-6957-4443-af32-1e86ec28ba99)


## Configure WordPress to connect to remote database.

Do not forget to open MySQL port 3306 on DB Server EC2. For extra security, you shall allow access to the DB server ONLY from your Web Server’s IP address, so in the Inbound Rule configuration specify source as /32

![open mysql port 3306 on DB server access only from web server](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/5a170c00-af6b-4fdc-84d8-69b953b5f869)

Install MySQL client and test that you can connect from your Web Server to your DB server by using mysql-client

`sudo yum install mysql`

`sudo mysql -u admin -p -h <DB-Server-Private-IP-address>`

Verify if you can successfully execute command below and see a list of existing databases.

`SHOW DATABASES;` 

![db server 8](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/dc606d2a-1459-43e2-9dba-2c8498c16389)

Change permissions and configuration so Apache could use WordPress:
Enable TCP port 80 in Inbound Rules configuration for your Web Server EC2 (enable from everywhere 0.0.0.0/0 or from your workstation’s IP)
Try to access from your browser the link to your WordPress http://<Web-Server-Public-IP-Address>/wordpress/

![wordpress working on browser](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/b34d61c2-86dc-4609-91ab-e894e776a0d1)

![wordpress installed username and password set](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/e7c7905a-ea72-462c-be9d-30171a65cd54)

![wordpress fully active](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/25b28bcb-f8d3-4e39-9ddf-08530e73506d)



