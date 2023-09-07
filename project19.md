# AUTOMATE INFRASTRUCTURE WITH IAC USING TERRAFORM. PART 4 – TERRAFORM CLOUD
 Terraform is an open-source system, that you installed and ran a Virtual Machine (VM) that you had to create, maintain and keep up to date. In Cloud world it is quite common to provide a managed version of an open-source software. Managed means that you do not have to install, configure and maintain it yourself – you just create an account and use it “as A Service”.

Terraform Cloud is a managed service that provides you with Terraform CLI to provision infrastructure, either on demand or in response to various events.

By default, Terraform CLI performs operation on the server when it is invoked, it is perfectly fine if you have a dedicated role who can launch it, but if you have a team who works with Terraform – you need a consistent remote environment with remote workflow and shared state to run Terraform commands.

Terraform Cloud executes Terraform commands on disposable virtual machines, this remote execution is also called remote operations.

### Task 1: Migrate your .tf codes to Terraform Cloud
* Create a Terraform Cloud account
![Screenshot from 2023-09-05 04-44-23](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/5f5b7b81-9b91-49fa-ad22-f9d103330f36)
* Create an organization
![Screenshot from 2023-09-05 04-46-48](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/5d0004a0-3b47-43be-8b7b-ff1ebbcfc6cf)

* Configure a workspace
We will use version control workflow as the most common and recommended way to run Terraform commands triggered from our git repository.

Create a new repository in your GitHub and call it **terraform-cloud,** push your Terraform codes developed in the previous projects to the repository.

![Screenshot from 2023-09-05 04-48-30](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/0baaafd3-d507-4894-9697-f8ab3bffea68)

Choose version control workflow and you will be prompted to connect your GitHub account to your workspace – follow the prompt and add your newly created repository to the workspace.

![Screenshot from 2023-09-05 04-54-42](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/719f1803-3acf-4cec-9376-bca8f7577f62)
* Configure variables
Set two environment variables: **AWS_ACCESS_KEY_ID** and **AWS_SECRET_ACCESS_KEY**,

![Screenshot from 2023-09-06 01-50-46](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/9db2e82a-35a8-41fb-8955-bbeed25e931d)

* Install Packer and Ansible on the local machine
 ![Screenshot from 2023-09-06 01-25-14](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/807f2092-3281-493c-a3b2-020ed0cc1399)
  We will be using Packer to build our images, and Ansible to configure the infrastructure
The files that would be Added is;

AMI: for building packer images
Ansible: for Ansible scripts to configure the infrastructure

I got this error while trying to build
![Screenshot from 2023-09-06 02-22-04](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/ebd9e4fc-e883-48aa-bc98-fe885c0e587e)



### Task 2: Migrate your .tf codes to Terraform Cloud
### Task 3: Migrate your .tf codes to Terraform Cloud
### Task 4: Migrate your .tf codes to Terraform Cloud
### Task 5: Migrate your .tf codes to Terraform Cloud