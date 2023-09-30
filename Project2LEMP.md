# WEB STACK IMPLEMENTATION (LEMP STACK)

Project 2 covers similar concepts as Project 1 and helps to cement your skills of deploying Web solutions using LA(E)MP stacks.
You have done a great job with successful completion of Project 1.

In this project you will implement a similar stack, but with an alternative Web Server – NGINX, which is also very popular and widely used by many websites in the Internet.

**Side Self Study
Make yourself familiar with basic SQL syntax and most commonly used commands
Be comfortable using not only VIM, but also Nano editor as well, get to know basic Nano commands**


### Step 0 – Preparing prerequisites
In order to complete this project you will need an AWS account and a virtual server with Ubuntu Server OS.

If you do not have an AWS account – go back to Project 1 Step 0 to sign in to AWS free tier account and create a new EC2 Instance of t2.micro family with Ubuntu Server 22.04 LTS (HVM) image. Remember, you can have multiple EC2 instances, but make sure you STOP the ones you are not working with at the moment to save available free hours.

Hint: In previous project we used Putty on Windows to connect to our EC2 Instance, but there is a simpler way that do not require conversion of .pem key to .ppk – using Git Bash.

Download and install Git Bash like it is shown in this video.

Launch Git Bash and run following command:
```
ssh -i <Your-private-key.pem> ubuntu@<EC2-Public-IP-address>
```
It will look like this:
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/70c87809-bf57-432f-bd83-d7a899b37280)
### Step 1 – Installing the Nginx Web Server
In order to display web pages to our site visitors, we are going to employ Nginx, a high-performance web server. We’ll use the apt package manager to install this package.

Since this is our first time using apt for this session, start off by updating your server’s package index. Following that, you can use apt install to get Nginx installed:

```
$sudo apt update
$sudo apt install nginx
```
When prompted, enter Y to confirm that you want to install Nginx. Once the installation is finished, the Nginx web server will be active and running on your Ubuntu 20.04 server.

To verify that nginx was successfully installed and is running as a service in Ubuntu, run:
```
$sudo systemctl status nginx
```
If it is green and running, then you did everything correctly – you have just launched your first Web Server in the Clouds!

Before we can receive any traffic by our Web Server, we need to open TCP port 80 which is default port that web browsers use to access web pages in the Internet.

As we know, we have TCP port 22 open by default on our EC2 machine to access it via SSH, so we need to add a rule to EC2 configuration to open inbound connection through port 80:

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/7dfaf6ec-6d1c-4639-87ce-c26401cfe25a)
Our server is running and we can access it locally and from the Internet (Source 0.0.0.0/0 means ‘from any IP address’).

First, let us try to check how we can access it locally in our Ubuntu shell, run:
```
$ curl http://localhost:80
or
$ curl http://127.0.0.1:80
```
These 2 commands above actually do pretty much the same – they use ‘curl’ command to request our Nginx on port 80 (actually you can even try to not specify any port – it will work anyway). The difference is that: in the first case we try to access our server via DNS name and in the second one – by IP address (in this case IP address 127.0.0.1 corresponds to DNS name ‘localhost’ and the process of converting a DNS name to IP address is called “resolution”). We will touch DNS in further lectures and projects.

As an output you can see some strangely formatted test, do not worry, we just made sure that our Nginx web service responds to ‘curl’ command with some payload.

Now it is time for us to test how our Nginx server can respond to requests from the Internet. Open a web browser of your choice and try to access following url

```
http://<Public-IP-Address>:80
```
Another way to retrieve your Public IP address, other than to check it in AWS Web console, is to use following command:
```
curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```
The URL in browser shall also work if you do not specify port number since all web browsers use port 80 by default.

If you see following page, then your web server is now correctly installed and accessible through your firewall.
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/eb99641e-d9c5-43db-a159-d6660a5fe225)

In fact, it is the same content that you previously got by ‘curl’ command, but represented in nice HTML formatting by your web browser.

### 
```
sudo mv cfssl /usr/local/bin
```
```
sudo mv cfssl /usr/local/bin
```

```
sudo mv cfssl /usr/local/bin
``````
sudo mv cfssl /usr/local/bin
```
```
sudo mv cfssl /usr/local/bin
```

```
sudo mv cfssl /usr/local/bin
``````
sudo mv cfssl /usr/local/bin
```
```
sudo mv cfssl /usr/local/bin
```

```
sudo mv cfssl /usr/local/bin
``````
sudo mv cfssl /usr/local/bin
```
```
sudo mv cfssl /usr/local/bin
```

```
sudo mv cfssl /usr/local/bin
``````
sudo mv cfssl /usr/local/bin
```
```
sudo mv cfssl /usr/local/bin
```

```
sudo mv cfssl /usr/local/bin
``````
sudo mv cfssl /usr/local/bin
```
```
sudo mv cfssl /usr/local/bin
```

```
sudo mv cfssl /usr/local/bin
``````
sudo mv cfssl /usr/local/bin
```
```
sudo mv cfssl /usr/local/bin
```

```
sudo mv cfssl /usr/local/bin
``````
sudo mv cfssl /usr/local/bin
```
```
sudo mv cfssl /usr/local/bin
```

```
sudo mv cfssl /usr/local/bin
```
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


My to do list script:

![Screenshot from 2022-08-28 19-46-35](https://user-images.githubusercontent.com/110517150/188638570-ef8d35e9-7cb4-4273-a929-b4bf2a199e1a.png)


My to do list on the website:

![Screenshot from 2022-08-28 19-48-36](https://user-images.githubusercontent.com/110517150/188638572-e543e09e-f5da-477d-81c1-808c9cb71290.png)
