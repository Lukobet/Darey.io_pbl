# AWS CLOUD SOLUTION FOR 2 COMPANY WEBSITES USING A REVERSE PROXY TECHNOLOGY
futurescreenshots

![Screenshot from 2023-08-21 15-28-34](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/ab00fa97-0946-41c2-9916-9ce1e5b8eb64)
![Screenshot from 2023-08-21 15-29-49](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/7b0bab7b-00f4-46a6-ae3e-907acd786e0b)
![Screenshot from 2023-08-21 15-29-25](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/074275a8-295f-41af-8eae-d5ddf01afa52)





* Properly configure your AWS account and Organization Unit 
Create an AWS Master account. (Also known as Root Account)
Within the Root account, create a sub-account and name it DevOps. (You will need another email address to complete this)

![Screenshot from 2023-08-21 22-43-19](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/9c5aa351-73e0-45e9-8fae-4b28a8121712)
![Screenshot from 2023-08-21 23-15-06](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/32f66cf7-6f75-4947-a77c-16a8abe491cb)
Within the Root account, create an AWS Organization Unit (OU). Name it Dev. (We will launch Dev resources in there)
Move the DevOps account into the Dev OU.
![Screenshot from 2023-08-21 23-14-52](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/9146e2e1-4648-4ad6-8af3-a659d50d9ead)

Login to the newly created AWS account using the new email address.

Create a free domain name for your fictitious company 
![Screenshot from 2023-08-23 05-36-57](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/085da3f5-472f-431f-b565-36562d6ccce2)

Create a hosted zone in AWS, and map it to your free domain from HOSTINGER


![Screenshot from 2023-08-23 05-34-59](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/01c9603b-3752-4f67-b8f2-d25a5f481400)

### TASK 1:Set Up a Virtual Private Network (VPC)
futurescreenshot
![Screenshot from 2023-08-21 23-24-39](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/15eca66e-c4f3-45af-b3ad-f631c8477c31)
![Screenshot from 2023-08-21 23-24-25](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/7f393260-4edf-45e2-9b5a-2cc6251d8f0c)

* Create a VPC
![Screenshot from 2023-08-21 23-30-02](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/ae43c187-dfe6-4487-9a3a-9c78055ff8fe)

*Create subnets as shown in the architecture

![Screenshot from 2023-08-21 23-43-59](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/4e8e01b6-1907-4b25-9f52-3effb6ba86b2)

*Create a route table and associate it with public subnets and private subnets

![Screenshot from 2023-08-21 23-46-55](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/65aec9de-002c-4b74-aef4-7c65b8e9c863)

*Create an Internet Gateway
![Screenshot from 2023-08-21 23-33-29](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/c3b3f566-0ee9-44dd-823f-88dc824c0ed1)

Edit a route in public route table, and associate it with the Internet Gateway. (This is what allows a public subnet to be accessible from the Internet)

![Screenshot from 2023-08-21 23-50-22](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/078c505d-17cb-433b-94a0-4b3460740bb0)

![Screenshot from 2023-08-21 23-57-40](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/32e02d23-5bcc-4822-8993-476f5d0f3221)
![Screenshot from 2023-08-21 23-57-23](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/1e94df34-a1ff-429b-9c57-d811f8566e59)
Create 3 Elastic IPs
Create a Nat Gateway and assign one of the Elastic IPs (*The other 2 will be used by Bastion hosts)
![Screenshot from 2023-08-21 23-55-13](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/6b6006da-2ea1-41c2-b22a-1859b4e21000)

* Create a Security Group for:

Nginx Servers:
Bastion Servers: 
Application Load Balancer: 
Webservers: 
Data Layer: 

![Screenshot from 2023-08-22 00-03-04](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/8a803717-954a-4ea7-9242-67bd81cb7480)
![Screenshot from 2023-08-22 00-06-06](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/d4a07609-5929-4ed3-b29b-af17dd3c6ff7)
![Screenshot from 2023-08-22 00-11-00](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/55078fad-c918-4a9e-9c3e-a2506f17169b)
![Screenshot from 2023-08-22 00-14-43](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/cdad0ff7-b7b9-407d-8b51-80b67c0b8178)
![Screenshot from 2023-08-22 00-17-36](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/dcc77ff6-9161-4a44-9c16-89da32c36bfd)
![Screenshot from 2023-08-22 00-21-46](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/fc92995b-a026-4dff-b5ac-e949d30ba0ed)

 
*Navigate to AWS ACM
Request a public wildcard certificate for the domain name you registered in Freenom
Use DNS to validate the domain name
Tag the resource
![Screenshot from 2023-08-23 19-54-31](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/071f89f7-3045-42b6-873b-55a723fb0653)



*create Amazon Relational Database Service (RDS) and Amazon Elastic File System (EFS) 
![Screenshot from 2023-08-23 07-06-21](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/c406226a-a37a-48e8-af77-88c70dfde460)
access point created for the filesystem

![Screenshot from 2023-08-23 07-13-14](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/be80b49a-399b-4d56-8952-bcb63e84e490)

Setup RDS
Pre-requisite: Create a KMS key from Key Management Service (KMS) to be used to encrypt the database instance.
![Screenshot from 2023-08-23 07-23-10](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/a1a75f00-a433-4611-a6ba-13a05ceb8919)

Subnet groups created
![Screenshot from 2023-08-23 07-26-59](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/fe86c307-7bea-46b4-ace3-9cc6443383e3)
Database created successfully
![Screenshot from 2023-08-23 20-07-52](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/2e98519a-21ac-4f2d-9548-04cc98ceda7d)
* Proceed With Compute Resources
You will need to set up and configure compute resources inside your VPC. The recources related to compute are:

*EC2 Instances
Launch Templates
Target Groups
Autoscaling Groups
TLS Certificates
Application Load Balancers (ALB)*


*Set Up Compute Resources for Nginx
Provision EC2 Instances for Nginx
Create an EC2 Instance based on CentOS Amazon Machine Image (AMI) in any 2 Availability Zones (AZ) in any AWS Region (it is recommended to use the Region that is closest to your customers). Use EC2 instance of T2 family (e.g. t2.micro or similar)

Ensure that it has the following software installed:

python
ntp
net-tools
vim
wget
telnet
epel-release
htop
Create an AMI out of the EC2 instance

Prepare Launch Template For Nginx (One Per Subnet)
Make use of the AMI to set up a launch template
Ensure the Instances are launched into a public subnet
Assign appropriate security group
Configure Userdata to update yum package repository and install nginx
Configure Target Groups
Select Instances as the target type
Ensure the protocol HTTPS on secure TLS port 443
Ensure that the health check path is /healthstatus
Register Nginx Instances as targets
Ensure that health check passes for the target group
Configure Autoscaling For Nginx
Select the right launch template
Select the VPC
Select both public subnets
Enable Application Load Balancer for the AutoScalingGroup (ASG)
Select the target group you created before
Ensure that you have health checks for both EC2 and ALB
The desired capacity is 2
Minimum capacity is 2
Maximum capacity is 4
Set scale out if CPU utilization reaches 90%
Ensure there is an SNS topic to send scaling notifications
Set Up Compute Resources for Bastion
Provision the EC2 Instances for Bastion
Create an EC2 Instance based on CentOS Amazon Machine Image (AMI) per each Availability Zone in the same Region and same AZ where you created Nginx server
Ensure that it has the following software installed

python
ntp
net-tools
vim
wget
telnet
epel-release
htop
Associate an Elastic IP with each of the Bastion EC2 Instances
Create an AMI out of the EC2 instance
Prepare Launch Template For Bastion (One per subnet)
Make use of the AMI to set up a launch template
Ensure the Instances are launched into a public subnet
Assign appropriate security group
Configure Userdata to update yum package repository and install Ansible and git
Configure Target Groups
Select Instances as the target type
Ensure the protocol is TCP on port 22
Register Bastion Instances as targets
Ensure that health check passes for the target group
Configure Autoscaling For Bastion
Select the right launch template
Select the VPC
Select both public subnets
Enable Application Load Balancer for the AutoScalingGroup (ASG)
Select the target group you created before
Ensure that you have health checks for both EC2 and ALB
The desired capacity is 2
Minimum capacity is 2
Maximum capacity is 4
Set scale out if CPU utilization reaches 90%
Ensure there is an SNS topic to send scaling notifications
Set Up Compute Resources for Webservers
Provision the EC2 Instances for Webservers
Now, you will need to create 2 separate launch templates for both the WordPress and Tooling websites

Create an EC2 Instance (Centos) each for WordPress and Tooling websites per Availability Zone (in the same Region).

Ensure that it has the following software installed

python
ntp
net-tools
vim
wget
telnet
epel-release
htop
php
Create an AMI out of the EC2 instance

Prepare Launch Template For Webservers (One per subnet)
Make use of the AMI to set up a launch template
Ensure the Instances are launched into a public subnet
Assign appropriate security group
Configure Userdata to update yum package repository and install wordpress (Only required on the WordPress launch template)
TLS Certificates From Amazon Certificate Manager (ACM)
You will need TLS certificates to handle secured connectivity to your Application Load Balancers (ALB).





