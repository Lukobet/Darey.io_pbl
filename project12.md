# ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)
In this project i will continue working with ansible-config-mgt repository and make some improvements of my codes.
Here i will need to refactor my Ansible code, create assignments, and learn how to use the imports functionality. 
Imports allow to effectively re-use previously created playbooks in a new playbook – it allows you to organize your tasks and reuse them when needed.
Code Refactoring
Refactoring is a general term in computer programming. It means making changes to the source code without changing expected behaviour of the software. The main idea of refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity, add proper comments without affecting the logic.




### Code Refactoring
Refactoring is a general term in computer programming. It means making changes to the source code without changing expected behaviour of the software. The main idea of refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity, add proper comments without affecting the logic.

In your case, you will move things around a little bit in the code, but the overal state of the infrastructure remains the same.

Let us see how you can improve your Ansible code!

### Step 1 – Jenkins job enhancement
Before we begin, let us make some changes to our Jenkins job – now every new change in the codes creates a separate directory which is not very convenient when we want to run some commands from one place. Besides, it consumes space on Jenkins serves with each subsequent change. Let us enhance it by introducing a new Jenkins project/job – we will require Copy Artifact plugin.

1. Go to your Jenkins-Ansible server and create a new directory called ansible-config-artifact – we will store there all artifacts after each build.
```
sudo mkdir /home/ubuntu/ansible-config-artifact
```

2. Change permissions to this directory, so Jenkins could save files there – _chmod -R 0777 /home/ubuntu/ansible-config-artifact_

3. Go to Jenkins web console _-> Manage Jenkins -> Manage Plugins -> _on Available tab search for _Copy Artifact_ and install this plugin without restarting Jenkins

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/1bbd819c-f38a-4afb-a1cc-9e11d9c902ef)


4. Create a new Freestyle project (you have done it in Project 9) and name it _save_artifacts_.

5. This project will be triggered by completion of your existing ansible project. Configure it accordingly: 

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/1e4b5aa6-64e1-448d-8752-aa236dfdbbad)

**Note**: You can configure number of builds to keep in order to save space on the server, for example, you might want to keep only last 2 or 5 build results. You can also make this change to your ansible job.

6. The main idea of _save_artifacts_ project is to save artifacts into _/home/ubuntu/ansible-config-artifact_ directory. To achieve this, create a _Build_ step and choose _Copy artifacts_ from other project, specify _ansible_ as a source project and _/home/ubuntu/ansible-config-artifact _as a target directory.
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/025435a4-47db-428b-95a7-381a00a6a04b)


7. Test your set up by making some change in README.MD file inside your _ansible-config-mgt_ repository (right inside _master_ branch).
If both Jenkins jobs have completed one after another – you shall see your files inside _/home/ubuntu/ansible-config-artifact_ directory and it will be updated with every commit to your _master_ branch.

Now your Jenkins pipeline is more neat and clean.

## Step 2 – Refactor Ansible code by importing other playbooks into site.yml
Before starting to refactor the codes, ensure that you have pulled down the latest code from master (main) branch, and created a new branch, name it refactor.

DevOps philosophy implies constant iterative improvement for better efficiency – refactoring is one of the techniques that can be used, but you always have an answer to question "why?". Why do we need to change something if it works well?

In Project 11 you wrote all tasks in a single playbook common.yml, now it is pretty simple set of instructions for only 2 types of OS, but imagine you have many more tasks and you need to apply this playbook to other servers with different requirements. In this case, you will have to read through the whole playbook to check if all tasks written there are applicable and is there anything that you need to add for certain server/OS families. Very fast it will become a tedious exercise and your playbook will become messy with many commented parts. Your DevOps colleagues will not appreciate such organization of your codes and it will be difficult for them to use your playbook.

Most Ansible users learn the one-file approach first. However, breaking tasks up into different files is an excellent way to organize complex sets of tasks and reuse them.

Let see code re-use in action by importing other playbooks.

1. Within playbooks folder, create a new file and name it site.yml – This file will now be considered as an entry point into the entire infrastructure configuration. Other playbooks will be included here as a reference. In other words, site.yml will become a parent to all other playbooks that will be developed. Including common.yml that you created previously. Dont worry, you will understand more what this means shortly.

2. Create a new folder in root of the repository and name it static-assignments. The static-assignments folder is where all other children playbooks will be stored. This is merely for easy organization of your work. It is not an Ansible specific concept, therefore you can choose how you want to organize your work. You will see why the folder name has a prefix of static very soon. For now, just follow along.

3. Move common.yml file into the newly created static-assignments folder.

4. Inside site.yml file, import common.yml playbook.
```
---
- hosts: all
- import_playbook: ../static-assignments/common.yml
```

The code above uses built in import_playbook Ansible module.

Your folder structure should look like this;
```
├── static-assignments
│   └── common.yml
├── inventory
    └── dev
    └── stage
    └── uat
    └── prod
└── playbooks
    └── site.yml
```
5. Run ansible-playbook command against the _dev_ environment
Since you need to apply some tasks to your dev servers and wireshark is already installed – you can go ahead and create another playbook under static-assignments and name it common-del.yml. In this playbook, configure deletion of wireshark utility.
```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    yum:
      name: wireshark
      state: removed

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    apt:
      name: wireshark-qt
      state: absent
      autoremove: yes
      purge: yes
      autoclean: yes
```
update _site.yml_ with - import_playbook: ../static-assignments/common-del.yml instead of common.yml and run it against dev servers:
```
cd /home/ubuntu/ansible-config-mgt/

ansible-playbook -i inventory/dev.yml playbooks/site.yaml
```

Make sure that wireshark is deleted on all the servers by running ``` wireshark --version ```

Now you have learned how to use _import_playbooks_ module and you have a ready solution to install/delete packages on multiple servers with just one command.

## Step 3 – Configure UAT Webservers with a role ‘Webserver’
We have our nice and clean dev environment, so let us put it aside and configure 2 new Web Servers as uat. We could write tasks to configure Web Servers in the same playbook, but it would be too messy, instead, we will use a dedicated role to make our configuration reusable.

1. Launch 2 fresh EC2 instances using RHEL 8 image, we will use them as our uat servers, so give them names accordingly – Web1-UAT and Web2-UAT.
**Tip**: Do not forget to stop EC2 instances that you are not using at the moment to avoid paying extra. For now, you only need 2 new RHEL 8 servers as Web Servers and 1 existing Jenkins-Ansible server up and running.

2. To create a role, you must create a directory called roles/, relative to the playbook file or in /etc/ansible/ directory.
There are two ways how you can create this folder structure:

* Use an Ansible utility called ansible-galaxy inside ansible-config-mgt/roles directory (you need to create roles directory upfront)
```
mkdir roles
cd roles
ansible-galaxy init webserver
```
* Create the directory/files structure manually
Note: You can choose either way, but since you store all your codes in GitHub, it is recommended to create folders and files there rather than locally on Jenkins-Ansible server.

The entire folder structure should look like below, but if you create it manually – you can skip creating tests, files, and vars or remove them if you used ansible-galaxy
```
└── webserver
    ├── README.md
    ├── defaults
    │   └── main.yml
    ├── files
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    ├── templates
    ├── tests
    │   ├── inventory
    │   └── test.yml
    └── vars
        └── main.yml
```
After removing unnecessary directories and files, the roles structure should look like this
```
└── webserver
    ├── README.md
    ├── defaults
    │   └── main.yml
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    └── templates
```
3. Update your inventory ansible-config-mgt/inventory/uat.yml file with IP addresses of your 2 UAT Web servers
**NOTE**: Ensure you are using ssh-agent to ssh into the Jenkins-Ansible instance just as you have done in project 11;

To learn how to setup SSH agent and connect VS Code to your Jenkins-Ansible instance, please see this video:

For Windows users – ssh-agent on windows (https://youtu.be/OplGrY74qog)
For Linux users – ssh-agent on linux (https://youtu.be/OplGrY74qog)
```
[uat-webservers]
<Web1-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user' 

<Web2-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user' 
```
4. In /etc/ansible/ansible.cfg file uncomment roles_path string and provide a full path to your roles directory roles_path    = /home/ubuntu/ansible-config-mgt/roles, so Ansible could know where to find configured roles.

5. It is time to start adding some logic to the webserver role. Go into tasks directory, and within the main.yml file, start writing configuration tasks to do the following:

* Install and configure Apache (httpd service)
* Clone Tooling website from GitHub ``` https://github.com/<your-name>/tooling.git.```
* Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers.
* Make sure httpd service is started

Your main.yml may consist of following tasks:
```
---
- name: install apache
  become: true
  ansible.builtin.yum:
    name: "httpd"
    state: present

- name: install git
  become: true
  ansible.builtin.yum:
    name: "git"
    state: present

- name: clone a repo
  become: true
  ansible.builtin.git:
    repo: https://github.com/<your-name>/tooling.git
    dest: /var/www/html
    force: yes

- name: copy html content to one level up
  become: true
  command: cp -r /var/www/html/html/ /var/www/

- name: Start service httpd, if not started
  become: true
  ansible.builtin.service:
    name: httpd
    state: started

- name: recursively remove /var/www/html/html/ directory
  become: true
  ansible.builtin.file:
    path: /var/www/html/html
    state: absent
```
## Step 4 – Reference ‘Webserver’ role
Within the static-assignments folder, create a new assignment for uat-webservers uat-webservers.yml. This is where you will reference the role.
```
---
- hosts: uat-webservers
  roles:
     - webserver
```

Remember that the entry point to our ansible configuration is the site.yml file. Therefore, you need to refer your uat-webservers.yml role inside site.yml.

So, we should have this in site.yml
```
---
- hosts: all
- import_playbook: ../static-assignments/common.yml

- hosts: uat-webservers
- import_playbook: ../static-assignments/uat-webservers.yml
```

## Step 5 – Commit & Test
Commit your changes, create a Pull Request and merge them to master branch, make sure webhook triggered two consequent Jenkins jobs, they ran successfully and copied all the files to your Jenkins-Ansible server into /home/ubuntu/ansible-config-mgt/ directory.

Now run the playbook against your uat inventory and see what happens:
```
sudo ansible-playbook -i /home/ubuntu/ansible-config-mgt/inventory/uat.yml /home/ubuntu/ansible-config-mgt/playbooks/site.yaml
```

You should be able to see both of your UAT Web servers configured and you can try to reach them from your browser:
```
http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php
```
or
```
http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php
```
Your Ansible architecture now looks like this:
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/f2a953aa-26b2-44f0-b2a4-b7668776b2f8)




# results

### Task 1 : Jenkins job enhancement
![Screenshot from 2022-11-03 07-07-25](https://user-images.githubusercontent.com/110517150/199657099-c91db4b6-6f94-4563-9136-d17b2ac7e28b.png)


* create a freestyle project and name it "save_artifacts"
![Screenshot from 2022-11-09 15-11-00](https://user-images.githubusercontent.com/110517150/200857166-7b39e80b-35ad-4eb1-857d-b5ecdc595a80.png)

* create a Build step and choose Copy artifacts from other project, specify ansible as a source project and /home/ubuntu/ansible-config-artifact as a target directory.
![Screenshot from 2022-11-09 15-36-15](https://user-images.githubusercontent.com/110517150/200858875-9dfcf5b3-49f1-483a-a900-509b0adf6290.png)
![Screenshot from 2022-11-09 15-37-10](https://user-images.githubusercontent.com/110517150/200858911-f5c7a5f0-04a0-41de-927f-b778b864454d.png)
![Screenshot from 2022-11-09 15-36-15](https://user-images.githubusercontent.com/110517150/200858935-7b26bef5-a365-472d-9e9f-228359725834.png)
![Screenshot from 2022-11-09 15-37-10](https://user-images.githubusercontent.com/110517150/200858953-a19753f9-1ecc-4767-bf05-06ee3e407484.png)

*Test your set up by making some change in README.MD file inside your ansible-config-mgt repository (right inside master branch).
![Screenshot from 2022-11-03 07-08-07](https://user-images.githubusercontent.com/110517150/199657123-0265f53f-fc02-4a9a-97dd-a64eae491e08.png)
![Screenshot from 2022-11-03 07-07-49](https://user-images.githubusercontent.com/110517150/199658471-a0923dae-d4eb-4252-9642-042de2096f4c.png)

### Task 2 :Refactor Ansible code by importing other playbooks into site.yml
* Within playbooks folder, create a new file and name it site.yml – This file will now be considered as an entry point into the entire infrastructure configuration and also create a new folder in root of the repository and name it static-assignments.

![Screenshot from 2022-11-09 15-50-43](https://user-images.githubusercontent.com/110517150/200862185-523c6291-f707-4f72-b3e5-1f55e3714249.png)
* Move common.yml file into the newly created static-assignments folder.
![Screenshot from 2022-11-09 15-54-35](https://user-images.githubusercontent.com/110517150/200863329-a79922cb-6209-4546-8eaf-ac2442fabed4.png)

*Inside site.yml file, import common.yml playbook.
```
---
- hosts: all
- import_playbook: ../static-assignments/common.yml
```
![Screenshot from 2022-11-09 15-59-34](https://user-images.githubusercontent.com/110517150/200864584-d086b4e5-94d2-4657-8ec1-a084c33dc90a.png)

*Run ansible-playbook command against the dev environment

```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    yum:
      name: wireshark
      state: removed

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    apt:
      name: wireshark-qt
      state: absent
      autoremove: yes
      purge: yes
      autoclean: yes
```
![Screenshot from 2022-11-09 16-13-41](https://user-images.githubusercontent.com/110517150/200867948-cb65c1db-3bdf-4d21-8f63-fa525d91b20b.png)

Encountered a problem my remote server couldnt connect to the servers
![Screenshot from 2022-11-09 16-54-22](https://user-images.githubusercontent.com/110517150/200877698-07484091-171f-4772-88c5-862ead607f93.png)
BUt i realised i can ping witout connecting to the remote
![Screenshot from 2022-11-09 17-04-05](https://user-images.githubusercontent.com/110517150/200880123-1a1e307b-58bc-42ca-aea1-845ec0ad00e2.png)

* Running the playbook
![Screenshot from 2022-11-09 17-09-50](https://user-images.githubusercontent.com/110517150/200881407-8910dad8-cdd7-4037-84a4-7a63037afff9.png)

![Screenshot from 2022-11-09 17-08-57](https://user-images.githubusercontent.com/110517150/200881172-8f86a047-e318-40c3-8124-ed980b26dacf.png)
![Screenshot from 2022-11-09 17-10-39](https://user-images.githubusercontent.com/110517150/200881619-3b95e206-22f5-46cf-aa3a-c40311568f33.png)
![Screenshot from 2022-11-09 17-09-43](https://user-images.githubusercontent.com/110517150/200881501-aa413f93-b557-4a68-9d88-fbb9b4eede58.png)
Make sure that wireshark is deleted on all the servers by running 
```
wireshark --version
```
![Screenshot from 2022-11-09 17-13-30](https://user-images.githubusercontent.com/110517150/200882681-30dc9053-b179-4715-8e70-af7c1610fe43.png)
![Screenshot from 2022-11-09 17-15-09](https://user-images.githubusercontent.com/110517150/200882691-c788f65f-1cef-45fc-966c-e8ff152443a4.png)

### Task 3 – Configure UAT Webservers with a role ‘Webserver’

* Launch 2 new RHEL8 instances
![Screenshot from 2022-11-09 17-28-40](https://user-images.githubusercontent.com/110517150/200885917-08e21439-cfb7-4fa9-bc0f-2e4817a8ce3b.png)


* Create Folder structure
![Screenshot from 2022-11-09 17-43-25](https://user-images.githubusercontent.com/110517150/200889472-9682d4e0-811a-4e39-a68e-8ab8afb7dbbf.png)

![Screenshot from 2022-11-09 17-46-18](https://user-images.githubusercontent.com/110517150/200890114-5991720e-5cef-4686-b766-288b200967c2.png)


* Adding some logic to the webserver role by writing configuration tasks to do the following

Install and configure Apache (httpd service), Clone Tooling website from GitHub, Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers and Make sure httpd service is started
  

```
---
- name: install apache
  become: true
  ansible.builtin.yum:
    name: "httpd"
    state: present

- name: install git
  become: true
  ansible.builtin.yum:
    name: "git"
    state: present

- name: clone a repo
  become: true
  ansible.builtin.git:
    repo: https://github.com/<your-name>/tooling.git
    dest: /var/www/html
    force: yes

- name: copy html content to one level up
  become: true
  command: cp -r /var/www/html/html/ /var/www/

- name: Start service httpd, if not started
  become: true
  ansible.builtin.service:
    name: httpd
    state: started

- name: recursively remove /var/www/html/html/ directory
  become: true
  ansible.builtin.file:
    path: /var/www/html/html
    state: absent
```
![Screenshot from 2022-11-09 20-31-27](https://user-images.githubusercontent.com/110517150/200923793-fa7371ec-8ef7-4cc2-925b-3a9d2b215804.png)

### Task 4 – Reference ‘Webserver’ role

* Within the static-assignments folder, create a new assignment for uat-webservers uat-webservers.yml
```
---
- hosts: uat-webservers
  roles:
     - webserver
```
![Screenshot from 2022-11-09 20-43-27](https://user-images.githubusercontent.com/110517150/200926104-33e44a7f-b56b-4ac2-a168-c6d5c2445fbb.png)

* Add the following into the site.yml file

```
---
- hosts: all
- import_playbook: ../static-assignments/common.yml

- hosts: uat-webservers
- import_playbook: ../static-assignments/uat-webservers.yml
```
  ![Screenshot from 2022-11-09 20-42-36](https://user-images.githubusercontent.com/110517150/200925953-ef5331f0-8a91-47d8-8956-9f277a87aa83.png)

### Task 5 – Commit & Test
![Screenshot from 2022-11-09 20-50-24](https://user-images.githubusercontent.com/110517150/200927344-438f6d22-8cbb-4d16-b0a3-1ba2aaf429e0.png)

![Screenshot from 2022-11-09 20-54-48](https://user-images.githubusercontent.com/110517150/200928141-d7c76137-9fae-4ce2-977c-ecdacb23cfac.png)


* I encountered difficulty running the palybook
![Screenshot from 2022-11-09 21-10-05](https://user-images.githubusercontent.com/110517150/200931123-3d2c702f-c935-4644-af30-d23d0b1e2ff7.png)
![Screenshot from 2022-11-09 21-24-38](https://user-images.githubusercontent.com/110517150/200934600-bca12116-e453-407a-8b21-83c45bf8a551.png)

solved by correcting some typographical error
![Screenshot from 2022-11-09 21-46-57](https://user-images.githubusercontent.com/110517150/200938623-1689d565-42ad-4fd4-8801-65a7d96533ff.png)

![Screenshot from 2022-11-09 21-56-27](https://user-images.githubusercontent.com/110517150/200940027-ffb79f90-a80e-44d8-910d-eee479c69162.png)


