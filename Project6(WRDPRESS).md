## WEB SOLUTION WITH WORDPRESS

In this project i am tasked to prepare storage infrastructure on two Linux servers and to implement a basic web solution using WordPress. 
WordPress is a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS).

This Project consists of two parts:

## Configure storage subsystem for Web and Database servers based on Linux OS. 
 1. Prepare a webserver and database server on EC2 using REDHAT as the Linux distribution.
 ![Screenshot from 2022-09-16 15-12-20](https://user-images.githubusercontent.com/110517150/192043811-d389356b-ac28-425b-bee2-5bf4c2bad667.png)

2. Creating volumes and attaching to webserver EC2 instances

![Screenshot from 2022-09-16 15-18-06](https://user-images.githubusercontent.com/110517150/192044489-85d663c1-3e57-4fac-9ae6-1994b28351c8.png)
  
3. Partition configuration

![Screenshot from 2022-09-16 15-27-07](https://user-images.githubusercontent.com/110517150/190662568-47b7499b-aab3-4fb5-b805-bf392be32ba6.png)

![Screenshot from 2022-09-16 15-34-18](https://user-images.githubusercontent.com/110517150/192044621-63725a60-3353-45d5-b71c-301f3b729da5.png)

![Screenshot from 2022-09-16 15-39-55](https://user-images.githubusercontent.com/110517150/192044688-4d6af576-fffc-438f-b997-1fbecfab9480.png)

![Screenshot from 2022-09-16 15-41-21](https://user-images.githubusercontent.com/110517150/192044726-f1faa213-cfdf-4346-8eeb-61dd21d939b1.png)

![Screenshot from 2022-09-16 15-45-05](https://user-images.githubusercontent.com/110517150/192044768-464e81dc-c57a-41f6-ae60-9415ae23519f.png)

To verify the entire setup

![Screenshot from 2022-09-16 15-27-07](https://user-images.githubusercontent.com/110517150/192044574-67a70b17-a565-43b9-8557-1651554f9903.png)

![Screenshot from 2022-09-16 15-47-30](https://user-images.githubusercontent.com/110517150/190666905-0e15003a-1726-4746-a02c-f7b6ccb85b56.png)


4. Updating the '/ETC/FSTAB` file

![Screenshot from 2022-09-16 16-01-09](https://user-images.githubusercontent.com/110517150/192161201-92f5f278-2ab6-4d4f-a456-cdb02a9335fc.png)

![Screenshot from 2022-09-16 16-01-52](https://user-images.githubusercontent.com/110517150/192161203-e6140df9-4204-4f0e-9e45-d3d65d61a8d5.png)

PREPARING THE DATABASE

 1. Create volumes for the Database server, attach the volumes to the Database server and connect to the server
 
![Screenshot from 2022-09-16 16-07-18](https://user-images.githubusercontent.com/110517150/190671342-aa1021de-1e1e-4488-b09c-fd754719a3ce.png)

![Screenshot from 2022-09-16 16-12-07](https://user-images.githubusercontent.com/110517150/190672285-76359a96-7247-47bf-b374-b3ce3f8454a8.png)

![Screenshot from 2022-09-16 16-12-07](https://user-images.githubusercontent.com/110517150/190672167-6aa22205-c861-4119-aa94-98bd09a00bb1.png)

![Screenshot from 2022-09-16 16-13-35](https://user-images.githubusercontent.com/110517150/190672663-13e49784-ed72-4283-8bde-d0daf75f2503.png)

lvm2 installed

![Screenshot from 2022-09-16 16-13-35](https://user-images.githubusercontent.com/110517150/190672695-d9c8e13d-5735-4bdd-a490-7f87defa4142.png)

Physical volume created
![Screenshot from 2022-09-16 16-16-40](https://user-images.githubusercontent.com/110517150/190673048-71cc6683-bbc7-4a14-9052-3fed12937efe.png)

Volume group created

![Screenshot from 2022-09-16 16-18-34](https://user-images.githubusercontent.com/110517150/190673448-d8a3d728-f9c6-4fcb-b4fd-f10dc688229d.png)

logical volume created

![Screenshot from 2022-09-16 16-24-02](https://user-images.githubusercontent.com/110517150/190674653-727dfd32-580e-4ca9-845c-9eb3a836ef28.png)

![Screenshot from 2022-09-16 16-24-44](https://user-images.githubusercontent.com/110517150/190674727-797623be-53b8-413a-91c6-a56167aa46a5.png)

mounting

![Screenshot from 2022-09-16 16-28-51](https://user-images.githubusercontent.com/110517150/190675436-0b3b2554-d6c7-40f7-b638-64f556b57989.png)

![Screenshot from 2022-09-16 16-36-16](https://user-images.githubusercontent.com/110517150/190676758-b38a59af-7774-4d31-8f3a-04f873d2a633.png)

* ## Install WordPress and connect it to a remote MySQL database server. 

Installing wordpress on Webserver EC2
1. Installing Apache and its dependencies

![Screenshot from 2022-09-16 16-53-24](https://user-images.githubusercontent.com/110517150/190679860-abc4973e-3fa2-45ec-aec6-f192de5f6db5.png)

installing Mysql on the Database server and enabling it

![Screenshot from 2022-09-16 17-27-38](https://user-images.githubusercontent.com/110517150/190686029-4dbb8714-e46a-48db-8500-6555bf21f0a1.png)

Configure DB to work with WordPress
![Screenshot from 2022-09-16 17-34-42](https://user-images.githubusercontent.com/110517150/190687233-5f99d333-f5f1-4ebd-b75a-c603d5db06c2.png)

![Screenshot from 2022-09-16 17-34-42](https://user-images.githubusercontent.com/110517150/190687251-28aab342-48fb-4df6-9b93-bcb2585ed838.png)

Configure WordPress to connect to remote database
.
![Screenshot from 2022-09-16 17-37-53](https://user-images.githubusercontent.com/110517150/190687805-260829e2-61cc-40e1-99ef-ae17ceb04c89.png)



![Screenshot from 2022-09-25 21-05-11](https://user-images.githubusercontent.com/110517150/192163577-a9bfff96-f68f-431f-898c-16ff9db7609c.png)

connecting to the database server from the webserver

![Screenshot from 2022-09-25 21-21-08](https://user-images.githubusercontent.com/110517150/192163580-ef0bea4c-38d5-4aee-9e4a-0a1d6a3c7fc9.png)

Configuring SELinux policies
![Screenshot from 2022-09-25 21-25-04](https://user-images.githubusercontent.com/110517150/192163685-922f2aed-8310-44da-812d-251140311193.png)

![Screenshot from 2022-09-25 21-27-24](https://user-images.githubusercontent.com/110517150/192163758-bc4829b5-5205-419b-bf9d-6f6e2dd90cfd.png)

confirming if wordpress is working and setting up a profile
![Screenshot from 2022-09-25 21-33-10](https://user-images.githubusercontent.com/110517150/192164389-b40110ee-9156-4c13-b146-a821ce18da1b.png)

![Screenshot from 2022-09-25 21-33-32](https://user-images.githubusercontent.com/110517150/192164391-e45518b3-7f63-4abb-a3d1-c1753d3e6674.png)

![Screenshot from 2022-09-25 21-36-02](https://user-images.githubusercontent.com/110517150/192164676-c00f0d6a-d0ec-42f5-a87b-c8d0b459c0cc.png)

![Screenshot from 2022-09-25 21-41-28](https://user-images.githubusercontent.com/110517150/192164677-58b471f1-77fd-4fa6-b5c9-b4831cc387ad.png)




username Tosin
o%dA(!Q#y0s2lV1Y9N
