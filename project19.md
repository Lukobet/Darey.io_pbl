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
I think its working now, after trying to install packer again and the version now is 1.9.4

![Screenshot from 2023-09-08 21-31-14](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/7b867061-89b1-4eaa-a13e-e0f28b461288)
![Screenshot from 2023-09-08 21-29-50](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/3199ca20-4461-4c66-a00d-bad6a5098009)
But the ami wasn't created because i didnt change the AMI name.
![Screenshot from 2023-09-09 18-23-49](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/2eb7713a-de44-4368-8e46-752a4a5ad554)
![Screenshot from 2023-09-09 19-00-24](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/be16f598-2b45-43e8-9be6-624347bf6e1b)
![Screenshot from 2023-09-09 19-13-38](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/cc201811-6079-43a2-8f0a-363532db09d9)

* Run **terraform plan** and **terraform apply** from web console

after so many editing on my documentations i finally got this result
![Screenshot from 2023-09-09 21-45-22](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/a9c63eac-c952-4518-98d7-eafcbdfec80a)
Switch to “Runs” tab and click on “Queue plan manually” button.
![Screenshot from 2023-09-09 22-25-41](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/51d1a6b3-136f-4e5c-822a-a767f1867456)

If planning has been successful, you can proceed and confirm Apply – press “Confirm and apply”, provide a comment and “Confirm plan”
Having problems with RDS AND KMS but i still went ahead with the project
![Screenshot from 2023-09-10 23-46-27](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/9a487335-24b3-4499-a301-ab678c49d317)
i have no **RDS**, **KMS** AND **EFS**
![Screenshot from 2023-09-11 00-41-43](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/6fc85ee4-9472-4f33-95b6-c55638d2b975)

SSH into the bastion server, input your acess key and seret key, update the nginx file, tooling and wordpress tooling file with yout RDS access endpoint code so that a user can be created on the mysql database

![Screenshot from 2023-09-11 00-34-15](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/c5889e5c-d99a-4973-8024-fdfcfe7774f8)

update the mounting share in **tooling/tasks/main.yml** and **wordpress/tasks/main.yml** with codes from the file system access point: click and attach and copy the fsap first and paste here ![Screenshot from 2023-09-11 00-44-54](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/f1dfd84a-5b23-4ead-a82e-f2f9200c3b42), followed by fs and paste it here ![Screenshot from 2023-09-11 00-45-56](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/652d568a-6dcf-41bb-919d-69654488b292) 
update the ansible.cfg file with the role path and ask ansible config to record the path ![Screenshot from 2023-09-11 00-53-19](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/068ade15-5cb1-43af-b0fe-acf0fc7fc719)

run the ansible playbook using
'''
ansible-playbook -i inventory/aws.ec2.yml playbooks/site.yml
'''

![Screenshot from 2023-09-16 14-34-33](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/7b6e0e27-44fb-4d11-b79a-b49f93ee5b09)
https://developer.hashicorp.com/terraform/tutorials/modules/module-private-registry-share
