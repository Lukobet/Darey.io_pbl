# AUTOMATE INFRASTRUCTURE WITH IAC USING TERRAFORM. PART 4 – TERRAFORM CLOUD
 Terraform is an open-source system, that you installed and ran a Virtual Machine (VM) that you had to create, maintain and keep up to date. In Cloud world it is quite common to provide a managed version of an open-source software. Managed means that you do not have to install, configure and maintain it yourself – you just create an account and use it “as A Service”.

Terraform Cloud is a managed service that provides you with Terraform CLI to provision infrastructure, either on demand or in response to various events.

By default, Terraform CLI performs operation on the server when it is invoked, it is perfectly fine if you have a dedicated role who can launch it, but if you have a team who works with Terraform – you need a consistent remote environment with remote workflow and shared state to run Terraform commands.

Terraform Cloud executes Terraform commands on disposable virtual machines, this remote execution is also called remote operations.

### Task 1: Migrate your .tf codes to Terraform Cloud
* Create a Terraform Cloud account

* Create an organization
Select “Start from scratch”, choose a name for your organization and create it.

* Configure a workspace
We will use version control workflow as the most common and recommended way to run Terraform commands triggered from our git repository.

Create a new repository in your GitHub and call it terraform-cloud, push your Terraform codes developed in the previous projects to the repository.
Choose version control workflow and you will be prompted to connect your GitHub account to your workspace – follow the prompt and add your newly created repository to the workspace.

* Configure variables

Set two environment variables: **AWS_ACCESS_KEY_ID** and **AWS_SECRET_ACCESS_KEY**, set the values that you used in Project 16. These credentials will be used to provision your AWS infrastructure by Terraform Cloud.

### Task 2: Migrate your .tf codes to Terraform Cloud
### Task 3: Migrate your .tf codes to Terraform Cloud
### Task 4: Migrate your .tf codes to Terraform Cloud
### Task 5: Migrate your .tf codes to Terraform Cloud
