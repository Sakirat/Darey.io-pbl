## ANSIBLE CONFIGURATION MANAGEMENT – AUTOMATE PROJECT 7 TO 10

We have been implementing some interesting projects up untill now, and that is awesome

In Projects 7 to 10 you had to perform a lot of manual operations to seet up virtual servers, install and configure required software, deploy your web application.

This Project will make you appreciate DevOps tools even more by making most of the routine tasks automated with Ansible Configuration Management, at the same time you will become confident at writing code using declarative language such as YAML.

### Ansible Client as a Jump Server (Bastion Host)

A Jump Server (sometimes also referred as Bastion Host) is an intermediary server through which access to internal network can be provided. If you think about the current architecture you are working on, ideally, the webservers would be inside a secured network which cannot be reached directly from the Internet. That means, even DevOps engineers cannot SSH into the Web servers directly and can only access it through a Jump Server – it provide better security and reduces attack surface.

On the diagram below the Virtual Private Network (VPC) is divided into two subnets – Public subnet has public IP addresses and Private subnet is only reachable by private IP addresses.

![image](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/efe0485c-3f36-4ffe-a6eb-659371cc7e36)


## INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE

Update Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.


![setting up an instance for Jenkins-Ansible](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/3cbb222c-34ce-464e-88d2-e2a7327170eb)


In your GitHub account create a new repository and name it ansible-config-mgt.

Install Ansible

`sudo apt update`

`sudo apt install ansible`

![installing ansible on ubuntu instance](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c8148d9a-f62e-443c-a8ed-b85704bc52fe)


Check your Ansible version by running 

`ansible --version`


![checking ansible version running](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/4bd37d28-6e21-43e9-9a2e-057af0e9658a)


Configure Jenkins build job to save your repository content every time you change it – this will solidify your Jenkins configuration skills acquired in Project 9.

Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.


![pointing Jenkins to Github repo](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/bd17c916-5a50-4c16-a992-b2be6c91099b)


![creating a new repo in Github](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/8b0ed38e-35bf-4d03-a9a2-caa1bc7b7dd6)


Configure Webhook in GitHub and set webhook to trigger ansible build.


![setting webhook for ansible-config-mgt repo in Github](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/2ca14b17-27ad-4828-a667-d34d1b9ecaa4)



![JenkinsProject titled ansible with sample builds](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c03ae3f1-33b6-4c7d-b978-45c2fdd3cab7)


Configure a Post-build job to save all (**) files, like you did it in Project 9.

Test your setup by making some change in README.MD file in master branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder


![viewing the changes made to README file from Jenkins Terminal](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/e72e4ee3-8e79-4799-825a-bb161363c1aa)


`ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/`

Note that we are to Trigger Jenkins project execution only for /main (master) branch.

our setup will look like this

![image](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/15083dde-8ecb-4e4b-ac9c-fb490af044ce)

Every time you stop/start your Jenkins-Ansible server – you have to reconfigure GitHub webhook to a new IP address, in order to avoid it, it makes sense to allocate an Elastic IP to your Jenkins-Ansible server (you have done it before to your LB server in Project 10). Note that Elastic IP is free only when it is being allocated to an EC2 Instance, so do not forget to release Elastic IP once you terminate your EC2 Instance.

## Prepare your development environment using Visual Studio Code

First part of ‘DevOps’ is ‘Dev’, which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable – you need an Integrated development environment (IDE) or Source-code Editor. There is a plethora of different IDEs and Source-code Editors for different languages with their own advantages and drawbacks, you can choose whichever you are comfortable with, but we recommend one free and universal editor that will fully satisfy your needs

After you have successfully installed VSC, configure it to connect to your newly created GitHub repository.


![VScode prep](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/2b1aa591-9984-4c50-bdd1-8341d8fb18ee)


![checking the branch we are working on VScode using git bash](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/5187ac20-febf-4f4c-9aa3-f27997935f45)


Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance

`git clone <ansible-config-mgt repo link>`


![cloned from github to VScode](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/7400783e-d321-46bd-befe-a821e13956f7)

![checkout to main and check git status that all is good](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/9f6bdb46-0fa7-45a6-a142-f9ccd35b4554)


## BEGIN ANSIBLE DEVELOPMENT

In your ansible-config-mgt GitHub repository, create a new branch that will be used for development of a new feature.

Tip: Give your branches descriptive and comprehensive names, for example, if you use Jira or Trello as a project management tool – include ticket number (e.g. PRJ-145) in the name of your branch and add a topic and a brief description what this branch is about – a bugfix, hotfix, feature, release (e.g. feature/prj-145-lvm)

Checkout the newly created feature branch to your local machine and start building your code and directory structure


![git push origin project-11](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/38b62dfa-715d-4f32-97c6-13a820f74425)


![using git status add and commit for all changes made to files in VSC](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/7d1cff19-6f55-4392-87b7-e018bb185e15)


![git status to confirm all is committed](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/4c2eee3c-86aa-4766-b377-159985e2a1c5)


Create a directory and name it playbooks – it will be used to store all your playbook files.

Create a directory and name it inventory – it will be used to keep your hosts organised.

Within the playbooks folder, create your first playbook, and name it common.yml

Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.


![adding dev uat prod and staging files to inventory](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/1ba200ef-5cba-4de2-a295-35ec6e12aa63)


### Set up an Ansible Inventory

An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory

Save below inventory structure in the inventory/dev file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.

Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host – for this you can implement the concept of ssh-agent. Now you need to import your key into ssh-agent:

`eval `ssh-agent -s``

`ssh-add <path-to-private-key>`

Confirm the key has been added with the command below, you should see the name of your key

`ssh-add -l`


![ssh-add -l](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/3fc12413-a037-40ef-a521-4fe9df6c2ccb)


Now, ssh into your Jenkins-Ansible server using ssh-agent

`ssh -A ubuntu@public-ip`


![SSH into Jenkins-Ansible server using ssh-agent](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/3074130d-9d57-4c42-a23c-f529122fd6d0)



![setting up SSH agent on gitbash terminal](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/bdcf0681-c94e-486d-990a-ed97ccc2475d)


Also notice, that your Load Balancer user is ubuntu and user for RHEL-based servers is ec2-user.

Update your inventory/dev.yml file with this snippet of code:

[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'


![providing nfs db and webserver IPs in VSC](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/63895409-d605-4693-852f-30e6bd835d37)


## CREATE A COMMON PLAYBOOK

It is time to start giving Ansible the instructions on what you needs to be performed on all servers listed in inventory/dev.

In common.yml playbook you will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

Update your playbooks/common.yml file with following code:

---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest


      ![updating the common yml file](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/9875d03d-9d2c-4fcf-a24f-2444d4735278)


 Examine the code above and try to make sense out of it. This playbook is divided into two parts, each of them is intended to perform the same task: install wireshark utility (or make sure it is updated to the latest version) on your RHEL 8 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 8 and apt for Ubuntu.

 ### Update GIT with the latest code

Now all of your directories and files live on your machine and you need to push changes made locally to GitHub.

In the real world, you will be working within a team of other DevOps engineers and developers. It is important to learn how to collaborate with help of GIT. In many organisations there is a development rule that do not allow to deploy any code before it has been reviewed by an extra pair of eyes – it is also called "Four eyes principle".

Now you have a separate branch, you will need to know how to raise a Pull Request (PR), get your branch peer reviewed and merged to the master branch.


![pull request successfully merged and closed](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/121d3315-cf31-4677-9243-e57986a03264)


Commit your code into GitHub:

use git commands to add, commit and push your branch to GitHub.

`git status`

`git add <selected files>`

`git commit -m "commit message"`

Create a Pull request (PR)

Wear a hat of another developer for a second, and act as a reviewer.

If the reviewer is happy with your new feature development, merge the code to the master branch.

Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.

Once your code changes appear in master branch – Jenkins will do its job and save all the files (build artifacts) to /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.


![Jenkins saved all files build artifacts in a directory in jenkins-ansible server](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/0d3b8e91-49fd-442b-9234-cf456d8af710)


![Update reflecting in Jenkins](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/175ef2a2-2e93-41d0-b0f0-2119eb66c9bc)


### RUN FIRST ANSIBLE TEST

Now, it is time to execute ansible-playbook command and verify if your playbook actually works:

`cd ansible-config-mgt`

`ansible-playbook -i inventory/dev.yml playbooks/common.yml`


![running first ansible test](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/a3466fc0-cdd3-4088-a6e9-a338aa5368da)


You can go to each of the servers and check if wireshark has been installed by running 

`which wireshark`

or 

`wireshark --version`


![checking wireshack version running on webserver](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/85de36c7-f089-4dd5-b1bc-f67814b9a027)


Your updated with Ansible architecture now looks like this:


![image](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/174c58ef-4199-42e9-aac0-d8e3d104f67c)


Congratulations!!!

we have just automated our routine tasks by implementing our first Ansible project! There is more exciting projects ahead, so lets keep it moving!





