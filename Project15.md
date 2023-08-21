# AWS CLOUD SOLUTION FOR 2 COMPANY WEBSITES USING A REVERSE PROXY TECHNOLOGY
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

Create a free domain name for your fictitious company at Freenom domain registrar here.
Create a hosted zone in AWS, and map it to your free domain from Freenom
### TASK 1:Set Up a Virtual Private Network (VPC)
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

Edit a route in public route table, and associate it with the Internet Gateway. (This is what allows a public subnet to be accisble from the Internet)
![Screenshot from 2023-08-21 23-46-55](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/65aec9de-002c-4b74-aef4-7c65b8e9c863)

Create 3 Elastic IPs
Create a Nat Gateway and assign one of the Elastic IPs (*The other 2 will be used by Bastion hosts)
Create a Security Group for:

Nginx Servers: Access to Nginx should only be allowed from a Application Load balancer (ALB). At this point, we have not created a load balancer, therefore we will update the rules later. For now, just create it and put some dummy records as a place holder.
Bastion Servers: Access to the Bastion servers should be allowed only from workstations that need to SSH into the bastion servers. Hence, you can use your workstation public IP address. To get this information, simply go to your terminal and type curl www.canhazip.com
Application Load Balancer: ALB will be available from the Internet
Webservers: Access to Webservers should only be allowed from the Nginx servers. Since we do not have the servers created yet, just put some dummy records as a place holder, we will update it later.
Data Layer: Access to the Data layer, which is comprised of Amazon Relational Database Service (RDS) and Amazon Elastic File System (EFS) must be carefully desinged – only webservers should be able to connect to RDS, while Nginx and Webservers will have access to EFS Mountpoint.
