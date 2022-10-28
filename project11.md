# Ansible Configuration Management– Automate Project 7 to 10
Ansible 

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

* Update your inventory/dev.yml file with this snippet of code:
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

![Screenshot from 2022-10-28 05-32-12](https://user-images.githubusercontent.com/110517150/198505651-f10510a2-363e-4788-93f7-87d7c2c82636.png)

