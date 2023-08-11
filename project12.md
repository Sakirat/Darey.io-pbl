## ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)

In this project we will continue working with ansible-config-mgt repository and make some improvements of our code. Now we need to refactor our Ansible code, create assignments, and learn how to use the imports functionality. Imports allow to effectively re-use previously created playbooks in a new playbook – it allows you to organize your tasks and reuse them when needed.

### Code Refactoring
Refactoring is a general term in computer programming. It means making changes to the source code without changing expected behaviour of the software. The main idea of refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity, add proper comments without affecting the logic.
In this case, we will move things around a little bit in the code, but the overal state of the infrastructure remains the same.

### Step 1 – Jenkins job enhancement
Before we begin, let us make some changes to our Jenkins job – now every new change in the codes creates a separate directory which is not very convenient when we want to run some commands from one place. Besides, it consumes space on Jenkins serves with each subsequent change. Let us enhance it by introducing a new Jenkins project/job – we will require Copy Artifact plugin.

we go to the Jenkins-Ansible server and create a new directory called ansible-config-artifact – we will store there all artifacts after each build.

`sudo mkdir /home/ubuntu/ansible-config-artifact`

Change permissions to this directory, so Jenkins could save files there 

`chmod -R 0777 /home/ubuntu/ansible-config-artifact`

![initial starting codes](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/5db7819d-9fd6-4a84-bedb-459838fb9eab)


Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on Available tab search for Copy Artifact and install this plugin without restarting Jenkins

Create a new Freestyle project (you have done it in Project 9) and name it save_artifacts.

This project will be triggered by completion of your existing ansible project. Configure it accordingly:

![job triggered on Jenkins](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/f6214e2e-73bd-429d-b1f1-45e55bc73ee1)

![jenkins console output on ansible](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/2ddf6aa0-99c6-41cc-9b83-8e2f226c3e48)

 You can configure number of builds to keep in order to save space on the server, for example, you might want to keep only last 2 or 5 build results. You can also make this change to your ansible job.

 The main idea of save_artifacts project is to save artifacts into /home/ubuntu/ansible-config-artifact directory. To achieve this, create a Build step and choose Copy artifacts from other project, specify ansible as a source project and /home/ubuntu/ansible-config-artifact as a target directory.

 Test your set up by making some change in README.MD file inside your ansible-config-mgt repository (right inside master branch).
If both Jenkins jobs have completed one after another – you shall see your files inside /home/ubuntu/ansible-config-artifact directory and it will be updated with every commit to your master branch.

![project save artifacts successful build](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/28e05097-0829-411f-9757-76cce7362fd6)

Now your Jenkins pipeline is more neat and clean.

### STEP 2: REFACTOR ANSIBLE CODE BY IMPORTING OTHER PLAYBOOKS INTO SITE.YML

Before starting to refactor the codes, ensure that you have pulled down the latest code from master (main) branch, and created a new branch, name it refactor.

![git checkout new branch refactor](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/05fef990-78b6-409c-a390-409a6785480e)

DevOps philosophy implies constant iterative improvement for better efficiency – refactoring is one of the techniques that can be used, but you always have an answer to question "why?". Why do we need to change something if it works well?

In Project 11 you wrote all tasks in a single playbook common.yml, now it is pretty simple set of instructions for only 2 types of OS, but imagine you have many more tasks and you need to apply this playbook to other servers with different requirements. In this case, you will have to read through the whole playbook to check if all tasks written there are applicable and is there anything that you need to add for certain server/OS families. Very fast it will become a tedious exercise and your playbook will become messy with many commented parts. Your DevOps colleagues will not appreciate such organization of your codes and it will be difficult for them to use your playbook.

Most Ansible users learn the one-file approach first. However, breaking tasks up into different files is an excellent way to organize complex sets of tasks and reuse them.

Let see code re-use in action by importing other playbooks.

Within playbooks folder, create a new file and name it site.yml – This file will now be considered as an entry point into the entire infrastructure configuration. Other playbooks will be included here as a reference. In other words, site.yml will become a parent to all other playbooks that will be developed. Including common.yml that you created previously. Dont worry, you will understand more what this means shortly.

![creating site yml inside the playbook folder](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/4fd2890e-2e57-465d-816e-18e04942392f)


Create a new folder in root of the repository and name it static-assignments. The static-assignments folder is where all other children playbooks will be stored. This is merely for easy organization of your work. It is not an Ansible specific concept, therefore you can choose how you want to organize your work. You will see why the folder name has a prefix of static very soon. For now, just follow along.

Move common.yml file into the newly created static-assignments folder.

![move common to static](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/821c6087-c64d-46e1-ad5c-f22138f80e32)

![import common yml playbook to static to have the 5 enviroments](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/9c04f8e2-1a26-4d95-adda-169cb8bf5464)

Inside site.yml file, import common.yml playbook.

---
- hosts: all
- import_playbook: ../static-assignments/common.yml

The code above uses built in import_playbook Ansible module. Your folder structure should look like this;

├── static-assignments
│   └── common.yml
├── inventory
    └── dev
    └── stage
    └── uat
    └── prod
└── playbooks
    └── site.yml

Run ansible-playbook command against the dev environment
Since you need to apply some tasks to your dev servers and wireshark is already installed – you can go ahead and create another playbook under static-assignments and name it common-del.yml. 

![updating common to common-del in site yml](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/e4b9bfc2-0c7a-45a5-af3f-f3a2f9b01791)


In this playbook, configure deletion of wireshark utility.

---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    yum:
      name: wireshark
      state: removed

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    apt:
      name: wireshark-qt
      state: absent
      autoremove: yes
      purge: yes
      autoclean: yes

    ![updating the wireshark deletion](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/bfa9daa6-043c-4af6-9aad-940bdfb0795d)


   update site.yml with - import_playbook: ../static-assignments/common-del.yml instead of common.yml and run it against dev servers:

`cd /home/ubuntu/ansible-config-mgt/`

`ansible-playbook -i inventory/dev.yml playbooks/site.yaml`

Make sure that wireshark is deleted on all the servers by running wireshark --version. Now you have learned how to use import_playbooks module and you have a ready solution to install/delete packages on multiple servers with just one command.

### Step 3 – Configure UAT Webservers with a role ‘Webserver’

We have our nice and clean dev environment, so let us put it aside and configure 2 new Web Servers as uat. We could write tasks to configure Web Servers in the same playbook, but it would be too messy, instead, we will use a dedicated role to make our configuration reusable.

Launch 2 fresh EC2 instances using RHEL 8 image, we will use them as our uat servers, so give them names accordingly – Web1-UAT and Web2-UAT.
Tip: Do not forget to stop EC2 instances that you are not using at the moment to avoid paying extra. For now, you only need 2 new RHEL 8 servers as Web Servers and 1 existing Jenkins-Ansible server up and running.

![set up 2 new instances web1 web2 UAT](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/8800f3f7-d2b0-4117-939c-db2f146c01bc)

To create a role, you must create a directory called roles/, relative to the playbook file or in /etc/ansible/ directory.
There are two ways how you can create this folder structure:

Use an Ansible utility called ansible-galaxy inside ansible-config-mgt/roles directory (you need to create roles directory upfront) 

`mkdir roles`
`cd roles`
`ansible-galaxy init webserver`

Create the directory/files structure manually

Ww can choose either way, but since you store all your codes in GitHub, it is recommended to create folders and files there rather than locally on Jenkins-Ansible server.

The entire folder structure should look like below, but if you create it manually – you can skip creating tests, files, and vars or remove them if you used ansible-galaxy

└── webserver
    ├── README.md
    ├── defaults
    │   └── main.yml
    ├── files
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    ├── templates
    ├── tests
    │   ├── inventory
    │   └── test.yml
    └── vars
        └── main.yml

After removing unnecessary directories and files, the roles structure should look like this    

└── webserver
    ├── README.md
    ├── defaults
    │   └── main.yml
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    └── templates


    ![creating the files and folders in webserver](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/0ce87443-f923-4ef7-ad2a-9972d057be72)


Update your inventory ansible-config-mgt/inventory/uat.yml file with IP addresses of your 2 UAT Web servers. Ensure you are using ssh-agent to ssh into the Jenkins-Ansible instance just as you have done in project 11;

  [uat-webservers]
<Web1-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user' 

<Web2-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user' 

![update the UAT yml file with web1 and web2 ip addresses](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/6b0ece14-3c47-4824-a5d9-55df698f4a44)


In /etc/ansible/ansible.cfg file uncomment roles_path string and provide a full path to your roles directory roles_path    = /home/ubuntu/ansible-config-mgt/roles, so Ansible could know where to find configured roles.

![sudo vi etc ansible config roles](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c2e41f57-4894-480c-8622-97d666d79125)

It is time to start adding some logic to the webserver role. Go into tasks directory, and within the main.yml file, start writing configuration tasks to do the following:

Install and configure Apache (httpd service)
Clone Tooling website from GitHub https://github.com/<your-name>/tooling.git.
Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers.
Make sure httpd service is started
Your main.yml may consist of following tasks

---
- name: install apache
  become: true
  ansible.builtin.yum:
    name: "httpd"
    state: present

- name: install git
  become: true
  ansible.builtin.yum:
    name: "git"
    state: present

- name: clone a repo
  become: true
  ansible.builtin.git:
    repo: https://github.com/<your-name>/tooling.git
    dest: /var/www/html
    force: yes

- name: copy html content to one level up
  become: true
  command: cp -r /var/www/html/html/ /var/www/

- name: Start service httpd, if not started
  become: true
  ansible.builtin.service:
    name: httpd
    state: started

- name: recursively remove /var/www/html/html/ directory
  become: true
  ansible.builtin.file:
    path: /var/www/html/html
    state: absent

  ![install apache clone tooling website](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/425fedbb-902f-495d-be7a-d7335812ef1a)


  
### Step 4 – Reference ‘Webserver’ role

Within the static-assignments folder, create a new assignment for uat-webservers uat-webservers.yml. This is where you will reference the role.

![in static assignment create new assignment](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/d0e5a693-73b3-400d-a395-ef2d70cc1de8)


---
- hosts: uat-webservers
  roles:
     - webserver

Remember that the entry point to our ansible configuration is the site.yml file. Therefore, you need to refer your uat-webservers.yml role inside site.yml. So, we should have this in site.yml

---
- hosts: all
- import_playbook: ../static-assignments/common.yml

- hosts: uat-webservers
- import_playbook: ../static-assignments/uat-webservers.yml

### Step 5 – Commit & Test

Commit your changes, create a Pull Request and merge them to master branch, make sure webhook triggered two consequent Jenkins jobs, they ran successfully and copied all the files to your Jenkins-Ansible server into /home/ubuntu/ansible-config-mgt/ directory.
Now run the playbook against your uat inventory and see what happens:

![merging changes to main branch](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/b4dd3d87-77d6-4fd4-b6f5-4920ed55ffe9)

![confirm changes to refactor branch reflects on github](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/8ce718d7-74d4-4ef3-a383-c2003a0e3752)


![pull request successfully merged and closed on github](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/a3d534b2-c422-4480-9cb9-dd5b4d3627e8)


You should be able to see both of your UAT Web servers configured and you can try to reach them from your browser:

http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php

or

http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php

Our Ansible architecture now looks like this:

![image](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/dade3b8d-48d3-42eb-b378-e6b1376ac9aa)

In Project 13, we would see the difference between Static and Dynamic assignments.

Congratulations! You have learned how to deploy and configure UAT Web Servers using Ansible imports and roles!


