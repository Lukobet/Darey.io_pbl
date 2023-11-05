# Ansible Configuration Management– Automate Project 7 to 10
Ansible 
You have been implementing some interesting projects up untill now, and that is awesome.

In Projects 7 to 10 you had to perform a lot of manual operations to seet up virtual servers, install and configure required software, deploy your web application.

This Project will make you appreciate DevOps tools even more by making most of the routine tasks automated with Ansible Configuration Management, at the same time you will become confident at writing code using declarative language such as YAML.

Let us get started!


## Ansible Client as a Jump Server (Bastion Host)
A Jump Server (sometimes also referred as Bastion Host) is an intermediary server through which access to internal network can be provided. If you think about the current architecture you are working on, ideally, the webservers would be inside a secured network which cannot be reached directly from the Internet. That means, even DevOps engineers cannot SSH into the Web servers directly and can only access it through a Jump Server – it provide better security and reduces attack surface.

On the diagram below the Virtual Private Network (VPC) is divided into two subnets – Public subnet has public IP addresses and Private subnet is only reachable by private IP addresses.

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/8b06e544-dad6-489b-9326-cbc16d8eee5e)

When you reach Project 15, you will see a Bastion host in proper action. But for now, we will develop Ansible scripts to simulate the use of a Jump box/Bastion host to access our Web Servers.

Task
* Install and configure Ansible client to act as a Jump Server/Bastion Host
* Create a simple Ansible playbook to automate servers configuration

## INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE
1. Update Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.
2. In your GitHub account create a new repository and name it ansible-config-mgt.
3. Install Ansible
```
sudo apt update
sudo apt install ansible
```
Check your Ansible version by running ``` ansible --version ```

4. Configure Jenkins build job to save your repository content every time you change it – this will solidify your Jenkins configuration skills acquired in Project 9.
* Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.
* Configure Webhook in GitHub and set webhook to trigger ansible build.
* Configure a Post-build job to save all (**) files, like you did it in Project 9.

5. Test your setup by making some change in README.MD file in master branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder
```
ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/
```
**Note**: Trigger Jenkins project execution only for /main (master) branch.

Now your setup will look like this:
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/41033f22-5dec-4fa4-8c3a-e26b66c942ef)

Tip Every time you stop/start your Jenkins-Ansible server – you have to reconfigure GitHub webhook to a new IP address, in order to avoid it, it makes sense to allocate an Elastic IP to your Jenkins-Ansible server (you have done it before to your LB server in Project 10). Note that Elastic IP is free only when it is being allocated to an EC2 Instance, so do not forget to release Elastic IP once you terminate your EC2 Instance.

## Step 2 – Prepare your development environment using Visual Studio Code
* First part of ‘DevOps’ is ‘Dev’, which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable – you need an Integrated development environment (IDE) or Source-code Editor. There is a plethora of different IDEs and Source-code Editors for different languages with their own advantages and drawbacks, you can choose whichever you are comfortable with, but we recommend one free and universal editor that will fully satisfy your needs – Visual Studio Code (VSC), you can get it here.

* After you have successfully installed VSC, configure it to connect to your newly created GitHub repository.

* Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance
```
git clone <ansible-config-mgt repo link>
```
# BEGIN ANSIBLE DEVELOPMENT
In your ansible-config-mgt GitHub repository, create a new branch that will be used for development of a new feature.
**Tip**: Give your branches descriptive and comprehensive names, for example, if you use Jira or Trello as a project management tool – include ticket number (e.g. PRJ-145) in the name of your branch and add a topic and a brief description what this branch is about – a bugfix, hotfix, feature, release (e.g. feature/prj-145-lvm)

1. Checkout the newly created feature branch to your local machine and start building your code and directory structure
2. Create a directory and name it playbooks – it will be used to store all your playbook files.
3. Create a directory and name it inventory – it will be used to keep your hosts organised.
4. Within the playbooks folder, create your first playbook, and name it common.yml
5. Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.

## Step 4 – Set up an Ansible Inventory

An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

Save below inventory structure in the inventory/dev file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.

**Note**: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host – for this you can implement the concept of ssh-agent. Now you need to import your key into ssh-agent:

To learn how to setup SSH agent and connect VS Code to your Jenkins-Ansible instance, please see this video:

For Windows users – ssh-agent on windows ( https://www.youtube.com/watch?v=OplGrY74qog&feature=youtu.be )
For Linux users – ssh-agent on linux ( https://www.youtube.com/watch?v=OplGrY74qog&feature=youtu.be )
```
eval `ssh-agent -s`
ssh-add <path-to-private-key>
```
Confirm the key has been added with the command below, you should see the name of your key
```
ssh-add -l
```

Now, ssh into your Jenkins-Ansible server using ssh-agent
```
ssh -A ubuntu@public-ip
```
Also notice, that your Load Balancer user is ubuntu and user for RHEL-based servers is ec2-user.

Update your inventory/dev.yml file with this snippet of code:
```
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
```
## Step 5 – Create a Common Playbook
It is time to start giving Ansible the instructions on what you needs to be performed on all servers listed in inventory/dev.

In common.yml playbook you will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

Update your playbooks/common.yml file with following code:
```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest
```
Examine the code above and try to make sense out of it. This playbook is divided into two parts, each of them is intended to perform the same task: install wireshark utility (or make sure it is updated to the latest version) on your RHEL 8 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 8 and apt for Ubuntu.

Feel free to update this playbook with following tasks:

Create a directory and a file inside it
Change timezone on all servers
Run some shell script
…
For a better understanding of Ansible playbooks – watch this video from RedHat and read this article.

## Step 6 – Update GIT with the latest code
Now all of your directories and files live on your machine and you need to push changes made locally to GitHub.

In the real world, you will be working within a team of other DevOps engineers and developers. It is important to learn how to collaborate with help of GIT. In many organisations there is a development rule that do not allow to deploy any code before it has been reviewed by an extra pair of eyes – it is also called "Four eyes principle".

Now you have a separate branch, you will need to know how to raise a Pull Request (PR), get your branch peer reviewed and merged to the master branch.

Commit your code into GitHub:

use git commands to add, commit and push your branch to GitHub.
```
git status

git add <selected files>

git commit -m "commit message"
```

Create a Pull request (PR)

Wear a hat of another developer for a second, and act as a reviewer.

If the reviewer is happy with your new feature development, merge the code to the master branch.

Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.

Once your code changes appear in master branch – Jenkins will do its job and save all the files (build artifacts) to /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.

## Step 7 – Run first Ansible test
Now, it is time to execute ansible-playbook command and verify if your playbook actually works:

```
cd ansible-config-mgt
ansible-playbook -i inventory/dev.yml playbooks/common.yml
```

You can go to each of the servers and check if wireshark has been installed by running which wireshark or wireshark --version

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/639f1398-cb9b-469d-ae37-91aceb1e34fb)

Your updated with Ansible architecture now looks like this:

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/e1cf4407-6a74-4a5e-b8d0-eb8a59d3f81e)

### Optional step – Repeat once again
Update your ansible playbook with some new Ansible tasks and go through the full checkout -> change codes -> commit -> PR -> merge -> build -> ansible-playbook cycle again to see how easily you can manage a servers fleet of any size with just one command!

Congratulations
You have just automated your routine tasks by implementing your first Ansible project! There is more exciting projects ahead, so lets keep it moving!


# RESULTS
### Task 1: INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE
* Change Jenkins instance to Jenkins-ansible

![Screenshot from 2022-10-26 20-07-29](https://user-images.githubusercontent.com/110517150/198114822-b7c6330a-76ed-4db6-bfd7-ce4a89da5172.png)

 * Create a Repository named "ansible-config-mgt"

![Screenshot from 2022-10-26 20-05-15](https://user-images.githubusercontent.com/110517150/198114445-57c2452c-a341-4d0e-a99a-e39d50f2bdfb.png)

* Install Ansible and verifying the version

![Screenshot from 2022-10-26 20-11-23](https://user-images.githubusercontent.com/110517150/198115643-1376ad43-2748-4b97-9de6-9b779dab13c3.png)

* Configure Jenkins and build job to save my repository
First build fails

![Screenshot from 2022-10-26 20-24-10](https://user-images.githubusercontent.com/110517150/198117989-fd4486b0-9481-44c0-ba60-ec42af1edecd.png)

Solved it by changing the branch configuration for the job

![Screenshot from 2022-10-26 20-28-13](https://user-images.githubusercontent.com/110517150/198118883-8ef67e6e-71ec-4b3b-97e0-6ccaafa60477.png)

* Made a change in the README.MD file on my repository

![Screenshot from 2022-10-26 20-32-27](https://user-images.githubusercontent.com/110517150/198119424-7940643b-2ff0-4c9e-b935-02eee7ee5f46.png)
![Screenshot from 2022-10-26 20-34-47](https://user-images.githubusercontent.com/110517150/198119904-2f3531df-fb81-40f0-be8f-bbd112dcd87b.png)
![Screenshot from 2022-10-26 20-35-05](https://user-images.githubusercontent.com/110517150/198119955-5e14932a-661b-4f62-8a6a-a1f9274be5b4.png)

* Testing to see the README.md file in the archive on the terminal

![Screenshot from 2022-10-26 20-40-34](https://user-images.githubusercontent.com/110517150/198121118-2951a5f6-9349-470f-9d51-82ac01e44758.png)

### Task 2:  Prepare your development environment using Visual Studio Code
* Here i will need write some codes which will require me to use a codding tool such as Integrated development environment (IDE) or Source-code Editor. The coding editor i will be using is the  Visual Studio Code (VSC)
![Screenshot from 2022-10-26 21-03-35](https://user-images.githubusercontent.com/110517150/198125698-6cf2672f-98ab-4749-af2f-71297a14e88a.png)
![Screenshot from 2022-10-26 21-13-24](https://user-images.githubusercontent.com/110517150/198128317-0d22061c-6c9d-4461-8c1d-15fb5326c1e8.png)

### Ansible development
* Create a new branch, create directories called 'playbooks' and 'inventory' and witin the placybook directory creae a palybook called common.yml and in the inventory directory create inventory files 
![Screenshot from 2022-10-26 21-23-20](https://user-images.githubusercontent.com/110517150/198129481-e4b949c8-9394-416e-9abe-4d143501aaa1.png)
![Screenshot from 2022-10-26 21-36-55](https://user-images.githubusercontent.com/110517150/198132421-94968777-2fe6-45a5-aabc-6542f0aa70cc.png)

### TASK 4 : SET UP ANSIBLE INVENTORY
An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

* connecting to the ssh agent through the visual studio code using the following commands
```
eval `ssh-agent -s`
ssh-add <path-to-private-key>
```
ssh into your Jenkins-Ansible server using ssh-agent

![Screenshot from 2022-10-28 03-42-55](https://user-images.githubusercontent.com/110517150/198505611-d8096267-f0aa-4ebd-9edf-a654fa2b1a25.png)

* Updating the inventory/dev.yml file with this snippet of code:
```
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
```
![Screenshot from 2022-11-01 17-08-05](https://user-images.githubusercontent.com/110517150/199281035-c09e12b6-4374-463f-a759-f1fb6340615c.png)

[Screenshot from 2022-11-01 17-10-13](https://user-images.githubusercontent.com/110517150/199281466-02fe510d-6ebd-440d-9921-74100ce56463.png)


### Task 5: Create a Common Playbook
* Update the playbooks/common.yml file with following code:
```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest
```
![Screenshot from 2022-10-28 04-05-07](https://user-images.githubusercontent.com/110517150/198505617-5d9c38f3-da14-4b78-9ab0-dff29e3700be.png)
### Task 6 – Update GIT with the latest code
* Commit the code into GitHub:

use git commands to add, commit and push your branch to GitHub.
```
git status

git add <selected files>

git commit -m "commit message"
```
![Screenshot from 2022-10-28 04-47-47](https://user-images.githubusercontent.com/110517150/198505619-0ffc87bf-b36d-4242-b71b-a2c9fb1f1adb.png)

* Create a Pull request (PR)

![Screenshot from 2022-10-28 04-54-54](https://user-images.githubusercontent.com/110517150/198505625-a04b11b0-ceae-4c3a-a63d-592ef4895fbe.png)

![Screenshot from 2022-10-28 04-56-46](https://user-images.githubusercontent.com/110517150/198505626-7e99b4b5-2952-421e-8511-ced71d8211f2.png)

![Screenshot from 2022-10-28 04-58-25](https://user-images.githubusercontent.com/110517150/198505629-59b5e5f1-233b-42c5-8304-21119a556cce.png)

![Screenshot from 2022-10-28 04-58-47](https://user-images.githubusercontent.com/110517150/198505634-dd143eb6-448f-47cf-868c-fb05c6a2b601.png)

![Screenshot from 2022-10-28 05-00-12](https://user-images.githubusercontent.com/110517150/198505636-27ca9d17-6a87-482a-96be-43f5930bf800.png)

![Screenshot from 2022-10-28 05-11-44](https://user-images.githubusercontent.com/110517150/198505650-a23866e8-1f1c-49d1-9772-e1ab15e9fe39.png)
* Confirming the build on Jenkins server

![Screenshot from 2022-10-28 05-03-39](https://user-images.githubusercontent.com/110517150/198505640-0e2f370c-b06a-4b6c-ab6d-f05e0cf9006d.png)

![Screenshot from 2022-10-28 05-07-45](https://user-images.githubusercontent.com/110517150/198505644-2800269d-2093-48d2-9e38-e4451c9bf301.png)

![Screenshot from 2022-10-28 05-09-44](https://user-images.githubusercontent.com/110517150/198505647-e0ffa95a-cce5-4c66-bb4f-562b9816702d.png)
### Task7: Run first Ansible test
using 
```
ansible-playbook -i /var/lib/jenkins/jobs/ansible/builds/6/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/6/archive/playbooks/common.yml 
```
![Screenshot from 2022-10-28 05-32-12](https://user-images.githubusercontent.com/110517150/198505651-f10510a2-363e-4788-93f7-87d7c2c82636.png)

![Screenshot from 2022-11-02 05-21-42](https://user-images.githubusercontent.com/110517150/199399991-e7e4b53a-3190-4935-bba6-3e2c30ea1511.png)
SUCCESS!!!

![Screenshot from 2022-11-02 05-50-41](https://user-images.githubusercontent.com/110517150/199401049-3ffd33ea-773f-465b-84ad-ae6e6a8a5265.png)


Confirming if Wireshark was installed using 
```
wireshark --version
```

![Screenshot from 2022-11-02 05-44-44](https://user-images.githubusercontent.com/110517150/199400667-e5c6834d-241b-4823-a5b1-27484acd6049.png)
![Screenshot from 2022-11-02 05-46-41](https://user-images.githubusercontent.com/110517150/199400674-c558ba00-3bd8-4df9-8b89-4bce0e259cc9.png)
![Screenshot from 2022-11-02 05-46-03](https://user-images.githubusercontent.com/110517150/199400690-2fbb625e-67cd-496b-9f43-0a5d2452ac18.png)

Optional steps
#### Steps
* checkout -> change codes by adding
 ```
- name: create directory, file and set timezone on all servers
  hosts: webservers, nfs, db, lb
  become: yes
  tasks:

    - name: create a directory
      file: 
        path: /home/sample
        state: directory

    - name: create a file
      file: 
        path: /home/sample/ansible.txt
        state: touch

    - name: set timezone
      timezone: 
        name: Africa/Lagos  
```
-> commit -> PR -> merge -> build 
![Screenshot from 2022-11-02 21-14-57](https://user-images.githubusercontent.com/110517150/199593832-7132a731-bbf2-49b1-9c89-0d3256b0bad5.png)

![Screenshot from 2022-11-02 21-16-08](https://user-images.githubusercontent.com/110517150/199593886-1a7c4c72-8873-4d0a-b00c-7297fba9ccd3.png)
![Screenshot from 2022-11-02 22-04-46](https://user-images.githubusercontent.com/110517150/199602314-3ec799fd-b9ef-426c-9a7f-521cab6c2595.png)
![Screenshot from 2022-11-02 22-06-02](https://user-images.githubusercontent.com/110517150/199602507-90e7d4a2-c84b-4aba-a9a9-80441beacaea.png)

-> Ansible-playbook
![Screenshot from 2022-11-02 22-27-13](https://user-images.githubusercontent.com/110517150/199605880-358ab509-592f-4fd6-beef-7f250ba7c17d.png)
![Screenshot from 2022-11-02 22-35-26](https://user-images.githubusercontent.com/110517150/199607031-ab70371d-beb9-4dbf-982b-66a4a48c02c6.png)

* confirming
* 
![Screenshot from 2022-11-02 22-38-12](https://user-images.githubusercontent.com/110517150/199607597-eb4b1703-eea5-4e4f-9968-018cdb9c27c4.png)

![Screenshot from 2022-11-02 22-44-08](https://user-images.githubusercontent.com/110517150/199608388-96f5f8e0-1f91-4782-a830-db76823e3547.png)
