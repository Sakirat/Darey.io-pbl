## AWS CLOUD SOLUTION FOR 2 COMPANY WEBSITES USING A REVERSE PROXY TECHNOLOGY

WARNING: This infrastructure set up is NOT covered by AWS free tier. Therefore, ensure to DELETE ALL the resources created immediately after finishing the project. Monthly cost may be shockingly high if resources are not deleted. Also, it is strongly recommended to set up a budget and configure notifications when your spendings reach a predefined limit. Watch this video to learn how to configure AWS budget.

We have been doing great work so far – implementing different Web Solutions and getting hands on experience with many great DevOps tools. In previous projects we used basic Infrastructure as a Service (IaaS) offerings from AWS such as EC2 (Elastic Compute Cloud) as rented Virtual Machines and EBS (Elastic Block Store), we have also learned how to configure Key pairs and basic Security Groups.

But the power of Clouds is not only in being able to rent Virtual Machines – it is much more than that. From now on, you will start gradually study different Cloud concepts and tools on example of AWS, but do not be worried, our knowledge will not be limited to only AWS specific concepts – overall principles are common across most of the major Cloud Providers (e.g., Microsoft Azure and Google Cloud Platform).

NOTE: The next few projects will be implemented manually. Before we begin to automate infrastructure in the cloud, it is very important that we can build the solution manually. Otherwise, programming your automation may become frustrating very quickly.

we will build a secure infrastructure inside AWS VPC (Virtual Private Cloud) network for a company (Choose an interesting name for it) that uses WordPress CMS for its main business website, and a Tooling Website (https://github.com/<your-name>/tooling) for their DevOps team. As part of the company’s desire for improved security and performance, a decision has been made to use a reverse proxy technology from NGINX to achieve this.

Cost, Security, and Scalability are the major requirements for this project. Hence, implementing the architecture designed below, ensure that infrastructure for both websites, WordPress and Tooling, is resilient to Web Server’s failures, can accommodate to increased traffic and, at the same time, has reasonable cost.

![image](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/772351da-2f84-499f-ac4b-42609ba45496)

Starting Off Your AWS Cloud Project

There are few requirements that must be met before you begin:

NOTE : As you proceed with configuration, ensure that all resources are appropriately tagged, for example:

Project: <Give your project a name>

Environment: <dev>

Automated: <No> (If you create a resource using an automation tool, it would be <Yes>)

### SET UP A VIRTUAL PRIVATE NETWORK (VPC)

![2 creating VPC](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/dcc5e5b8-b6fa-43e4-83fa-73cce99c9848)

![3 Enable DNS hostname](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/909664a9-b941-4eef-8dba-5bc4875a8536)

![4 create internet gateway and attach it to VPC](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/ce247705-fb54-4182-8798-601f0da87c6b)

![5 creating 2 public and 4 private subnets distributed into 2AZs](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/d9954bbf-7386-4dc1-b0c0-6ddd4cbd5cce)

![6 the 6 subnets private n Public distributed accross 2AZs](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/474b42cc-7bc9-40b4-89a8-6a1b00add7be)

![7 create 2 route tables 1 for public and 1 private subnets](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/e786eba4-2e72-43f1-a120-a304f5097c0b)

![8 route table subnet association](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/d86d6811-041b-4d6e-b978-8623e914ef89)

![9 edit route of public route table](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/a601b44f-fb12-412c-8ca0-cbd6b35064a8)

![10 Elastic IP created for NAT gateway](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/cc7f3200-423c-4866-83f3-f91635795564)

![11 create NAT gateway](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/67dff6f1-b176-492b-aded-09b41fd6faec)

![12 editing the routes of the private route table](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/68497def-83e0-4846-97a6-ab26658fba0f)

![13 creating SG for external ALB](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/3814c923-d1e9-4123-9407-63e46025fdb2)

![14 create SG for Bastion allowing SSH access from my IP](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/e93be061-ae5d-4541-afa6-b7f0299faae8)

![15 SG for Nginx reverse proxy server](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/b87c1106-33f9-4153-9b6f-6bdce93d9c3f)

![16 SG for ACS internal ALB](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/f738987c-8e6d-45cb-adda-5fe59c90fb26)

![17 SG for Webservers](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/8ef6b07f-48fb-4ca5-a990-57359089808f)

![18 SG for DataLayer](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/bdbb6b89-958e-4e61-88d0-69aad12e422c)

![19 SG for DataLayer](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/f50c3bcf-a1d4-41b5-84fd-eeb3f4e96fac)

![20 SGs at a glance](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/ebde683f-634d-449a-92ed-f608e15ce5ab)

![21 setting up a hostedzone in aws and linking to a domain boughy on namecheap](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/42870c4a-aa16-4672-8781-38a6de6cf435)

![22 request a certificate from AWS certificate manager](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/a68c0618-cc4d-4ad1-913b-64b8d6d53156)

![23 requesting a public certificate for my domain on AWS](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/b36ca0ec-d800-4d0c-b344-fd5334f8016c)

![24 CERTIFICATE CREATED IN AWS](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c85c18ed-4f8c-4390-918f-5de661e09b52)

![25 set up EFS](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/e2f9782d-4ae3-48e7-826d-46775e2f8301)

![26 file system](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/5b8b2389-ffc0-4fcb-aa0f-2f0c0e3edf8d)

![27 mount targets for EFS using private subnet 1 and private subnet 2 in 2AZs  SG is that of datalayer](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/28840c7a-2c68-49bb-bf98-2f1c275605ca)

![28 creating EFS access point for word press](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/e1931de2-b244-4f43-8428-fc6a98a1b26d)

![29 2 accesspoints created in EFS for tooling and wordpress werbservers](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/3df75828-b49a-47ca-b22d-1939cacf01eb)

![30 KMS to create key](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/27d38d56-d06d-47d1-be7e-15dc3d816d92)

![31 ACS KMSkey](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c6a6de9a-f5f6-46b9-88ea-d08e7a979e5c)

![32 create DB subnet group](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/80aaa560-3e99-4207-8009-e6ff29ed2c1b)

![33 RDS mysql database created](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/1a279267-1a03-43b9-8b73-58fa49bc7fe6)

![34 using Termius to connect to the 3 instances](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/aa421fff-0d01-4ed3-bb33-500330d2faaa)

![35 installing epel remi release  wget vim python 3 telnet htop git mysql net-tools and chrony on Bastion server](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/8f8dfed1-7dde-470d-b62f-6c83d989cbee)

![36 installing epel remi vim python3 telnet htop git mysql net-tools and chrony](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/cd0b6e21-81ea-49bf-9fc6-d79be2961012)

![37 generating certificate for nginx](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/85f1117a-17c2-4678-87a9-1841bc92e8b7)

![38 creating the ssl CERTIFICATE for webserver](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/05dd12a9-f5c7-48de-b4cf-914099afa54f)

![38B sudo vi for SSL CERT AND KEY](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/de71e586-2313-4dc6-98b9-5347cb065194)

![39 create AMIs for the 3 instances nginx webserver and bastion](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/f30025bf-5b9d-45ec-99fe-4b2cd7fe5f1d)

![39b](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/6501b03a-0fd6-4beb-b7c5-2f536ff8fdc8)

![40 Target group for Nginx](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/07ddd56c-faa7-41be-a4e3-16a4fea88a10)

![41 target groups for servers that will behind load balancers](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/7a5cf359-e811-414c-b575-07f49509299d)

![42 configure load balancer1](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/d929acaf-ab72-4a04-9add-6a96b207b916)

![42 configure load balancer2](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/d782ff61-b135-40b5-9ac1-19b251053799)

![42 configure load balancer3](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/7492af37-1efa-4936-b4d7-001cc22812d2)

![42 configure load balancer4](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/ba31e9c7-08ae-4eb9-a804-a7f80e1d34eb)

![42 configure load balancer5](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/5b8b0d03-9512-43af-b3fb-c7739d717e75)

![43 internal and external load balancers](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/ba0ff8fc-d4e5-40d5-9274-416826b793bd)

![44 adding listeners to the ALB](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/77ffd4c9-0944-4dee-8506-2e6b42ac6c29)

![45 adding listeners to the ALB](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/d2d20af9-2082-4e50-9b08-e495d34baa90)

![46 Int ALB structure](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/f43ea7b6-5017-4763-8f68-deb3a5dc74ca)

![47 Launch Template](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/fe2bf7a9-2d9a-478c-a533-8521f2d9f6e1)

![48 user data for bastion launch template](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/aafa866c-f581-4869-b5f8-beba54402b02)

![49 user data for Nginx](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/5ca89165-d205-4b42-940c-b38d57041f18)

![50 userdata for wordpress](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/9da5ff55-dcf5-43e8-b71a-9a31bd63ebd3)

![51 Launch templates for all servers](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c8459811-40ac-47d4-baaa-71426e7fcfe6)

![52 create ASG](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/02bd2a79-6f4e-4364-abab-29bb3d7fbedd)

![53 from the bastion on VSC sign into mysql with RDS endpoint username and password create databases](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/fe1ff0ba-c46f-4673-9c5a-918c1a9228b3)

![54 target tracking ASG set to 90](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/75f2c117-0988-4976-b1bc-b7e470e8c624)

![55 ASG for our 4 resources](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/7b736938-f31f-49fb-a011-d192cc75f4f4)

![56 A records created in the Route 53 hosted zone](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/dfdb154d-cfef-49fd-be59-e776517e3655)





















