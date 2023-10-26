# CLIENT-SERVER ARCHITECTURE WITH MYSQL

In this project i am to implement a Client Server Architecture using MySQL Database Management System (DBMS).

A quick recap on the past projects

![Screenshot from 2022-09-10 22-58-30](https://user-images.githubusercontent.com/110517150/189503159-e382c330-121f-4a10-80a5-d7d7c8901c62.png)
![Screenshot from 2022-09-10 22-58-09](https://user-images.githubusercontent.com/110517150/189503160-5cdf0d4f-d2e9-4133-baa4-872501e01b83.png)

## Task 1: IMPLEMENT A CLIENT SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT SYSTEM (DBMS).
TASK – Implement a Client Server Architecture using MySQL Database Management System (DBMS).
To demonstrate a basic client-server using MySQL Relational Database Management System (RDBMS), follow the below instructions

Create and configure two Linux-based virtual servers (EC2 instances in AWS).
```
Server A name - `mysql server`
Server B name - `mysql client`
```

On mysql server Linux Server install MySQL Server software.
Interesting fact: MySQL is an open-source relational database management system. Its name is a combination of “My”, the name of co-founder Michael Widenius’s daughter, and “SQL”, the abbreviation for Structured Query Language.

On mysql client Linux Server install MySQL Client software.
By default, both of your EC2 virtual servers are located in the same local virtual network, so they can communicate to each other using local IP addresses. Use mysql server's local IP address to connect from mysql client. MySQL server uses TCP port 3306 by default, so you will have to open it by creating a new entry in ‘Inbound rules’ in ‘mysql server’ Security Groups. For extra security, do not allow all IP addresses to reach your ‘mysql server’ – allow access only to the specific local IP address of your ‘mysql client’.

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/4b98cef1-58f5-4a12-8feb-52be4bdb9bd8)

You might need to configure MySQL server to allow connections from remote hosts.
```
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
```
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/1a3dd129-f98a-46c3-9fea-a4e5bb12bb0f)

Replace ‘127.0.0.1’ to ‘0.0.0.0’ like this:



From mysql client Linux Server connect remotely to mysql server Database Engine without using SSH. You must use the mysql utility to perform this action.
Check that you have successfully connected to a remote MySQL server and can perform SQL queries:
```
Show databases;
```

If you see an output similar to the below image, then you have successfully completed this project – you have deloyed a fully functional MySQL Client-Server set up.

Well Done! You are getting there gradually. You can further play around with this set up and practice in creating/dropping databases & tables and inserting/selecting records to and from them.
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/585f64eb-27a5-479e-b41b-0c4e187fc40b)

## Result

![Screenshot from 2022-09-10 22-49-25](https://user-images.githubusercontent.com/110517150/189502969-d098396c-2380-4848-9913-91af72f48c8f.png)

* Connect to the Mysql server through the terminal and install MYSQL server software

![Screenshot from 2022-09-10 23-11-31](https://user-images.githubusercontent.com/110517150/189503517-8e85b9ac-12f4-43da-bcf0-42454b119674.png)

*  Connect to the Mysql client through the teminal and install MYSQL server software
  
 ![Screenshot from 2022-09-10 23-20-17](https://user-images.githubusercontent.com/110517150/189503672-b752c733-18eb-4ba6-90c2-c1d751164829.png)
 
 * Change the Mysql server security group in the inbound rules
 
![Screenshot from 2022-09-10 23-27-38](https://user-images.githubusercontent.com/110517150/189503909-c0f0401d-1a46-4e35-9364-1679a5700496.png)

 * Setting up mysql security
 
 ![Screenshot from 2022-09-10 23-31-18](https://user-images.githubusercontent.com/110517150/189504014-3d65a3ba-939d-4a8a-853a-2c8add3a25a1.png)
 
 I encountered difficulty here, so i had to start all over again by first setting up ALTER USER PASSWORD using :
 'ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PasSword.1';' on mysql before running the mysql secure installation
 
   ![Screenshot from 2022-09-16 10-56-04](https://user-images.githubusercontent.com/110517150/190613577-7b65bdb9-604f-4edd-819b-fd9611ec67f1.png)
   
![Screenshot from 2022-09-16 10-58-34](https://user-images.githubusercontent.com/110517150/190613608-bf2d4d9e-df44-4c27-8ec8-d20b6ef2506e.png)

![Screenshot from 2022-09-16 10-58-34](https://user-images.githubusercontent.com/110517150/190613638-09986e95-15cc-4267-bdcc-793a9a9f29e0.png)

Couldnt enter the mysql with sudo mysql, i had to use : 'mysql -u root -p'
![Screenshot from 2022-09-16 11-51-33](https://user-images.githubusercontent.com/110517150/190623353-61c3cf01-6a62-4ae1-9940-de465c868ab7.png)

Creating a user for the remote user

![Screenshot from 2022-09-16 11-31-06](https://user-images.githubusercontent.com/110517150/190619766-ae081ada-4687-4d18-8901-1f0bb6308bc9.png)

USER AND DATABASE CREATED

 ![Screenshot from 2022-09-16 11-33-57](https://user-images.githubusercontent.com/110517150/190620128-a03d1dc8-da4a-4f9b-844a-5a09f97ab335.png)

 * Configuring MYSQL server to allow connection from remote hosts by changing the bind address to 0.0.0.0

![Screenshot from 2022-09-16 11-41-40](https://user-images.githubusercontent.com/110517150/190621505-311f6ea9-330a-44c3-b6e4-3dc2037f0f5c.png)

 
* Connecting remotely from the mysql client Linux Server to mysql server Database Engine without using SSH. 

![Screenshot from 2022-09-16 11-40-33](https://user-images.githubusercontent.com/110517150/190621308-b9cc89cf-e0cf-43f1-a420-c0363cc6b237.png)

![Screenshot from 2022-09-16 11-42-58](https://user-images.githubusercontent.com/110517150/190621707-30f8d41e-17a8-4460-ad03-b6fa7df03788.png)
 
 
 
 
 
