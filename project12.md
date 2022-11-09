# ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)
In this project i will continue working with ansible-config-mgt repository and make some improvements of my codes.
Here i will need to refactor my Ansible code, create assignments, and learn how to use the imports functionality. 
Imports allow to effectively re-use previously created playbooks in a new playbook – it allows you to organize your tasks and reuse them when needed.
Code Refactoring
Refactoring is a general term in computer programming. It means making changes to the source code without changing expected behaviour of the software. The main idea of refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity, add proper comments without affecting the logic.

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


```
# code block
print '3 backticks or'
print 'indent 4 spaces'
```

