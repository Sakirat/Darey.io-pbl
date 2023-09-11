## AUTOMATE INFRASTRUCTURE WITH IAC USING TERRAFORM PART 1

After you have built AWS infrastructure for 2 websites manually, it is time to automate the process using Terraform.

Let us start building the same set up with the power of Infrastructure as Code (IaC)

#### Tips:

#### The secrets of writing quality Terraform code

The secret recipe of a successful Terraform projects consists of:

Your understanding of your goal (desired AWS infrastructure end state)

Your knowledge of the IaC technology used (in this case – Terraform)

Your ability to effectively use up to date Terraform documentation

Ensure that every resource is tagged using multiple key-value pairs. You will see this in action as we go along.

Try to write reusable code, avoid hard coding values wherever possible. 

For easier authentication configuration – use AWS CLI with aws configure command.

Create an S3 bucket to store Terraform state file. You can name it something like <yourname>-dev-terraform-bucket (Note: S3 bucket names must be unique unique within a region partition, you can read about S3 bucken naming in this article). We will use this bucket from next project onwards.

![1 creating an S3 bucket](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/470e7d68-0e87-46b3-8618-60b92e73c1a1)


When you have configured authentication and installed boto3, make sure you can programmatically access your AWS account by running following commands in >python:

![2 install python SDK boto3](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/7fa47584-de78-4416-b627-63733aace931)

![3 listing my bucket from workstation](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/3527187d-7090-47a6-825e-a5055c49b8fd)



### VPC | SUBNETS | SECURITY GROUPS

Let us create a directory structure

Open your Visual Studio Code and:

Create a folder called PBL

Create a file in the folder, name it main.tf

![4 creating a new folder in VSC name it PBL touch main tf file](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/627fddd3-48b3-481e-8457-bcadfc59cef1)


Your setup should look like this.

Provider and VPC resource section

Set up Terraform CLI as per this instruction.

![5 setting up terraform CLI](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/e36b1dd6-3ba9-45e9-9fb7-fa8b8eb5bf54)


Add AWS as a provider, and a resource to create a VPC in the main.tf file.

Provider block informs Terraform that we intend to build infrastructure within AWS.

Resource block will create a VPC.

provider "aws" {
  region = "eu-central-1"
}

# Create VPC
resource "aws_vpc" "main" {
  cidr_block                     = "172.16.0.0/16"
  enable_dns_support             = "true"
  enable_dns_hostnames           = "true"
  enable_classiclink             = "false"
  enable_classiclink_dns_support = "false"
}

You can change the configuration above to create your VPC in other region that is closer to you. The same applies to all configuration snippets that will follow.

The next thing we need to do, is to download necessary plugins for Terraform to work. These plugins are used by providers and provisioners. At this stage, we only have provider in our main.tf file. So, Terraform will just download plugin for AWS provider.
Lets accomplish this with terraform init command as seen in the below demonstration.

![6 running terraform init](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/fa063ef8-ce00-4886-98df-37570af400b3)

![7 newly created files as a result of initialisation](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/71d95572-6702-4971-ac6a-d758dc4ac966)


#### Observations:

Notice that a new directory has been created: .terraform\.... This is where Terraform keeps plugins. Generally, it is safe to delete this folder. It just means that you must execute terraform init again, to download them.
Moving on, let us create the only resource we just defined. aws_vpc. But before we do that, we should check to see what terraform intends to create before we tell it to go ahead and create it.

Run 

`terraform plan`

![8 terraform plan command](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/1735b761-4def-4d39-8d9a-64166b58ae3f)

![9 terraform plan command 2](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/eacafd25-7e94-4c1e-a4e5-a1be8dca8ea8)


Then, if you are happy with changes planned, execute 

`terraform apply`

![10 terraform apply command](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/42000bf7-8a70-4411-a345-5cad2469880b)

![11 terrafrom apply command 2](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/fa837ec9-85cf-43aa-a4f2-c7777ebcbe93)

#### Observations:

A new file is created terraform.tfstate This is how Terraform keeps itself up to date with the exact state of the infrastructure. It reads this file to know what already exists, what should be added, or destroyed based on the entire terraform code that is being developed.
If you also observed closely, you would realize that another file gets created during planning and apply. But this file gets deleted immediately. terraform.tfstate.lock.info This is what Terraform uses to track, who is running its code against the infrastructure at any point in time. This is very important for teams working on the same Terraform repository at the same time. The lock prevents a user from executing Terraform configuration against the same infrastructure when another user is doing the same – it allows to avoid duplicates and conflicts.

It is a json format file that stores information about a user: user’s ID, what operation he/she is doing, timestamp, and location of the state file.

Subnets resource section
According to our architectural design, we require 6 subnets:

2 public

2 private for webservers

2 private for data layer

Let us create the first 2 public subnets.

Add below configuration to the main.tf file:

# Create public subnets1
    resource "aws_subnet" "public1" {
    vpc_id                     = aws_vpc.main.id
    cidr_block                 = "172.16.0.0/24"
    map_public_ip_on_launch    = true
    availability_zone          = "eu-central-1a"

}

# Create public subnet2
    resource "aws_subnet" "public2" {
    vpc_id                     = aws_vpc.main.id
    cidr_block                 = "172.16.1.0/24"
    map_public_ip_on_launch    = true
    availability_zone          = "eu-central-1b"
}

![12 adding the 2 public subnets code to the main tf file](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/4f933b01-e62c-494c-b8a8-af0c6a364fef)

![13 terraform plan after adding subnet creation details](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/ad17add6-8d4d-4071-a11c-c447d4ea8812)


We are creating 2 subnets, therefore declaring 2 resource blocks – one for each of the subnets.

We are using the vpc_id argument to interpolate the value of the VPC id by setting it to aws_vpc.main.id. This way, Terraform knows inside which VPC to create the subnet.

Run terraform plan and terraform apply

![14 vpc created in AWS console](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/da1fe1eb-83e8-45ac-b658-a6f16596d797)

![15 subnets created](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/8d1e4b3e-2080-4bc8-b3ff-8175aa4f134b)

#### Observations:

Hard coded values: Remember our best practice hint from the beginning? Both the availability_zone and cidr_block arguments are hard coded. We should always endeavour to make our work dynamic.

Multiple Resource Blocks: Notice that we have declared multiple resource blocks for each subnet in the code. This is bad coding practice. We need to create a single resource block that can dynamically create resources without specifying multiple blocks. Imagine if we wanted to create 10 subnets, our code would look very clumsy. So, we need to optimize this by introducing a count argument.

Now let us improve our code by refactoring it.

First, destroy the current infrastructure. Since we are still in development, this is totally fine. Otherwise, DO NOT DESTROY an infrastructure that has been deployed to production.

![16 terraform destroy](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/79f54734-33f7-4b5b-a975-e247ee27b6de)

To destroy whatever has been created run terraform destroy command, and type yes after evaluating the plan.

![17 terraform destroy 2](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/476afcbd-7fec-4924-a7ec-c38c0f423137)

### Fixing The Problems By Code Refactoring

#### Fixing Hard Coded Values:

We will introduce variables, and remove hard coding.
Starting with the provider block, declare a variable named region, give it a default value, and update the provider section by referring to the declared variable.

variable "region" {
        default = "eu-central-1"
    }

    provider "aws" {
        region = var.region
    }

Do the same to cidr value in the vpc block, and all the other arguments.

 variable "region" {
        default = "eu-central-1"
    }

    variable "vpc_cidr" {
        default = "172.16.0.0/16"
    }

    variable "enable_dns_support" {
        default = "true"
    }

    variable "enable_dns_hostnames" {
        default ="true" 
    }

    variable "enable_classiclink" {
        default = "false"
    }

    variable "enable_classiclink_dns_support" {
        default = "false"
    }

    provider "aws" {
    region = var.region
    }

    # Create VPC
    resource "aws_vpc" "main" {
    cidr_block                     = var.vpc_cidr
    enable_dns_support             = var.enable_dns_support 
    enable_dns_hostnames           = var.enable_dns_support
    enable_classiclink             = var.enable_classiclink
    enable_classiclink_dns_support = var.enable_classiclink

    }

![18 setting the variables](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/4f81d310-d820-46ee-a3bf-a09c197ce89f)

![19 refactoring the code for creating VPC](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/f821a12e-c9bc-4a98-b504-4cac0c8f7655)

Fixing multiple resource blocks: This is where things become a little tricky. It’s not complex, we are just going to introduce some interesting concepts. Loops & Data sources
Terraform has a functionality that allows us to pull data which exposes information to us. For example, every region has Availability Zones (AZ). Different regions have from 2 to 4 Availability Zones. With over 20 geographic regions and over 70 AZs served by AWS, it is impossible to keep up with the latest information by hard coding the names of AZs. Hence, we will explore the use of Terraform’s Data Sources to fetch information outside of Terraform. In this case, from AWS

Let us fetch Availability zones from AWS, and replace the hard coded value in the subnet’s availability_zone section.

        # Get list of availability zones
        data "aws_availability_zones" "available" {
        state = "available"
        }

![20 REFACTORING CODE TO GET LIST OF AZs](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/588f9d34-ee80-4183-860a-399665130663)

To make use of this new data resource, we will need to introduce a count argument in the subnet block: Something like this.

    # Create public subnet1
    resource "aws_subnet" "public" { 
        count                   = 2
        vpc_id                  = aws_vpc.main.id
        cidr_block              = "172.16.1.0/24"
        map_public_ip_on_launch = true
        availability_zone       = data.aws_availability_zones.available.names[count.index]

    }

Let us quickly understand what is going on here.

The count tells us that we need 2 subnets. Therefore, Terraform will invoke a loop to create 2 subnets.

The data resource will return a list object that contains a list of AZs. Internally, Terraform will receive the data like this
  ["eu-central-1a", "eu-central-1b"]

Each of them is an index, the first one is index 0, while the other is index 1. If the data returned had more than 2 records, then the index numbers would continue to increment.

Therefore, each time Terraform goes into a loop to create a subnet, it must be created in the retrieved AZ from the list. Each loop will need the index number to determine what AZ the subnet will be created. That is why we have data.aws_availability_zones.available.names[count.index] as the value for availability_zone. When the first loop runs, the first index will be 0, therefore the AZ will be eu-central-1a. The pattern will repeat for the second loop.

But we still have a problem. If we run Terraform with this configuration, it may succeed for the first time, but by the time it goes into the second loop, it will fail because we still have cidr_block hard coded. The same cidr_block cannot be created twice within the same VPC. So, we have a little more work to do.

Let’s make cidr_block dynamic.
We will introduce a function cidrsubnet() to make this happen. It accepts 3 parameters. Let us use it first by updating the configuration, then we will explore its internals.

    # Create public subnet1
    resource "aws_subnet" "public" { 
        count                   = 2
        vpc_id                  = aws_vpc.main.id
        cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
        map_public_ip_on_launch = true
        availability_zone       = data.aws_availability_zones.available.names[count.index]

    }

![21 refactoring code for Public subnet 1 calling the AZ](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/d64e695c-a428-4fab-8b99-653da56fdcb6)

A closer look at cidrsubnet – this function works like an algorithm to dynamically create a subnet CIDR per AZ. Regardless of the number of subnets created, it takes care of the cidr value per subnet.

Its parameters are cidrsubnet(prefix, newbits, netnum)

The prefix parameter must be given in CIDR notation, same as for VPC.

The newbits parameter is the number of additional bits with which to extend the prefix. For example, if given a prefix ending with /16 and a newbits value of 4, the resulting subnet address will have length /20

The netnum parameter is a whole number that can be represented as a binary integer with no more than newbits binary digits, which will be used to populate the additional bits added to the prefix

You can experiment how this works by entering the terraform console and keep changing the figures to see the output.

On the terminal, run terraform console

type cidrsubnet("172.16.0.0/16", 4, 0)

Hit enter

See the output

Keep changing the numbers and see what happens.

To get out of the console, type exit

![22 testing out how the cidr subnet function works using Prefix Newbit and Netnum](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/72800735-cb76-4dc6-8043-ccfdf762be80)


The final problem to solve is removing hard coded count value.

If we cannot hard code a value we want, then we will need a way to dynamically provide the value based on some input. Since the data resource returns all the AZs within a region, it makes sense to count the number of AZs returned and pass that number to the count argument.

To do this, we can introuduce length() function, which basically determines the length of a given list, map, or string.

Since data.aws_availability_zones.available.names returns a list like ["eu-central-1a", "eu-central-1b", "eu-central-1c"] we can pass it into a lenght function and get number of the AZs.

length(["eu-central-1a", "eu-central-1b", "eu-central-1c"])

Open up terraform console and try it

Now we can simply update the public subnet block like this

# Create public subnet1
    resource "aws_subnet" "public" { 
        count                   = length(data.aws_availability_zones.available.names)
        vpc_id                  = aws_vpc.main.id
        cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
        map_public_ip_on_launch = true
        availability_zone       = data.aws_availability_zones.available.names[count.index]

    }

![23 refactoring the code to remove the hard coded count value using the lenght of AZs which is the count of AZs](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/4a238cb3-a1ea-4158-a4af-ebc33655cb7d)


Observations:

What we have now, is sufficient to create the subnet resource required. But if you observe, it is not satisfying our business requirement of just 2 subnets. The length function will return number 3 to the count argument, but what we actually need is 2.
Now, let us fix this.

Declare a variable to store the desired number of public subnets, and set the default value
variable "preferred_number_of_public_subnets" {
  default = 2
}

![24 introducing a variable to store the desired no of subnets](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/08a5c1fb-21e2-4420-8168-52e78036eb13)

Next, update the count argument with a condition. Terraform needs to check first if there is a desired number of subnets. Otherwise, use the data returned by the lenght function. See how that is presented below.


# Create public subnets
resource "aws_subnet" "public" {
  count  = var.preferred_number_of_public_subnets == null ? length(data.aws_availability_zones.available.names) : var.preferred_number_of_public_subnets   
  vpc_id = aws_vpc.main.id
  cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
  map_public_ip_on_launch = true
  availability_zone       = data.aws_availability_zones.available.names[count.index]

}

![25 refactoring the count of subnets](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/5944c986-dbb9-4145-b7a0-4da4f6710cf6)


Now lets break it down:

The first part var.preferred_number_of_public_subnets == null checks if the value of the variable is set to null or has some value defined.
The second part ? and length(data.aws_availability_zones.available.names) means, if the first part is true, then use this. In other words, if preferred number of public subnets is null (Or not known) then set the value to the data returned by lenght function.
The third part : and var.preferred_number_of_public_subnets means, if the first condition is false, i.e preferred number of public subnets is not null then set the value to whatever is definied in var.preferred_number_of_public_subnets
Now the entire configuration should now look like this

# Get list of availability zones
data "aws_availability_zones" "available" {
state = "available"
}

variable "region" {
      default = "eu-central-1"
}

variable "vpc_cidr" {
    default = "172.16.0.0/16"
}

variable "enable_dns_support" {
    default = "true"
}

variable "enable_dns_hostnames" {
    default ="true" 
}

variable "enable_classiclink" {
    default = "false"
}

variable "enable_classiclink_dns_support" {
    default = "false"
}

  variable "preferred_number_of_public_subnets" {
      default = 2
}

provider "aws" {
  region = var.region
}

# Create VPC
resource "aws_vpc" "main" {
  cidr_block                     = var.vpc_cidr
  enable_dns_support             = var.enable_dns_support 
  enable_dns_hostnames           = var.enable_dns_support
  enable_classiclink             = var.enable_classiclink
  enable_classiclink_dns_support = var.enable_classiclink

}

# Create public subnets
resource "aws_subnet" "public" {
  count  = var.preferred_number_of_public_subnets == null ? length(data.aws_availability_zones.available.names) : var.preferred_number_of_public_subnets   
  vpc_id = aws_vpc.main.id
  cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
  map_public_ip_on_launch = true
  availability_zone       = data.aws_availability_zones.available.names[count.index]

}


Note: You should try changing the value of preferred_number_of_public_subnets variable to null and notice how many subnets get created.


#### INTRODUCING VARIABLES.TF & TERRAFORM.TFVARS

Instead of havng a long lisf of variables in main.tf file, we can actually make our code a lot more readable and better structured by moving out some parts of the configuration content to other files.

We will put all variable declarations in a separate file

And provide non default values to each of them

Create a new file and name it variables.tf

Copy all the variable declarations into the new file.

Create another file, name it terraform.tfvars

Set values for each of the variables.

#### Maint.tf

# Get list of availability zones
data "aws_availability_zones" "available" {
state = "available"
}

provider "aws" {
  region = var.region
}

# Create VPC
resource "aws_vpc" "main" {
  cidr_block                     = var.vpc_cidr
  enable_dns_support             = var.enable_dns_support 
  enable_dns_hostnames           = var.enable_dns_support
  enable_classiclink             = var.enable_classiclink
  enable_classiclink_dns_support = var.enable_classiclink

}

# Create public subnets
resource "aws_subnet" "public" {
  count  = var.preferred_number_of_public_subnets == null ? length(data.aws_availability_zones.available.names) : var.preferred_number_of_public_subnets   
  vpc_id = aws_vpc.main.id
  cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
  map_public_ip_on_launch = true
  availability_zone       = data.aws_availability_zones.available.names[count.index]
}

![28 new state of main tf file](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c4ec9887-595b-42e4-88cb-d82a15034b73)


#### variables.tf

variable "region" {
      default = "eu-central-1"
}

variable "vpc_cidr" {
    default = "172.16.0.0/16"
}

variable "enable_dns_support" {
    default = "true"
}

variable "enable_dns_hostnames" {
    default ="true" 
}

variable "enable_classiclink" {
    default = "false"
}

variable "enable_classiclink_dns_support" {
    default = "false"
}

  variable "preferred_number_of_public_subnets" {
      default = null
}

![26 create new file in PBL call it variable tf and paste all the variables inside](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/bde204d3-4bba-4ee4-96e0-3925c95a0619)


#### terraform.tfvars

region = "eu-central-1"

vpc_cidr = "172.16.0.0/16" 

enable_dns_support = "true" 

enable_dns_hostnames = "true"  

enable_classiclink = "false" 

enable_classiclink_dns_support = "false" 

preferred_number_of_public_subnets = 2

![27 terraform tf vars file to hold default values](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/7bb919a6-395b-4112-a15d-a2adda77f735)


You should also have this file structure in the PBL folder.

└── PBL
    ├── main.tf
    ├── terraform.tfstate
    ├── terraform.tfstate.backup
    ├── terraform.tfvars
    └── variables.tf

Run terraform plan and ensure everything works

![29 running terraform fmt and terraform validate to arrange codes properly and check configuration respectively](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/38e594bf-cfe0-4caa-b6e2-9e8685ef7a1d)

![30 using terraform apply auto approve to create our VPC and subnets](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/a2cd27a9-3bdd-44a5-818d-175a156f0f49)

![31 3 resources added which are 1 vpc and 2 public subnets](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/f94b94b0-c27b-43f3-bafd-fb9f9acca15d)

![32 the VPC created in AWS Console](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/c653acc4-a1ad-4b45-9a9f-6c4dc1a93098)

![33 the 2 public subnets created](https://github.com/Sakirat/Project_Based_Learning/assets/110112922/99f4b05a-8194-4e98-9a9b-617b75bb8762)

Congratulations!

You have learned how to create and delete AWS Network Infrastructure programmatically with Terraform!
