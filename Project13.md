## ANSIBLE DYNAMIC ASSIGNMENTS (INCLUDE) AND COMMUNITY ROLES
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

  
  
