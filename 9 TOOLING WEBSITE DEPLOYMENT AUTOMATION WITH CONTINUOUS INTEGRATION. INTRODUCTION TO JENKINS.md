## TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS

In previous Project 8 we introduced horizontal scalability concept, which allow us to add new Web Servers to our Tooling Website and you have successfully deployed a set up with 2 Web Servers and also a Load Balancer to distribute traffic between them. If it is just two or three servers – it is not a big deal to configure them manually. Imagine that you would need to repeat the same task over and over again adding dozens or even hundreds of servers.

DevOps is about Agility, and speedy release of software and web solutions. One of the ways to guarantee fast and repeatable deployments is Automation of routine tasks.

In this project we are going to start automating part of our routine tasks with a free and open source automation server – Jenkins. It is one of the most popular CI/CD tools, it was created by a former Sun Microsystems developer Kohsuke Kawaguchi and the project originally had a named “Hudson”.

According to Circle CI, Continuous integration (CI) is a software development strategy that increases the speed of development while ensuring the quality of the code that teams deploy. Developers continually commit code in small increments (at least daily, or even several times a day), which is then automatically built and tested before it is merged with the shared repository.

In our project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub https://github.com/<yourname>/tooling will be automatically be updated to the Tooling Website.


### Task

Enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server.

Here is how your updated architecture will look like upon competition of this project:

![image](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/03a4423a-b19b-468f-904b-1b677111df57)


### INSTALL AND CONFIGURE JENKINS SERVER

Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it “Jenkins”

![new instance for Jenkins](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/f6a2d935-5014-4d5f-bdb8-ed1c45d71f60)


Install JDK (since Jenkins is a Java-based application)

`sudo apt update`

`sudo apt install default-jdk-headless`

### Install Jenkins

`curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null`
  
`sudo apt-get update`

`sudo apt-get install jenkins`

![installing Jenkins](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/37a31ff2-62d7-450f-884a-af54f5932413)


Make sure Jenkins is up and running

`sudo systemctl status jenkins`

![make sure Jenkins is up and running](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/57e791e0-173a-4a12-be2b-59a77242f5d7)


By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group

Perform initial Jenkins setup.

From your browser access http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080

You will be prompted to provide a default admin password

![initial setup on Jenkins from browser](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/39ca9093-8bb9-4bc7-9915-110d21663e13)


Retrieve it from your server:

`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

Then you will be asked which plugins to install – choose suggested plugins.

![choose plugin to install](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/de15f4b0-3856-495a-89be-32430afc2a62)


Once plugins installation is done – create an admin user and you will get your Jenkins server address.

![create admin user in Jenkins](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/b586addd-08d3-4c6c-b52f-88540b76b8d9)


The installation is completed!

![Jenkins ready to use](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/b9b6e70c-ee5c-4bcc-b75a-d0e047cced84)


### Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks

In this part, you will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will will be triggered by GitHub webhooks and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.

![fork dareyIO tooling repo and add the webhook](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/8d61f687-0697-4050-8f42-dce5fa36bef0)


#### Enable webhooks in your GitHub repository settings

Go to Jenkins web console, click “New Item” and create a “Freestyle project”

![select new item in Jenkins](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c7c11752-8ff9-4816-ac0f-fbda0d57c6c2)


To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself

![copy the tooling repo link](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/d0672ed4-4007-418a-96dd-4f4b66a4b232)


In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

![connected to the tooling project on jenkins](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/0c922b9c-b1d2-4ed2-bb2d-ce05e5a2099e)


Save the configuration and let us try to run the build. For now we can only do it manually.

![adding github webhook](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/04260687-733a-48c6-b4ff-c75ccdce7c82)


![2 configuration set for Jenkins](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/a499c764-87e8-420f-bfab-ade1e3175088)


Click “Build Now” button, if you have configured everything correctly, the build will be successfull and you will see it under #1

![build successful and showing under #1](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/cc38e8f7-6a65-4a10-b20a-228050445e41)


You can open the build and check in “Console Output” if it has run successfully.

![open build](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c5efb0eb-8628-40b5-99cf-e851d0c6b98e)


![console output](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c4936c47-a77d-4a44-ba84-253c1680369d)


If so – congratulations! You have just made your very first Jenkins build!

But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.

Click “Configure” your job/project and add these two configurations

Configure triggering the job from GitHub webhook:

Configure “Post-build Actions” to archive all the files – files resulted from a build are called “artifacts”.

![post build actions in Jenkins](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/061b3b86-db42-4348-8d31-532bbaaa9853)


Now, go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.

You will see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on Jenkins server.

You have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

By default, the artifacts are stored on Jenkins server locally

`ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/`

![listing the artifacts on the Jenkins Ubuntu Server](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/2cd4b3bd-fae7-42a6-b64d-c0d2e6948cb6)


## CONFIGURE JENKINS TO COPY FILES TO NFS SERVER VIA SSH

Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.

Jenkins is a highly extendable application and there are 1400+ plugins available. We will need a plugin that is called “Publish Over SSH”.

Install “Publish Over SSH” plugin.

![available plugin publish over SSH](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/02f1c93d-ae38-4b40-98d8-b05fa2ea1a11)


On main dashboard select “Manage Jenkins” and choose “Manage Plugins” menu item.

On “Available” tab search for “Publish Over SSH” plugin and install it 

Configure the job/project to copy artifacts over to NFS server.

On main dashboard select “Manage Jenkins” and choose “Configure System” menu item.

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)

Arbitrary name

Hostname – can be private IP address of your NFS server

Username – ec2-user (since NFS server is based on EC2 with RHEL 8)

![configure SSH Plugin](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/3923cb8f-5d63-4b9f-af2b-a6c1cdf127f1)

Remote directory – /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server
Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.

Save the configuration, open your Jenkins job/project configuration page and add another one “Post-build Action”

Configure it to send all files produced by the build into our previously define remote directory. In our case we want to copy all files and directories – so we use **.

If you want to apply some particular pattern to define which files to send – use this syntax.

Save this configuration and go ahead, change something in README.MD file in your GitHub Tooling repository.

Webhook will trigger a new job and in the “Console Output” of the job you will find something like this:

![update on git ref;ecting in Jenkins trigerred a second build](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/9fe91466-2bb9-4b99-af62-bc325afecc35)



To make sure that the files in /mnt/apps have been updated – connect via SSH/Putty to your NFS server and check README.MD file

`cat /mnt/apps/README.md`

![cat readme md](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/e35d5625-4089-4f11-8efb-589184a7a0f1)

![image](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/f9f83adb-8d26-4415-9614-b21ed20e8e1f)


If you see the changes you had previously made in your GitHub – the job works as expected.

Congratulations!

You have just implemented your first Continuous Integration solution using Jenkins CI. Watch out for advanced CI configurations in upcoming projects.
