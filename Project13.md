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
  
