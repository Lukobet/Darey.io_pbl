
# AUTOMATE INFRASTRUCTURE WITH IAC USING TERRAFORM PART 1


After you have built AWS infrastructure for 2 websites manually, it is time to automate the process using Terraform.
##### Prerequisites before you begin writing Terraform code

Create an IAM user, name it terraform (ensure that the user has only programatic access to your AWS account) and grant this user AdministratorAccess permissions. ![Screenshot from 2023-08-24 20-28-17](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/ce7a24d2-d7ab-4633-8733-0fb9e1430507)


Copy the secret access key and access key ID. Save them in a notepad temporarily **====DONE**

Configure programmatic access from your workstation to connect to AWS using the access keys copied above and a Python SDK (boto3). You must have Python 3.6 or higher on your workstation.


For easier authentication configuration – use AWS CLI with aws configure command.**===DONE**

* Create an S3 bucket to store Terraform state file. You can name it something like <yourname>-dev-terraform-bucket (Note: S3 bucket names must be unique unique within a region partition, you can read about S3 bucken naming in this article).![Screenshot from 2023-08-25 17-11-45](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/af1b2e0a-7671-4384-941b-4cf62e08bfde)


 
When you have configured authentication and installed boto3, make sure you can programmatically access your AWS account by running following commands in >python:
```
import boto3
s3 = boto3.resource('s3')
for bucket in s3.buckets.all():
    print(bucket.name)
```

You shall see your previously created S3 bucket name – <yourname>-dev-terraform-bucket
![Screenshot from 2023-08-25 17-20-37](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/3a7ae430-ef3f-4f6b-8576-4851f6fa29fc)
The secrets of writing quality Terraform code
The secret recipe of a successful Terraform projects consists of:

Your understanding of your goal (desired AWS infrastructure end state)
Your knowledge of the IaC technology used (in this case – Terraform)
Your ability to effectively use up to date Terraform documentation here
As you go along completing this project, you will get familiar with Terraform-specific terminology, such as:

* **Attribute**: In Terraform's configuration language: a named piece of data that belongs to some kind of object. The value of an attribute can be referenced in expressions using a dot-separated notation, like aws_instance.example.id.

Terraform resources and data sources make all of their arguments available as readable attributes, and also typically export additional read-only attributes.


* **Resource:** Also "infrastructure resource".

In Terraform's configuration language: A block that describes one or more infrastructure objects. Resources can be things like virtual networks, compute instances, or higher-level components such as DNS records.

In other Terraform-related contexts: An infrastructure object of a type that could be managed by Terraform.

A resource block in a configuration instructs Terraform to manage the described resource — during a run, Terraform will create a matching real infrastructure object if one doesn't already exist, and will modify the existing object if it doesn't match the desired configuration. Terraform uses state to keep track of which real infrastructure objects correspond to resources in a configuration.

Terraform uses cloud provider APIs to create, edit, and destroy resources. Terraform providers are responsible for defining resource types and mapping transitions in a resource's state to a specific series of API calls that will carry out the necessary changes.


* **Interpolations**: Using a special placeholder to insert a computed value into a string.

Terraform's configuration language supports interpolation in strings using ${<EXPRESSION>} placeholders. For example:

"Hello, ${var.name}!"

Prior to Terraform 0.12, interpolation was the only way to use non-literal expressions in Terraform configurations; in 0.12 and later, expressions can be used independently.

Interpolation is a very common feature in programming languages; for example, Ruby uses #{<EXPRESSION>} placeholders in double-quoted strings, and JavaScript (ES6 and up) uses ${<EXPRESSION>} placeholders in backtick-quoted strings
* **Argument** : In Terraform's configuration language: a syntax construct that assigns a value to a name. Arguments have the form <IDENTIFIER> = <EXPRESSION>, and they appear within blocks.

Most of a Terraform configuration consists of using arguments to configure Terraform resources. Each resource type defines the arguments its resources can use, the allowed values for each argument, and which arguments are required or optional. Information about a given resource type can be found in the docs for that resource's provider.
* **Providers**: Terraform relies on plugins called providers to interact with cloud providers, SaaS providers, and other APIs.

Terraform configurations must declare which providers they require so that Terraform can install and use them. Additionally, some providers require configuration (like endpoint URLs or cloud regions) before they can be used.


* **Provisioners** : You can use provisioners to model specific actions on the local machine or on a remote machine in order to prepare servers or other infrastructure objects for service.


* **Input Variables**: Variables
Also "input variables".

In general-purpose programming, a variable is a symbolic name associated with a value.

In Terraform, "variables" almost always refers to input variables, which are key/value pairs used in a run. Terraform modules can declare variables to allow customization. For child modules, the parent module provides a value when calling the module; for the root module, values are provided at run time.

Terraform Cloud lets you set values for root input variables in a workspace, so all collaborators can use the same values. Variable values marked as "sensitive" become unreadable in the UI and API, and all variable values are protected by Vault.
* **Output Variables** : Also "outputs".

Data exported by a Terraform module, which can be displayed to a user and/or programmatically used by other Terraform code.
* **Module:** Also "Terraform module".

A self-contained collection of Terraform configurations that manages a collection of related infrastructure resources.

Other Terraform configurations can call a module, which tells Terraform to manage any resources described by that module.

Modules define input variables (which the calling module can set values for) and output values (which the calling module can reference in expressions).
* **Data Source**: A resource-like object that can be configured in Terraform's configuration language.

Unlike resources, data sources do not create or manage infrastructure. Instead, they return information about some kind of external object in the form of readable attributes. This allows a Terraform configuration to make use of information defined outside of Terraform, or defined by another separate Terraform configuration.

Data sources are implemented by providers
* **Local Values**: Local values assign a name to an expression, that can then be used multiple times within a module.

Comparing modules to functions in a traditional programming language, if variables are analogous to function arguments and outputs are analogous to function return values then local values are comparable to a function's local variables.

This page assumes you're already familiar with the configuration syntax.

Examples
Local values are defined in locals blocks:
```
# Ids for multiple sets of EC2 instances, merged together
locals {
  instance_ids = "${concat(aws_instance.blue.*.id, aws_instance.green.*.id)}"
}

# A computed default name prefix
locals {
  default_name_prefix = "${var.project_name}-web"
  name_prefix         = "${var.name_prefix != "" ? var.name_prefix : local.default_name_prefix}"
}

# Local values can be interpolated elsewhere using the "local." prefix.
resource "aws_s3_bucket" "files" {
  bucket = "${local.name_prefix}-files"
  # ...
}

```

* **Backend**: The part of Terraform's core that determines how Terraform stores state and performs operations (like plan, apply, import, etc.). Terraform has multiple backends to choose from, which can be configured in a variety of ways. Backends are not plugins, so it is not possible to install additional backends.

In a general computer science sense, a backend is any lower-level implementation that enables a higher-level feature. But in the context of Terraform, "backend" always means the built-in code that handles state and operations.


Make sure you understand them and know when to use each of them.

Another concept you must know is data type. This is a general programing concept, it refers to how data represented in a programming language and defines how a compiler or interpreter can use the data. Common data types are:Integer,Float,String,Boolean, etc.
### VPC | SUBNETS | SECURITY GROUPS
Open your Visual Studio Code and:

* Create a folder called PBL
* Create a file in the folder, name it main.tf
![Screenshot from 2023-08-25 21-57-39](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/0221d9b2-c6c5-49da-86c9-9c746b4faf9c)

**Provider and VPC resource section**
Set up Terraform CLI as per this instruction.

* Add AWS as a provider, and a resource to create a VPC in the main.tf file.
* Provider block informs Terraform that we intend to build infrastructure within AWS.
* Resource block will create a VPC.

 
** provider "aws" {
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
**

The next thing we need to do, is to download necessary plugins for Terraform to work. These plugins are used by providers and provisioners. At this stage, we only have provider in our main.tf file. So, Terraform will just download plugin for AWS provider.

![Screenshot from 2023-08-25 22-04-09](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/62492997-8399-4d85-9e8c-771c544f2b39)

![Screenshot from 2023-08-26 11-42-01](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/cc050e83-fac1-4bdc-8558-6e177c550bac)


while trying to create the aws-vpc i got this error when i implemented the terraform plan.

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/b76d62a4-ea9e-4cca-941b-d2c4460f0980)
Had to comment out line 10 and 11
![Screenshot from 2023-08-26 12-09-58](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/879d007c-eced-404d-8df2-f9c05bbf0968)

According to our architectural design, we require 6 subnets:

2 public, 2 private for webservers and 2 private for data layer
Let us create the first 2 public subnets.

Add below configuration to the main.tf file:

  ```
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
```

Run terraform plan and terraform apply

![Screenshot from 2023-08-26 13-45-40](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/422c19c2-48d2-448e-b0d8-b363432b669d)
![Screenshot from 2023-08-26 13-51-55](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/67be1800-9efb-4109-9e48-38340487d06c)
vpc and subnets created 

![Screenshot from 2023-08-26 13-53-25](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/d9adc02f-e6c2-47b1-afab-25f6c29aa1a5)
![Screenshot from 2023-08-26 13-54-05](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/dc840a46-d45b-485e-a4b9-e3ca5f48a082)
Now let us improve our code by refactoring it.

First, destroy the current infrastructure. Since we are still in development, this is totally fine. Otherwise, DO NOT DESTROY an infrastructure that has been deployed to production.

To destroy whatever has been created run terraform destroy command, and type yes after evaluating the plan.

![Screenshot from 2023-08-26 13-58-01](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/97342ab1-da9e-4041-afc7-5bfc76a7b81a)


![Screenshot from 2023-08-26 13-58-51](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/bf1fcc88-dc91-44f0-94fa-8af8401066c0)


## FIXING THE PROBLEMS BY CODE REFACTORING

**Fixing Hard Coded Values:** We will introduce variables, and remove hard coding.
Starting with the provider block, declare a variable named region, give it a default value, and update the provider section by referring to the declared variable.
```
 variable "region" {
        default = "eu-central-1"
    }

    provider "aws" {
        region = var.region
    }
```

Do the same to cidr value in the vpc block, and all the other arguments.

```
 
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
```
**Fixing multiple resource blocks:**
Terraform has a functionality that allows us to pull data which exposes information to us. For example, every region has Availability Zones (AZ). Different regions have from 2 to 4 Availability Zones. With over 20 geographic regions and over 70 AZs served by AWS, it is impossible to keep up with the latest information by hard coding the names of AZs. Hence, we will explore the use of Terraform’s Data Sources to fetch information outside of Terraform. In this case, from AWS

Let us fetch Availability zones from AWS, and replace the hard coded value in the subnet’s availability_zone section.
```
  # Get list of availability zones
        data "aws_availability_zones" "available" {
        state = "available"
        }
```

change the info in the subnet 1 to this
```


   # Create public subnet1
    resource "aws_subnet" "public" { 
        count                   = 2
        vpc_id                  = aws_vpc.main.id
        cidr_block              = "172.16.1.0/24"
        map_public_ip_on_launch = true
        availability_zone       = data.aws_availability_zones.available.names[count.index]

    }
```
this above means that the count tells us that we need 2 subnets. Therefore, Terraform will invoke a loop to create 2 subnets.
The data resource will return a list object that contains a list of AZs. Internally, Terraform will receive the data like this
  ["eu-central-1a", "eu-central-1b"]
![Screenshot from 2023-08-26 14-21-13](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/23adb14a-f007-4ff2-af78-152712cb333e)

but with the data bove it will fail, we need to change it to the codes below

**Let’s make cidr_block dynamic.**
We will introduce a function cidrsubnet() to make this happen. It accepts 3 parameters. Let us use it first by updating the configuration, then we will explore its internals.

```
 # Create public subnet1
    resource "aws_subnet" "public" { 
        count                   = 2
        vpc_id                  = aws_vpc.main.id
        cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
        map_public_ip_on_launch = true
        availability_zone       = data.aws_availability_zones.available.names[count.index]

    }
```

![Screenshot from 2023-08-26 14-22-21](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/68e83f54-50f5-4cd1-a768-897f7db4627d)

A closer look at cidrsubnet – this function works like an algorithm to dynamically create a subnet CIDR per AZ. Regardless of the number of subnets created, it takes care of the cidr value per subnet.

Its parameters are cidrsubnet(prefix, newbits, netnum)

The **prefix** parameter must be given in CIDR notation, same as for VPC.
The **newbits** parameter is the number of additional bits with which to extend the prefix. For example, if given a prefix ending with /16 and a newbits value of 4, the resulting subnet address will have length /20
The **netnum** parameter is a whole number that can be represented as a binary integer with no more than newbits binary digits, which will be used to populate the additional bits added to the prefix.

You can experiment how this works by entering the terraform console and keep changing the figures to see the output.

On the terminal, run terraform console
type cidrsubnet("172.16.0.0/16", 4, 0)
Hit enter
See the output
Keep change the numbers and see what happens.
To get out of the console, type exit
![Screenshot from 2023-08-26 14-29-10](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/17399bb7-b25c-4217-be13-6bb2fd50219b)
#### The final problem to solve is removing hard coded count value.

 Since the data resource returns all the AZs within a region, it makes sense to count the number of AZs returned and pass that number to the count argument.

To do this, we can introduce (**length**) function, which basically determines the length of a given list, map, or string.

Since data.aws_availability_zones.available.names returns a list like ["eu-central-1a", "eu-central-1b", "eu-central-1c"] we can pass it into a length function and get number of the AZs.

length(["eu-central-1a", "eu-central-1b", "eu-central-1c"])

Open up terraform console and try it
![Screenshot from 2023-08-26 14-36-40](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/b577c120-4593-4b1b-a942-a2a21131a176)

Now we can simply update the public subnet1  block like this

```
 # Create public subnet1
    resource "aws_subnet" "public" { 
        count                   = length(data.aws_availability_zones.available.names)
        vpc_id                  = aws_vpc.main.id
        cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
        map_public_ip_on_launch = true
        availability_zone       = data.aws_availability_zones.available.names[count.index]

    }
```
![Screenshot from 2023-08-26 14-37-55](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/7301b2ed-eae6-44d5-ad55-2d272e9f8de6)

You will observe that What we have now, is sufficient to create the subnet resource required. But if you observe, it is not satisfying our business requirement of just 2 subnets. The length function will return number 3 to the count argument, but what we actually need is 2.
so to fix this declare a variable to store the desired number of public subnets, and set the default value

```
 variable "preferred_number_of_public_subnets" {
  default = 2
}
```

![Screenshot from 2023-08-26 14-41-10](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/76611173-a120-4fbd-9f98-c03d96e43430)

* Next, update the count argument with a condition. Terraform needs to check first if there is a desired number of subnets. Otherwise, use the data returned by the lenght function. See how that is presented below.
```
 # Create public subnet1
    resource "aws_subnet" "public" { 
        count                   = length(data.aws_availability_zones.available.names)
        vpc_id                  = aws_vpc.main.id
        cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
        map_public_ip_on_launch = true
        availability_zone       = data.aws_availability_zones.available.names[count.index]

    }
```

![Screenshot from 2023-08-26 14-42-52](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/cfafdeb3-c15a-4ee8-991e-57fa6621f386)
A breakdown of it all 
* The first part var.preferred_number_of_public_subnets == null checks if the value of the variable is set to null or has some value defined.
* The second part ? and length(data.aws_availability_zones.available.names) means, if the first part is true, then use this. In other words, if preferred number of public subnets is null (Or not known) then set the value to the data returned by lenght function.
* The third part : and var.preferred_number_of_public_subnets means, if the first condition is false, i.e preferred number of public subnets is not null then set the value to whatever is definied in var.preferred_number_of_public_subnets

 You should try changing the value of preferred_number_of_public_subnets variable to null and notice how many subnets get created. 
![Screenshot from 2023-08-26 14-59-34](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/218ac13b-74f5-472a-9b3e-c6f4179f47b6)


##### INTRODUCING VARIABLES.TF & TERRAFORM.TFVARS

Instead of havng a long lisf of variables in main.tf file, we can actually make our code a lot more readable and better structured by moving out some parts of the configuration content to other files.

We will put all variable declarations in a separate file
And provide non default values to each of them

* Create a new file and name it variables.tf
* Copy all the variable declarations into the new file.
* Create another file, name it terraform.tfvars
* Set values for each of the variables.

**variable.tf** will look like this
   ```
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
```

![Screenshot from 2023-08-26 15-02-11](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/4a482224-5aa2-49ab-aea8-ebee0234092a)

**terraform.tfvars**  will look like this

```
 region = "eu-central-1"

vpc_cidr = "172.16.0.0/16" 

enable_dns_support = "true" 

enable_dns_hostnames = "true"  

enable_classiclink = "false" 

enable_classiclink_dns_support = "false" 

preferred_number_of_public_subnets = 2
```
![Screenshot from 2023-08-26 15-03-06](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/7a03a074-69f8-4588-898c-6855bd2aa61c)
file structure
![Screenshot from 2023-08-26 15-05-59](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/59135366-52de-4aca-adbb-b436ad6b74f5)
do terraform plan
![Screenshot from 2023-08-26 15-07-53](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/ac847d9b-b8df-47ec-9986-cedb4325cc5a)

