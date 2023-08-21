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

Edit a route in public route table, and associate it with the Internet Gateway. (This is what allows a public subnet to be accessible from the Internet)

![Screenshot from 2023-08-21 23-50-22](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/078c505d-17cb-433b-94a0-4b3460740bb0)

![Screenshot from 2023-08-21 23-57-40](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/32e02d23-5bcc-4822-8993-476f5d0f3221)
![Screenshot from 2023-08-21 23-57-23](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/1e94df34-a1ff-429b-9c57-d811f8566e59)
Create 3 Elastic IPs
Create a Nat Gateway and assign one of the Elastic IPs (*The other 2 will be used by Bastion hosts)
![Screenshot from 2023-08-21 23-55-13](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/6b6006da-2ea1-41c2-b22a-1859b4e21000)

Create a Security Group for:

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
