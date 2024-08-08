
# WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS
Welcome to your very first PBL Project
You must be really excited to start getting your hands dirty. There is a lot of projects ahead, so without any delay, lets get started.

As you kick off your career in DevOps, you will soon realize that everything you will be doing as a DevOps engineer is around *software*, *websites*, *applications* etc. And, there are different stack of technologies that make different solutions possible. These stack of technologies are regarded as WEB STACKS. Examples of Web Stacks include LAMP, LEMP, MEAN, and MERN stacks. As you proceed in your journey, you will be required to understand and implement all of these technology stacks. Lets have a short close up on what a Technology stack is.

What is a Technology stack?
A technology stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically chosen to work together in creating a well-functioning software. They are acronymns for individual technologies used together for a specific technology product. some examples are…

**LAMP (Linux, Apache, MySQL, PHP or Python, or Perl)**
**LEMP (Linux, Nginx, MySQL, PHP or Python, or Perl)**
**MERN (MongoDB, ExpressJS, ReactJS, NodeJS)**
**MEAN (MongoDB, ExpressJS, AngularJS, NodeJS**

WARNING: Most of the things you will be doing at the early days may not mean a lot to you. Sometimes it may seem like you are just copying and pasting. That is absolutely fine. We want some concepts to begin to register in your sub-conscious mind, and without you realising it, you are building up skills. Although, there are certain traps that will require you to troubleshoot along the way. So watch out for them in all your project implementations.

After successful completion of PBL projects 1 to 4, you will be able to achieve the following.
1. Become very confident on the Linux Terminal
2. Deepen your understanding of Web Stacks and familiarity between the differences between the different Web Technology stacks such as LAMP, LEMP, MEAN, and MERN stacks
3. Solid Linux administration skills in Storage management, NFS, troubleshooting, and basic networking.
4. Basic knowledge of AWS platform and components used to host a Website of various Web stacks.

Being able to work with Linux requires the ability to work outside the level of your present knowledge. It means that in the real world, you will be faced with tasks that you have never worked on before, and with Google search and its results, you can achieve a lot. Thanks to “Google!!!”. It is one of the essential skills you will need to develop – constructing a correct search query for Google to process and having the ability to comb through resources that interpretes into a potential solution for you is a great skill to have as well.

### Step 0 – Preparing prerequisites
In order to complete this project you will need an AWS account and a virtual server with Ubuntu Server OS.

AWS is the biggest Cloud Service Provider and it offers a free tier account that we are going to leverage for our projects.

Do not focus too much on AWS itself right now, there will be a proper Cloud introduction and configuration projects later in our course.

Right now, all we need to know is that AWS can provide us with a free virtual server called EC2 (Elastic Compute Cloud) for our needs.

Spinning up a new EC2 instance (an instance of a virtual server) is only a matter of a few clicks.

You can either Watch the videos below to get yourself set up.

1. AWS account setup and Provisioning an Ubuntu Server
2. Connecting to your EC2 Instance

Or follow the instructions below.

1. Register a new AWS account following this instruction.
2. Select your preferred region (the closest to you) and launch a new EC2 instance of t2.micro family with Ubuntu Server 20.04 LTS (HVM)
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/2edb4474-9d90-490d-a62d-4613cc80328c)

IMPORTANT – save your private key (.pem file) securely and do not share it with anyone! If you lose it, you will not be able to connect to your server ever again!

1. For Windows users, you will need a tool called putty to connect to your EC2 Instance. Download Putty Here.
2. For Mac users, you can simply open up Terminal and use the ssh command to get into the server.

**IMPORTANT NOTICE**
Both Putty and ssh use the SSH protocol to establish connectivity between computers. It is the most secure protocol because it uses crypto algorithms to encrypt the data that is transmitted – it uses TCP port 22 which is open for all newly created EC2 intances in AWS by default. Most of these terminologies will make more sense to you as you proceed. So for now, if nothing makes sense, just ignore. But be assured that the information is already registered in your sub-conscious mind. So it will become useful to you soon.

The process to connect to the virtual server is different between Windows and Mac. So lets take a quick tour.
**Side Self Study**
1. Conduct a Google search on what software development life cycle (SDLC) is and document your finding in a Google word file.
2. Conduct another Google search, understand what LAMP stack means.
3. Read about ‘chmod’ and ‘chown’ commands in Linux and understand how access and ownership of files and directories work.
4. Learn what TCP and UPD terms mean and how they are different. List down ports most commonly used in Web (http, https, ssh, telnet, ftp, sftp, telnet)
5. Get yourself familiar with basic text editing in Vi (Vim) editor.


   
### STEP 1. INSTANCE CREATION AND CONNECTION
What exactly is Apache?

Apache HTTP Server is the most widely used web server software. Developed and maintained by Apache Software Foundation, Apache is an open source software available for free. It runs on 67% of all webservers in the world. It is fast, reliable, and secure. It can be highly customized to meet the needs of many different environments by using extensions and modules. Most WordPress hosting providers use Apache as their web server software. However, websites and other applications can run on other web server software as well. Such as Nginx, Microsoft’s IIS, etc.

The Apache web server is among the most popular web servers in the world. It’s well documented, has an active community of users, and has been in wide use for much of the history of the web, which makes it a great default choice for hosting a website.

Install Apache using Ubuntu’s package manager ‘apt’:
```
#update a list of packages in package manager
sudo apt update

#run apache2 package installation
sudo apt install apache2
```
To verify that apache2 is running as a Service in our OS, use following command

```
sudo systemctl status apache2
```
If it is green and running, then you did everything correctly – you have just launched your first Web Server in the Clouds!

Before we can receive any traffic by our Web Server, we need to open TCP port 80 which is the default port that web browsers use to access web pages on the Internet

As we know, we have TCP port 22 open by default on our EC2 machine to access it via SSH, so we need to add a rule to EC2 configuration to open inbound connection through port 80:

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/1777ecb8-897a-4efc-bda8-d270711fe611)

Our server is running and we can access it locally and from the Internet (Source 0.0.0.0/0 means ‘from any IP address’).

First, let us try to check how we can access it locally in our Ubuntu shell, run:
```
curl http://localhost:80
or
 curl http://127.0.0.1:80
```

These 2 commands above actually do pretty much the same – they use ‘curl’ command to request our Apache HTTP Server on port 80 (actually you can even try to not specify any port – it will work anyway). The difference is that: in the first case we try to access our server via DNS name and in the second one – by IP address (in this case IP address 127.0.0.1 corresponds to DNS name ‘localhost’ and the process of converting a DNS name to IP address is called “resolution”). We will touch DNS in further lectures and projects.

As an output you can see some strangely formatted test, do not worry, we just made sure that our Apache web service responds to ‘curl’ command with some payload.

Now it is time for us to test how our Apache HTTP server can respond to requests from the Internet.
Open a web browser of your choice and try to access following url
```
(http://<Public-IP-Address>:80)
```
Another way to retrieve your Public IP address, other than to check it in AWS Web console, is to use following command:
```
curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```

The URL in browser shall also work if you do not specify port number since all web browsers use port 80 by default.

If you see following page, then your web server is now correctly installed and accessible through your firewall.

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/e5bb3710-e826-4335-9065-4da72dbd826c)

In fact, it is the same content that you previously got by ‘curl’ command, but represented in nice HTML formatting by your web browser.


result= ![Screenshot from 2022-08-28 15-08-38](https://user-images.githubusercontent.com/110517150/188636550-7e94a85b-f0da-47f8-a660-3efb5a28ac23.png)
![Screenshot from 2022-08-28 15-08-38](https://user-images.githubusercontent.com/110517150/188636550-7e94a85b-f0da-47f8-a660-3efb5a28ac23.png)


### STEP 2 — INSTALLING MYSQL
Now that you have a web server up and running, you need to install a Database Management System (DBMS) to be able to store and manage data for your site in a relational database. MySQL is a popular relational database management system used within PHP environments, so we will use it in our project.

Again, use ‘apt’ to acquire and install this software:


```
$ sudo apt install mysql-server
```
When prompted, confirm installation by typing Y, and then ENTER.

When the installation is finished, log in to the MySQL console by typing:

```
$ sudo mysql
```
This will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running this command. You should see output like this:
```
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.22-0ubuntu0.20.04.3 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```
It’s recommended that you run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system. Before running the script you will set a password for the root user, using mysql_native_password as default authentication method. We’re defining this user’s password as PassWord.1.

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
```
Exit the MySQL shell with:

```
mysql> exit
```
Start the interactive script by running:

```
$ sudo mysql_secure_installation
```
This will ask if you want to configure the VALIDATE PASSWORD PLUGIN.

Note: Enabling this feature is something of a judgment call. If enabled, passwords which don’t match the specified criteria will be rejected by MySQL with an error. It is safe to leave validation disabled, but you should always use strong, unique passwords for database credentials.

Answer Y for yes, or anything else to continue without enabling.

```
VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?

Press y|Y for Yes, any other key for No:
```

If you answer “yes”, you’ll be asked to select a level of password validation. Keep in mind that if you enter 2 for the strongest level, you will receive errors when attempting to set any password which does not contain numbers, upper and lowercase letters, and special characters, or which is based on common dictionary words e.g PassWord.1.

```
There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary              file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1
```
Regardless of whether you chose to set up the VALIDATE PASSWORD PLUGIN, your server will next ask you to select and confirm a password for the MySQL root user. This is not to be confused with the system root. The database root user is an administrative user with full privileges over the database system. Even though the default authentication method for the MySQL root user dispenses the use of a password, even when one is set, you should define a strong password here as an additional safety measure. We’ll talk about this in a moment.

If you enabled password validation, you’ll be shown the password strength for the root password you just entered and your server will ask if you want to continue with that password. If you are happy with your current password, enter Y for “yes” at the prompt:
```
Estimated strength of the password: 100 
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
```

For the rest of the questions, press Y and hit the ENTER key at each prompt. This will prompt you to change the root password, remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MySQL immediately respects the changes you have made.

When you’re finished, test if you’re able to log in to the MySQL console by typing:
```
$ sudo mysql -p
```
Notice the -p flag in this command, which will prompt you for the password used after changing the root user password.

To exit the MySQL console, type:
```
mysql> exit
```
Notice that you need to provide a password to connect as the root user.

For increased security, it’s best to have dedicated user accounts with less expansive privileges set up for every database, especially if you plan on having multiple databases hosted on your server.

Note: At the time of this writing, the native MySQL PHP library mysqlnd doesn’t support caching_sha2_authentication, the default authentication method for MySQL 8. For that reason, when creating database users for PHP applications on MySQL 8, you’ll need to make sure they’re configured to use mysql_native_password instead.

Your MySQL server is now installed and secured. Next, we will install PHP, the final component in the LAMP stack.

### STEP 3 — INSTALLING PHP
You have Apache installed to serve your content and MySQL installed to store and manage your data. PHP is the component of our setup that will process code to display dynamic content to the end user. In addition to the php package, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. You’ll also need libapache2-mod-php to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies.

To install these 3 packages at once, run:
```
sudo apt install php libapache2-mod-php php-mysql
```
Once the installation is finished, you can run the following command to confirm your PHP version:

```
php -v

PHP 7.4.3 (cli) (built: Oct  6 2020 15:47:56) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
```
At this point, your LAMP stack is completely installed and fully operational.

Linux (Ubuntu)
Apache HTTP Server
MySQL
PHP
To test your setup with a PHP script, it’s best to set up a proper Apache Virtual Host to hold your website’s files and folders. Virtual host allows you to have multiple websites located on a single machine and users of the websites will not even notice it.

### STEP 4 — CREATING A VIRTUAL HOST FOR YOUR WEBSITE USING APACHE

In this project, you will set up a domain called projectlamp, but you can replace this with any domain of your choice.

Apache on Ubuntu 20.04 has one server block enabled by default that is configured to serve documents from the /var/www/html directory.
We will leave this configuration as is and will add our own directory next next to the default one.

Create the directory for projectlamp using ‘mkdir’ command as follows:
```
sudo mkdir /var/www/projectlamp
```
Next, assign ownership of the directory with your current system user:
```
 sudo chown -R $USER:$USER /var/www/projectlamp
```
Then, create and open a new configuration file in Apache’s sites-available directory using your preferred command-line editor. Here, we’ll be using vi or vim (They are the same by the way):
```
sudo vi /etc/apache2/sites-available/projectlamp.conf
```
This will create a new blank file. Paste in the following bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and paste the text:

```
<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
To save and close the file, simply follow the steps below:

Hit the esc button on the keyboard
Type :
Type wq. w for write and q for quit
Hit ENTER to save the file
You can use the ls command to show the new file in the sites-available directory

```
sudo ls /etc/apache2/sites-available
```
You will see something like this;

```
000-default.conf  default-ssl.conf  projectlamp.conf
```
With this VirtualHost configuration, we’re telling Apache to serve projectlamp using /var/www/projectlampl as its web root directory. If you would like to test Apache without a domain name, you can remove or comment out the options ServerName and ServerAlias by adding a # character in the beginning of each option’s lines. Adding the # character there will tell the program to skip processing the instructions on those lines.

You can now use a2ensite command to enable the new virtual host:

```
sudo a2ensite projectlamp
```
You might want to disable the default website that comes installed with Apache. This is required if you’re not using a custom domain name, because in this case Apache’s default configuration would overwrite your virtual host. To disable Apache’s default website use a2dissite command , type:
```
sudo a2dissite 000-default
```

To make sure your configuration file doesn’t contain syntax errors, run:
```
sudo apache2ctl configtest
```
Finally, reload Apache so these changes take effect:

```
sudo systemctl reload apache2
```
Your new website is now active, but the web root /var/www/projectlamp is still empty. Create an index.html file in that location so that we can test that the virtual host works as expected:
```
sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html
```
Now go to your browser and try to open your website URL using IP address:
```
http://<Public-IP-Address>:80
```
If you see the text from ‘echo’ command you wrote to index.html file, then it means your Apache virtual host is working as expected.
In the output you will see your server’s public hostname (DNS name) and public IP address. You can also access your website in your browser by public DNS name, not only by IP – try it out, the result must be the same (port is optional)

```
http://<Public-DNS-Name>:80
```
You can leave this file in place as a temporary landing page for your application until you set up an index.php file to replace it. Once you do that, remember to remove or rename the index.html file from your document root, as it would take precedence over an index.php file by default.

### STEP 5 — ENABLE PHP ON THE WEBSITE
With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. This is useful for setting up maintenance pages in PHP applications, by creating a temporary index.html file containing an informative message to visitors. Because this page will take precedence over the index.php page, it will then become the landing page for the application. Once maintenance is over, the index.html is renamed or removed from the document root, bringing back the regular application page.

In case you want to change this behavior, you’ll need to edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive:
```
sudo vim /etc/apache2/mods-enabled/dir.conf
```
display
```
<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```

After saving and closing the file, you will need to reload Apache so the changes take effect:
```
sudo systemctl reload apache2
```
Finally, we will create a PHP script to test that PHP is correctly installed and configured on your server.

Now that you have a custom location to host your website’s files and folders, we’ll create a PHP test script to confirm that Apache is able to handle and process requests for PHP files.

Create a new file named index.php inside your custom web root folder:
```
vim /var/www/projectlamp/index.php
```
This will open a blank file. Add the following text, which is valid PHP code, inside the file:
```
<?php
phpinfo();
```
When you are finished, save and close the file, refresh the page 

This page provides information about your server from the perspective of PHP. It is useful for debugging and to ensure that your settings are being applied correctly.

If you can see this page in your browser, then your PHP installation is working as expected.

After checking the relevant information about your PHP server through that page, it’s best to remove the file you created as it contains sensitive information about your PHP environment -and your Ubuntu server. You can use rm to do so:
```
sudo rm /var/www/projectlamp/index.php
```
You can always recreate this page if you need to access the information again later.


Congratulations! You have finished your very first REAL LIFE PROJECT by deploying a LAMP stack website in AWS Cloud!



After the installation of APACHE, MYSQL AND PHP, i have the following proof for review:
CREATING A VIRTUAL HOST FOR YOUR WEBSITE USING APACHE

![Screenshot from 2022-08-28 13-48-39](https://user-images.githubusercontent.com/110517150/188636878-6dc53d63-9726-4a4e-b928-6e11cbaee5e5.png)


![Screenshot from 2022-08-28 13-49-00](https://user-images.githubusercontent.com/110517150/188636886-882a8eaa-2485-4392-9d7f-d2d690507b67.png)


![Screenshot from 2022-08-28 13-48-26](https://user-images.githubusercontent.com/110517150/188636864-59424584-ea5b-4b5c-b926-d95a92f71ec1.png)


final result 
![Screenshot from 2022-08-28 13-46-49](https://user-images.githubusercontent.com/110517150/188637334-c848fd12-2c48-47a3-a71f-dea6c9bdbf98.png)
![Screenshot from 2022-08-28 13-47-06](https://user-images.githubusercontent.com/110517150/188637340-c36ad949-8986-4d9c-aa58-1c3fa19e1429.png)
![Screenshot from 2022-08-28 13-49-21](https://user-images.githubusercontent.com/110517150/188637343-87f93702-7b75-4279-8bc9-c2873c26547c.png)
![Screenshot from 2022-08-28 13-49-54](https://user-images.githubusercontent.com/110517150/188637348-531f2b5e-06f9-45c3-b4ef-d1ff9ad6b90e.png)
![Screenshot from 2022-08-28 13-50-11](https://user-images.githubusercontent.com/110517150/188637351-a75804e2-69c1-4db5-811e-2ea80627a717.png)
![Screenshot from 2022-08-28 13-50-44](https://user-images.githubusercontent.com/110517150/188637354-accfa077-2e8e-4ecf-b174-b6a37a9da478.png)
![Screenshot from 2022-08-28 13-50-56](https://user-images.githubusercontent.com/110517150/188637357-9791e835-e13e-4c87-a76c-5787a6aeca3d.png)
![Screenshot from 2022-08-28 13-51-13](https://user-images.githubusercontent.com/110517150/188637362-b33dc60d-69fd-4c3e-af45-0ad9bacc87f8.png)
![Screenshot from 2022-08-28 13-51-28](https://user-images.githubusercontent.com/110517150/188637368-b7ebfeb4-e200-4201-a4d9-f56e9d268465.png)
![Screenshot from 2022-08-28 13-51-43](https://user-images.githubusercontent.com/110517150/188637372-666c6b6c-5872-46c4-8b98-50429f8a892d.png)
![Screenshot from 2022-08-28 13-51-57](https://user-images.githubusercontent.com/110517150/188637373-f017d7c9-27bb-424f-8aa4-720078a9b931.png)
![Screenshot from 2022-08-28 13-52-30](https://user-images.githubusercontent.com/110517150/188637378-1c346151-b696-4c5b-a97b-2869f16469a6.png)
![Screenshot from 2022-08-28 13-52-48](https://user-images.githubusercontent.com/110517150/188637380-71f5a5ee-7bf0-4a07-a8f5-6c07228f70bf.png)
![Screenshot from 2022-08-28 13-53-16](https://user-images.githubusercontent.com/110517150/188637385-a9239f04-1a7a-4789-85c3-8c4683cc0244.png)





Certainly! Here’s an example of a project involving the LAMP stack:

---

### **LAMP Stack Web Application Deployment**

**Project Title:** **E-commerce Platform Deployment Using LAMP Stack**

**Overview:**
- **Objective:** Deploy a scalable e-commerce platform using the LAMP stack (Linux, Apache, MySQL, PHP).

**Key Tasks:**
1. **Server Setup:**
   - Provisioned and configured Linux servers on AWS EC2.
   - Installed and configured Apache as the web server.

2. **Database Configuration:**
   - Set up MySQL for managing the e-commerce database.
   - Optimized database performance and ensured secure remote connections.

3. **Application Deployment:**
   - Deployed the e-commerce platform built on PHP.
   - Connected the application to the MySQL database and configured the application settings.

4. **Security and Performance:**
   - Implemented SSL certificates for secure HTTPS connections.
   - Configured firewall rules, SELinux policies, and performed load testing to ensure performance.

**Outcome:**
- Successfully deployed a fully functional, secure, and scalable e-commerce platform accessible to users globally.

---

This project example provides a clear idea of how a LAMP stack can be utilized in a real-world IT project.

# USING CHATGPT TO PRACTICE

Sure! Here's a simplified version of the LAMP stack deployment script, tailored for beginners:

Beginner's Guide to LAMP Stack WordPress Deployment on AWS
## 1. Setting Up Your EC2 Linux Server

### Step 1: Launch Your EC2 Instance

Go to AWS Console:
Navigate to EC2 and click “Launch Instance.”
Choose an Amazon Linux 2 AMI and select the “t2.micro” instance type.
Follow the prompts, create a new key pair (or use an existing one), and launch your instance.

![Screenshot from 2024-08-08 05-34-16](https://github.com/user-attachments/assets/61364e19-d763-4b34-ab6c-68dcf90df77e)

### Step 2: Connect to Your EC2 Instance


```
ssh -i "YourKeyPair.pem" ec2-user@your-ec2-public-ip
```
Description: Use SSH to connect to your Linux server from your terminal.
![Screenshot from 2024-08-08 05-35-55](https://github.com/user-attachments/assets/512bca6e-f9a9-48b3-9531-f151449d3eb1)

## 2. Installing Apache (Web Server)

### Step 3: Install Apache

Command:
```
sudo apt update
sudo apt install apache2 -y

```

Description: Install Apache, which will serve your website.

### Step 4: Start Apache

Command:
```
sudo systemctl start apache2
sudo systemctl enable apache2

```
Description: Start Apache and make sure it starts automatically when the server reboots.

![Screenshot from 2024-08-08 05-38-14](https://github.com/user-attachments/assets/1fa0556e-f658-436d-a085-be4afa2cdd07)

## 3. Installing MySQL (Database Server)

### Step 5: Install MySQL

Command:
```
sudo apt install mysql-server -y

```
Description: Install MySQL to manage your website's data.

### Step 6: Start MySQL and Secure It

Command:
```
sudo mysql_secure_installation

```
Description: Start MySQL and follow the prompts to set a root password and remove insecure defaults.
![Screenshot from 2024-08-08 05-41-12](https://github.com/user-attachments/assets/a1b3b945-f77b-43db-8eaf-5c1f2e9efdf0)

### Step 7: Create a Database for WordPress

MySQL Command:
```
sudo mysql -u root -p
CREATE DATABASE wordpress_db;
CREATE USER 'wp_user'@'localhost' IDENTIFIED BY 'Password123#@!';
GRANT ALL PRIVILEGES ON wordpress_db.* TO 'wp_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;

```
Description: Create a database and a user for WordPress.


i encountered problem at the creating of user

![Screenshot from 2024-08-08 05-47-25](https://github.com/user-attachments/assets/4eec6b61-3fea-4d03-bce2-1a75e641159c)
![Screenshot from 2024-08-08 05-47-29](https://github.com/user-attachments/assets/7bb5a446-5d9a-4933-811a-36add9304100)
![Screenshot from 2024-08-08 05-47-33](https://github.com/user-attachments/assets/39963610-1157-4f3b-a500-3a92c7030dae)
result 
![Screenshot from 2024-08-08 05-49-22](https://github.com/user-attachments/assets/408c63cb-ed22-4046-b06d-f34c49fbe0fc)

## 4. Installing PHP (Programming Language)

### Step 8: Install PHP
Command:
```
sudo apt install php libapache2-mod-php php-mysql -y

```
Description: Install PHP, the programming language WordPress uses.

### Step 9: Restart Apache to Apply Changes
Command:
```
sudo systemctl restart apache2
```
Description: Restart Apache so it recognizes PHP.
![Screenshot from 2024-08-08 05-51-09](https://github.com/user-attachments/assets/01123f26-ffa8-4447-8509-d300ac61508d)

## 5. Installing WordPress

### Step 10: Download and Set Up WordPress

Command:
```
cd /tmp
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
sudo mv wordpress/* /var/www/html/

```
Description: Download and move WordPress files to your web server.
![Screenshot from 2024-08-08 05-52-04](https://github.com/user-attachments/assets/5820c50a-9882-49bb-ba73-9e88e70e2f03)

### Step 11: Configure WordPress
Command:
```
sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
sudo nano /var/www/html/wp-config.php

```
Description: Edit the WordPress configuration file to connect to your MySQL database. Replace these lines with your database info:
Command:
```
define('DB_NAME', 'wordpress_db');
define('DB_USER', 'wp_user');
define('DB_PASSWORD', 'Password123#@!');
define('DB_HOST', 'localhost');

```
![Screenshot from 2024-08-08 05-55-27](https://github.com/user-attachments/assets/f517cecd-04b5-4b77-8ebd-389f9d31fe86)

### Step 12: Set Permissions
Command:
```
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/

```
Description: Ensure the web server can read and write to the WordPress files.

## 6. Accessing Your WordPress Site

### Step 13: Complete WordPress Setup

Browser:
Visit http://your-ec2-public-ip in your web browser.
Follow the on-screen instructions to complete the WordPress setup.

## 7. (Optional) Securing Your Site with SSL

### Step 14: Install SSL (Self-Signed)

Command:
```
sudo apt install openssl
sudo openssl req -newkey rsa:2048 -nodes -keyout /etc/ssl/private/apache-selfsigned.key -x509 -days 365 -out /etc/ssl/certs/apache-selfsigned.crt

```
Description: Install and configure a self-signed SSL certificate to secure your website.

### Step 15: Step 15: Configure Apache for SSL

Command:
```
sudo nano /etc/apache2/sites-available/default-ssl.conf

```
![Screenshot from 2024-08-08 06-08-59](https://github.com/user-attachments/assets/7d230b37-51b4-4903-8d8b-e7b2de376e0d)

Description: Edit the SSL configuration file to use your SSL certificate. Ensure the SSLCertificateFile and SSLCertificateKeyFile directives point to your certificate and key paths.

### Step 16: Enable SSL and Restart Apache

Command:
```
sudo a2enmod ssl
sudo a2ensite default-ssl.conf
sudo systemctl restart apache2
```
Description: Enable SSL in Apache and restart it to apply the settings.
![Screenshot from 2024-08-08 06-10-53](https://github.com/user-attachments/assets/1617ba59-013e-4514-8665-ea97704f0c45)
![Screenshot from 2024-08-08 06-11-47](https://github.com/user-attachments/assets/597e2e9b-ee67-4d84-85e2-ca73f22ac120)
