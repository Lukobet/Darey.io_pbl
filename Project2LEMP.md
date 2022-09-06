Step 1_ 
I launched an instance on AWS


![1 ](https://user-images.githubusercontent.com/110517150/188638499-dcb0316a-d09c-4af2-8e4e-6d18677cca69.png)

I connected to my server through Ubuntu

![Screenshot from 2022-08-28 18-42-29](https://user-images.githubusercontent.com/110517150/188638505-25a5c6bb-28d7-422a-b49a-895ac172275c.png)

Installing the Nginx Web Server

![Screenshot from 2022-08-28 18-45-42](https://user-images.githubusercontent.com/110517150/188638509-27a7210f-4ce5-471a-bb2f-e94d1ed8aa3d.png)

![Screenshot from 2022-08-28 18-49-50](https://user-images.githubusercontent.com/110517150/188638510-defec68b-9430-4820-a034-16f2f1d82fcf.png)

The welcome note

![Screenshot from 2022-08-28 18-51-17](https://user-images.githubusercontent.com/110517150/188638514-71698388-701d-4da0-ac4e-34f74956fcfb.png)

Installing Mysql with
sudo apt install mysql-server

![Screenshot from 2022-08-28 18-55-25](https://user-images.githubusercontent.com/110517150/188638516-7c14f7cb-00d2-41d5-85d4-14f8184a9be0.png)

Installing PHP
sudo apt install php-fpm php-mysql

![Screenshot from 2022-08-28 19-00-33](https://user-images.githubusercontent.com/110517150/188638520-ec6b6ec4-39e9-45b0-815e-8460db7fdc6b.png)

# Configuring Nginx to Use PHP Processor
After the creation of the website, i disabled default Nginx host and reload Nginx to apply the changes, the website turned to this:

![Screenshot from 2022-08-28 19-09-46](https://user-images.githubusercontent.com/110517150/188638526-c09631a4-7cd0-4db2-a826-876d509cba57.png)

I had to create an index.html file using the following commands
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
And got this :


![Screenshot from 2022-08-28 19-12-35](https://user-images.githubusercontent.com/110517150/188638528-c71b6dd2-7d12-42da-a0e5-7e3c5f775ee5.png)

Testing PHP with Nginx and getting the final result

![Screenshot from 2022-08-28 19-13-46](https://user-images.githubusercontent.com/110517150/188638536-bb7884c7-053c-4d59-84a7-55b2d9bf95f0.png)

![Screenshot from 2022-08-28 19-17-18](https://user-images.githubusercontent.com/110517150/188638540-a7dfb22f-efdf-4de6-8061-8c685d06639a.png)

![Screenshot from 2022-08-28 19-17-18-1](https://user-images.githubusercontent.com/110517150/188638544-b2176c5d-10b7-4746-ab6b-a82a0f561da7.png)

Retrieving data from MySQL database with PHP

![Screenshot from 2022-08-28 19-21-59](https://user-images.githubusercontent.com/110517150/188638546-69c9c83a-d11d-4a3a-b0cf-edf46e943753.png)

![Screenshot from 2022-08-28 19-24-22](https://user-images.githubusercontent.com/110517150/188638555-1acb88c9-d7ca-4705-b182-fce26b1b4f82.png)

I encountered some difficulties at the stage of to -do list creation but was able to resolve it:

![Screenshot from 2022-08-28 19-42-25](https://user-images.githubusercontent.com/110517150/188638563-883f5b1d-1057-4a1a-b5c7-0e0580f5d2c9.png)

![Screenshot from 2022-08-28 19-46-35](https://user-images.githubusercontent.com/110517150/188638570-ef8d35e9-7cb4-4273-a929-b4bf2a199e1a.png)

My to do list script:

![Screenshot from 2022-08-28 19-48-36](https://user-images.githubusercontent.com/110517150/188638572-e543e09e-f5da-477d-81c1-808c9cb71290.png)

![Screenshot from 2022-08-28 19-50-51](https://user-images.githubusercontent.com/110517150/188638579-bf2f8c9d-e60f-4021-86ff-32fad2d1023a.png)

My to do list on the website:

![Screenshot from 2022-08-28 19-50-51-1](https://user-images.githubusercontent.com/110517150/188638584-aeddc341-9219-41be-a322-abfaedd6cca0.png)
