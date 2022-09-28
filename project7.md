# DEVOPS TOOLING WEBSITE SOLUTION

In this project i will be usinng DevOps tools that will help in day to day activities in managing, developing, testing, deploying and monitoring different projects.
This a single DevOps Tooling Solution that will consist of the following components:

* Infrastructure: AWS 
* Webserver Linux: Red Hat Enterprise Linux 8
* Database Server: Ubuntu 22.04 + MySQL
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

*Installing NFS Server

![Screenshot from 2022-09-27 15-10-16](https://user-images.githubusercontent.com/110517150/192549857-9469e33c-a5c5-46d7-bc65-ff03d63dbcf4.png)

![Screenshot from 2022-09-27 15-29-11](https://user-images.githubusercontent.com/110517150/192554480-bc840a2a-cfe0-4e5e-96ae-c19f07dceeec.png)

# Configuring the DBSERVER
![Screenshot from 2022-09-27 12-26-37](https://user-images.githubusercontent.com/110517150/192513413-cf395d67-185c-442a-9017-1f0281842d0b.png)

*Installing mysql-server, Create a database and name it tooling, Create a database user and name it webaccess
Grant permission to webaccess user on tooling database

![Screenshot from 2022-09-27 12-40-45](https://user-images.githubusercontent.com/110517150/192515935-2d767e72-0d70-4593-aeb8-d89a8e5d7620.png)


# Preparing the Webservers
*Mounted /var/www/ and target the NFS server’s export for apps

![Screenshot from 2022-09-27 17-22-37](https://user-images.githubusercontent.com/110517150/192581390-e629544a-d386-4c9a-8ced-03897c164a30.png)

*Installing apache on the 3 webservers
![Screenshot from 2022-09-27 17-19-42](https://user-images.githubusercontent.com/110517150/192580744-634b86f2-adf0-4dfc-bdf8-a8f12e755ac9.png)
![Screenshot from 2022-09-27 17-19-52](https://user-images.githubusercontent.com/110517150/192580765-fe0be780-f3c5-48d3-bd16-db1af493b1b8.png)
![Screenshot from 2022-09-27 17-19-52](https://user-images.githubusercontent.com/110517150/192580789-ca58126b-d854-4e70-a7e8-7b00a5a89c1d.png)


*Verify that Apache files and directories are available on the Web Server in /var/www and also on the NFS server in /mnt/apps.

![Screenshot from 2022-09-27 17-26-10](https://user-images.githubusercontent.com/110517150/192582136-8c41676e-d09b-4fba-b1a8-e823e0c46547.png)
![Screenshot from 2022-09-27 17-26-04](https://user-images.githubusercontent.com/110517150/192582165-eb7c9602-ac5b-41d2-9113-809add8bb0f1.png)

*Locate the log folder for Apache on the Web Server and mount it to NFS server’s export for logs. 

![Screenshot from 2022-09-27 17-33-43](https://user-images.githubusercontent.com/110517150/192584304-b120807c-afff-4121-8e75-38bf60e85d14.png)
![Screenshot from 2022-09-27 17-33-34](https://user-images.githubusercontent.com/110517150/192584337-5a1d82ef-9229-46ff-b45d-f35d92fb6da0.png)
![Screenshot from 2022-09-27 17-33-27](https://user-images.githubusercontent.com/110517150/192584361-d6b852d9-ad8d-4178-a82d-99bda0cf414f.png)

*Fork the tooling source code from *Darey.io Github Account*

![Screenshot from 2022-09-27 17-44-23](https://user-images.githubusercontent.com/110517150/192586414-a9fbb356-8ffb-4d44-b386-4409d3fb28e1.png)

Deploy the tooling website’s code to the Webserver. Ensure that the html folder from the repository is deployed to /var/www/html

![Screenshot from 2022-09-27 17-48-16](https://user-images.githubusercontent.com/110517150/192587195-caef2e1e-5ceb-4f2f-8f10-33bda1bdcb78.png)

![Screenshot from 2022-09-28 12-00-03](https://user-images.githubusercontent.com/110517150/192762742-c9e02ddd-1869-4bb9-9265-424c1a597548.png)

![Screenshot from 2022-09-28 12-02-58](https://user-images.githubusercontent.com/110517150/192763297-a35f4b02-830a-4c14-a6f9-531e18c9d461.png)
![Screenshot from 2022-09-28 12-02-58](https://user-images.githubusercontent.com/110517150/192763322-1cc9ec69-294e-404d-a38a-1d8072d9566d.png)

![Screenshot from 2022-09-28 12-05-10](https://user-images.githubusercontent.com/110517150/192763683-f82c82a7-7097-4ccf-8908-d0f033d3ac39.png)

# END GOAL
![Screenshot from 2022-09-28 12-23-38](https://user-images.githubusercontent.com/110517150/192766890-372d788f-e38e-4014-a356-d452ec733d47.png)
