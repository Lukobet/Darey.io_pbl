## ANSIBLE DYNAMIC ASSIGNMENTS (INCLUDE) AND COMMUNITY ROLES
IMPORTANT NOTICE: Ansible is an actively developing software project, so you are encouraged to visit Ansible Documentation for the latest updates on modules and their usage.

Last 2 projects have already equipped you with some knowledge and skills on Ansible, so you can perform configurations using playbooks, roles and imports. Now you will continue configuring your UAT servers learning and practicing new Ansible concepts and modules.

In this project we will introduce dynamic assignments by using include module.

Now you may be wondering, what is the difference between static and dynamic assignments?

Well, from Project 12, you can already tell that static assignments use import Ansible module. The module that enables dynamic assignments is include.

Hence,

import = Static
include = Dynamic
When the import module is used, all statements are pre-processed at the time playbooks are parsed. Meaning, when you execute site.yml playbook, Ansible will process all the playbooks referenced during the time it is parsing the statements. This also means that, during actual execution, if any statement changes, such statements will not be considered. Hence, it is static.

On the other hand, when include module is used, all statements are processed only during execution of the playbook. Meaning, after the statements are parsed, any changes to the statements encountered during execution will be used.

Take note that in most cases it is recommended to use static assignments for playbooks, because it is more reliable. With dynamic ones, it is hard to debug playbook problems due to its dynamic nature. However, you can use dynamic assignments for environment specific variables as we will be introducing in this project.

# INTRODUCING DYNAMIC ASSIGNMENT INTO OUR STRUCTURE
Introducing Dynamic Assignment Into Our structure
In your https://github.com/<your-name>/ansible-config-mgt GitHub repository start a new branch and call it dynamic-assignments.

Create a new folder, name it dynamic-assignments. Then inside this folder, create a new file and name it env-vars.yml. We will instruct site.yml to include this playbook later. For now, let us keep building up the structure.

Your GitHub shall have following structure by now.

**Note**: Depending on what method you used in the previous project you may have or not have roles folder in your GitHub repository – if you used ansible-galaxy, then roles directory was only created on your Jenkins-Ansible server locally. It is recommended to have all the codes managed and tracked in GitHub, so you might want to recreate this structure manually in this case – it is up to you.
```
├── dynamic-assignments
│   └── env-vars.yml
├── inventory
│   └── dev
    └── stage
    └── uat
    └── prod
└── playbooks
    └── site.yml
└── roles (optional folder)
    └──...(optional subfolders & files)
└── static-assignments
    └── common.yml
```

Since we will be using the same Ansible to configure multiple environments, and each of these environments will have certain unique attributes, such as servername, ip-address etc., we will need a way to set values to variables per specific environment.

For this reason, we will now create a folder to keep each environment’s variables file. Therefore, create a new folder env-vars, then for each environment, create new YAML files which we will use to set variables.

Your layout should now look like this.
```
├── dynamic-assignments
│   └── env-vars.yml
├── env-vars
    └── dev.yml
    └── stage.yml
    └── uat.yml
    └── prod.yml
├── inventory
    └── dev
    └── stage
    └── uat
    └── prod
├── playbooks
    └── site.yml
└── static-assignments
    └── common.yml
    └── webservers.yml
```
Now paste the instruction below into the env-vars.yml file.
```
---
- name: collate variables from env specific file, if it exists
  hosts: all
  tasks:
    - name: looping through list of available files
      include_vars: "{{ item }}"
      with_first_found:
        - files:
            - dev.yml
            - stage.yml
            - prod.yml
            - uat.yml
          paths:
            - "{{ playbook_dir }}/../env-vars"
      tags:
        - always
```

Notice 3 things to notice here:
1. We used include_vars syntax instead of include, this is because Ansible developers decided to separate different features of the module. From Ansible version 2.8, the include module is deprecated and variants of include_* must be used. These are:
include_role
include_tasks
include_vars
In the same version, variants of import were also introduces, such as:

import_role
import_tasks
2. We made use of a special variables { playbook_dir } and { inventory_file }. { playbook_dir } will help Ansible to determine the location of the running playbook, and from there navigate to other path on the filesystem. { inventory_file } on the other hand will dynamically resolve to the name of the inventory file being used, then append .yml so that it picks up the required file within the env-vars folder.
3. We are including the variables using a loop. with_first_found implies that, looping through the list of files, the first one found is used. This is good so that we can always set default values in case an environment specific env file does not exist.

# Update site.yml with dynamic assignments
Update site.yml file to make use of the dynamic assignment. (At this point, we cannot test it yet. We are just setting the stage for what is yet to come. So hang on to your hats)

site.yml should now look like this.
```
---
- hosts: all
- name: Include dynamic variables 
  tasks:
  import_playbook: ../static-assignments/common.yml 
  include: ../dynamic-assignments/env-vars.yml
  tags:
    - always

-  hosts: webservers
- name: Webserver assignment
  import_playbook: ../static-assignments/webservers.yml
```
# Community Roles
Now it is time to create a role for MySQL database – it should install the MySQL package, create a database and configure users. But why should we re-invent the wheel? There are tons of roles that have already been developed by other open source engineers out there. These roles are actually production ready, and dynamic to accomodate most of Linux flavours. With Ansible Galaxy again, we can simply download a ready to use ansible role, and keep going.

Download Mysql Ansible Role
You can browse available community roles here https://galaxy.ansible.com/ui/

We will be using a MySQL role developed by geerlingguy.  https://galaxy.ansible.com/ui/standalone/roles/geerlingguy/mysql/

**Hint**: To preserve your your GitHub in actual state after you install a new role – make a commit and push to master your ‘ansible-config-mgt’ directory. Of course you must have git installed and configured on Jenkins-Ansible server and, for more convenient work with codes, you can configure Visual Studio Code to work with this directory. In this case, you will no longer need webhook and Jenkins jobs to update your codes on Jenkins-Ansible server, so you can disable it – we will be using Jenkins later for a better purpose.

On Jenkins-Ansible server make sure that git is installed with git --version, then go to ‘ansible-config-mgt’ directory and run
```
git init
git pull https://github.com/<your-name>/ansible-config-mgt.git
git remote add origin https://github.com/<your-name>/ansible-config-mgt.git
git branch roles-feature
git switch roles-feature
```

Inside roles directory create your new MySQL role with ansible-galaxy install geerlingguy.mysql and rename the folder to mysql
```
mv geerlingguy.mysql/ mysql
```
Read README.md file, and edit roles configuration to use correct credentials for MySQL required for the tooling website.

Now it is time to upload the changes into your GitHub:
```
git add .
git commit -m "Commit new role files into GitHub"
git push --set-upstream origin roles-feature
```

Now, if you are satisfied with your codes, you can create a Pull Request and merge it to main branch on GitHub.
# Load Balancer roles
We want to be able to choose which Load Balancer to use, Nginx or Apache, so we need to have two roles respectively:

_Nginx_
_Apache_
With your experience on Ansible so far you can:

Decide if you want to develop your own roles, or find available ones from the community
Update both static-assignment and site.yml files to refer the roles
Important Hints:

Since you cannot use both Nginx and Apache load balancer, you need to add a condition to enable either one – this is where you can make use of variables.
Declare a variable in defaults/main.yml file inside the Nginx and Apache roles. Name each variables enable_nginx_lb and enable_apache_lb respectively.
Set both values to false like this enable_nginx_lb: false and enable_apache_lb: false.
Declare another variable in both roles load_balancer_is_required and set its value to false as well
Update both assignment and site.yml files respectively

loadbalancers.yml file
```
- hosts: lb
  roles:
    - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
    - { role: apache, when: enable_apache_lb and load_balancer_is_required }
```
site.yml file
```
     - name: Loadbalancers assignment
       hosts: lb
         - import_playbook: ../static-assignments/loadbalancers.yml
        when: load_balancer_is_required 
```

Now you can make use of env-vars\uat.yml file to define which loadbalancer to use in UAT environment by setting respective environmental variable to true.

You will activate load balancer, and enable nginx by setting these in the respective environment’s env-vars file.
```
enable_nginx_lb: true
load_balancer_is_required: true
```

The same must work with apache LB, so you can switch it by setting respective environmental variable to true and other to false.

To test this, you can update inventory for each environment and run Ansible against each environment.

Congratulations!
You have learned and practiced how to use Ansible configuration management tool to prepare UAT environment for Tooling web solution.



# RESULTS
### Task 1: INTRODUCING DYNAMIC ASSIGNMENT INTO OUR STRUCTURE

* In the https://github.com/<your-name>/ansible-config-mgt GitHub repository start a new branch named "dynamic-assignments".

* Create a new folder, name it dynamic-assignments. Then inside this folder, create a new file and name it env-vars.yml. 
![Screenshot from 2023-04-18 23-58-06](https://user-images.githubusercontent.com/110517150/232922782-5b08e2a5-b53a-4d3b-9823-1ecc88842889.png)

 * There will be a way to set values to variables per specific environment and for this reason, i will now create a folder to keep each environment’s variables file. Therefore, create a new folder 'env-vars' and in it creat files and name them dev.yml, stage.yml, uat.yml, prod.yml
  
  ![Screenshot from 2023-04-19 00-07-30](https://user-images.githubusercontent.com/110517150/232923864-143b841e-026b-4368-a76d-076bc97e3fb3.png)
  
  * Paste the instruction below into the env-vars.yml file.
  ```
---
- name: collate variables from env specific file, if it exists
  hosts: all
  tasks:
    - name: looping through list of available files
      include_vars: "{{ item }}"
      with_first_found:
        - files:
            - dev.yml
            - stage.yml
            - prod.yml
            - uat.yml
          paths:
            - "{{ playbook_dir }}/../env-vars"
      tags:
        - always
```
  ![Screenshot from 2023-04-19 00-13-14](https://user-images.githubusercontent.com/110517150/232924520-ecb2fe16-19d7-439c-b724-3a3bc7d1b3f8.png)

### Task 2: Update site.yml with dynamic assignments
* Update Site.yml with the following snippet
```
  ---
- hosts: all
- name: Include dynamic variables 
  tasks:
  import_playbook: ../static-assignments/common.yml 
  include: ../dynamic-assignments/env-vars.yml
  tags:
    - always

-  hosts: webservers
- name: Webserver assignment
  import_playbook: ../static-assignments/webservers.yml
  
```
 ![Screenshot from 2023-04-19 00-50-20](https://user-images.githubusercontent.com/110517150/232928816-1cef6ca2-7b22-49d9-8bc7-e576832dd634.png)

  
  git pull
  
  ### Task 3: Create a Community Role for MySQL database
  * Download Mysql Ansible 
  ![Screenshot from 2023-04-19 01-03-49](https://user-images.githubusercontent.com/110517150/232930352-b9ddb265-48ba-4081-8f3c-291c9b573d7b.png)
  
  i encounteed problems with the downloadbecause i didnt include sudo
  ![Screenshot from 2023-04-19 01-05-21](https://user-images.githubusercontent.com/110517150/232930555-9be8475e-1e18-4fdf-bd17-265dc02a2487.png)

  i also encountered a problem in the change of geerlingguy.mysql file to mysql
![Screenshot from 2023-04-29 21-49-28](https://user-images.githubusercontent.com/110517150/235323681-208f28eb-4855-42d0-8080-a99b5fbc8b73.png)

I realised that it was downloaded into ansible-config-artifact path
 ![Screenshot from 2023-04-29 22-20-57](https://user-images.githubusercontent.com/110517150/235324758-92144bd8-2b5e-4103-af26-870b1e5073b6.png) 
  
  ![Screenshot from 2023-04-29 22-22-36](https://user-images.githubusercontent.com/110517150/235324792-e9791cd5-97bb-42a7-8b06-19989d633b06.png)
  
  Renaming of the file done successfully
  ![Screenshot from 2023-04-29 22-23-56](https://user-images.githubusercontent.com/110517150/235324838-ef8271f3-9e21-46ee-b8c9-629d853e9717.png)
  
  I finally got the solution to my problems
  ![Screenshot from 2023-05-01 22-46-49](https://user-images.githubusercontent.com/110517150/235537495-b044289a-49e1-4cb0-8d09-0d3ed1b6a6bc.png)

  But still i couldnt move forward, i had to go back again to project 9 to start all over again
  Now here
  
![Screenshot from 2023-05-22 22-16-42](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/1b653493-6f26-488d-86af-412d82129e37)
  
  
  moving forward, what is needed to complete the task 
  ![Screenshot from 2023-08-21 15-10-30](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/930b1440-5964-4d0d-b084-2679ea3de960)
![Screenshot from 2023-08-21 15-10-18](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/eab0bda8-68fc-49e2-920a-3927ed895443)
![Screenshot from 2023-08-21 15-10-05](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/f8312638-85dd-4f90-b9f9-6417eec0fb33)

* Update loadbalancers.yml file with the following snippet
```
- hosts: lb
  roles:
    - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
    - { role: apache, when: enable_apache_lb and load_balancer_is_required }
```


* Update Site.yml with the following snippet
```
 enable_nginx_lb: true
load_balancer_is_required: true
  
```
  Now you can make use of env-vars\uat.yml file to define which loadbalancer to use in UAT environment by setting respective environmental variable to true.

You will activate load balancer, and enable nginx by setting these in the respective environment’s env-vars file

