# DEVOPS TOOLING WEBSITE SOLUTION

In this project i will be usinng DevOps tools that will help in day to day activities in managing, developing, testing, deploying and monitoring different projects.
This a single DevOps Tooling Solution that will consist of the following components:

* Infrastructure: AWS 
* Webserver Linux: Red Hat Enterprise Linux 8
* Database Server: Ubuntu 20.04 + MySQL
* Storage Server: Red Hat Enterprise Linux 8 + NFS Server
* Programming Language: PHP
* Code Repository: GitHub

##  Infrastructure: AWS , Webserver Linux, Database server and storage server.
![Screenshot from 2022-09-27 11-04-06](https://user-images.githubusercontent.com/110517150/192497491-79caf5a7-b2a7-452b-90cf-b219ee135178.png)

ssh into the NFS server after adding volumes

![Screenshot from 2022-09-27 11-02-11](https://user-images.githubusercontent.com/110517150/192497295-3f453ffc-c252-437f-9dd3-4f2564dcfb65.png)

![Screenshot from 2022-09-27 11-06-49](https://user-images.githubusercontent.com/110517150/192498190-0c5db8bf-2f78-4c28-a0f1-4268ed5e1d6e.png)

# Configuring LVM on the server 
* configuring disk partitions

![Screenshot from 2022-09-27 11-10-36](https://user-images.githubusercontent.com/110517150/192499072-93ff3306-80b7-418f-b777-9a8eccc63800.png)

* Installing LVM2 to check for available partitions
![Screenshot from 2022-09-27 11-13-04](https://user-images.githubusercontent.com/110517150/192499385-331cbd72-e64e-403d-b227-194a0704b34d.png)

* Using pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM and usin vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

![Screenshot from 2022-09-27 11-15-26](https://user-images.githubusercontent.com/110517150/192500926-bfed4871-581a-4089-b15c-9337fc73741f.png)

Creating 3 logical volumes

![Screenshot from 2022-09-27 11-17-48](https://user-images.githubusercontent.com/110517150/192500340-76ed058c-4e2e-4d7e-b870-3daced40b636.png)
i encountered a problem when i used 14G for each logical voulmes i created for WEBDATA-VG
It was resolved when i removed them 
![Screenshot from 2022-09-27 11-42-13](https://user-images.githubusercontent.com/110517150/192505191-7dcdc9a2-75e3-4869-a2de-9cc4260e5555.png)


Created 3 LOgical voulmes(lv-opt, lv-apps, lv-logs)

![Screenshot from 2022-09-27 11-45-13](https://user-images.githubusercontent.com/110517150/192505754-7b12d356-83c8-46d3-b7b2-237c8edccf64.png)

![Screenshot from 2022-09-27 11-45-13](https://user-images.githubusercontent.com/110517150/192506310-b2b7d3dd-cd9b-4855-8ef1-a9c61c0c0fa2.png)

![Screenshot from 2022-09-27 11-46-25](https://user-images.githubusercontent.com/110517150/192506185-c21a6c81-c639-4f99-89dd-beabb0b94139.png)

Formatting the disk as xfS

![Screenshot from 2022-09-27 11-52-54](https://user-images.githubusercontent.com/110517150/192507184-adf0a504-8305-48b1-96fe-55f03939b924.png)

Create mount points on /mnt directory for the logical volumes 
![Screenshot from 2022-09-27 12-01-11](https://user-images.githubusercontent.com/110517150/192508718-830e9ee2-3a82-473f-973d-8c427311a9e1.png)

*Installing NFS Server and NFS utils














# Configuring the DBSERVER
![Screenshot from 2022-09-27 12-26-37](https://user-images.githubusercontent.com/110517150/192513413-cf395d67-185c-442a-9017-1f0281842d0b.png)

*Installing mysql-server, Create a database and name it tooling, Create a database user and name it webaccess
Grant permission to webaccess user on tooling database

![Screenshot from 2022-09-27 12-40-45](https://user-images.githubusercontent.com/110517150/192515935-2d767e72-0d70-4593-aeb8-d89a8e5d7620.png)




