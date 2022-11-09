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


