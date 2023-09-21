## Migration to the Сloud with containerization. Part 1 - Docker & Docker Compose

Until now, you have been using VMs (AWS EC2) in Amazon Virtual Private Cloud (AWS VPC) to deploy your web solutions, and it works well in many cases. You have learned how easy to spin up and configure a new EC2 manually or with such tools as Terraform and Ansible to automate provisioning and configuration. You have also deployed two different websites on the same VM; this approach is scalable, but to some extent; imagine what if you need to deploy many small applications (it can be web front-end, web-backend, processing jobs, monitoring, logging solutions, etc.) and some of the applications will require various OS and runtimes of different versions and conflicting dependencies – in such case you would need to spin up servers for each group of applications with the exact OS/runtime/dependencies requirements. When it scales out to tens/hundreds and even thousands of applications (e.g., when we talk of microservice architecture), this approach becomes very tedious and challenging to maintain.

In this project, we will learn how to solve this problem and practice the technology that revolutionized application distribution and deployment back in 2013! We are talking of Containers and imply Docker. Even though there are other application containerization technologies, Docker is the standard and the default choice for shipping your app in a container!


#### Task 1: Install Docker and prepare for migration to the Cloud

First, we need to install Docker Engine
Docker Engine is an open source containerization technology for building and containerizing your applications. Docker Engine acts as a client-server application with:

* A server with a long-running daemon process dockerd.
* APIs that specify interfaces that programs can use to talk to and instruct the Docker daemon.
* A command-line interface (CLI) client docker.
The CLI uses Docker APIs to control or interact with the Docker daemon through scripting or direct CLI commands. Many other Docker applications use the underlying API and CLI. The daemon creates and manage Docker objects, such as images, containers, networks, and volumes.

I installed docker using the apt repository found here 
```
https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository
```

![Screenshot from 2023-09-16 15-17-36](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/6b79df1f-a077-4723-ad27-9da50cbbec61)



![Screenshot from 2023-09-16 15-40-39](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/f5d10a81-1e16-4146-99ea-cc84ca6787cd)


#### *  migrate the Tooling Web Application from a VM-based solution into a containerized one.
*MySQL in container
**Step 1: Pull MySQL Docker Image from Docker Hub Registry**
Start by pulling the appropriate Docker image for MySQL. You can download a specific version or opt for the latest release, as seen in the following command:
```
docker pull mysql/mysql-server:latest
```
I relaised that i had to type sudo befroe every docker command for it to work
![Screenshot from 2023-09-17 12-32-24](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/a5677c36-6b6f-4e60-a279-c2636405058b)
![Screenshot from 2023-09-17 12-33-33](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/033916d2-465f-4aae-9a43-47480782448a)
**Step 2: Deploy the MySQL Container to your Docker Engine**
1. Once you have the image, move on to deploying a new MySQL container with:

```
 sudo docker run --name tosin -e MYSQL_ROOT_PASSWORD=password -d mysql/mysql-server:latest
```

* Replace <container_name> with the name of your choice. If you do not provide a name, Docker will generate a random one
* The -d option instructs Docker to run the container as a service in the background
* Replace <my-secret-pw> with your chosen password
* In the command above, we used the latest version tag. This tag may differ according to the image you downloaded
  
![Screenshot from 2023-09-17 12-35-31](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/bf1f21b3-dd4d-4490-bcea-a8d75b8c58aa)
2.  Then, check to see if the MySQL container is running: Assuming the container name specified is mysql-server
```
docker ps -a
```

![Screenshot from 2023-09-17 12-53-30](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/af89dc00-818d-4cfa-8b42-aaf90e02d4ab)


#### CONNECTING TO THE MYSQL DOCKER CONTAINER


**Step 3: Connecting to the MySQL Docker Container**

We can either connect directly to the container running the MySQL server or use a second container as a MySQL client. Let us see what the first option looks like.

**Approach 1**

Connecting directly to the container running the MySQL server:

```
$ docker exec -it mysql bash

or

$ docker exec -it mysql mysql -uroot -p
```
![Screenshot from 2023-09-17 13-03-01](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/c9be81ce-1513-4ebb-9d56-b151dbda365a)
![Screenshot from 2023-09-17 13-06-36](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/c77d0fb9-89a7-4c80-ba46-93f5854d33af)
Flags used
```
exec used to execute a command from bash itself
-it makes the execution interactive and allocate a pseudo-TTY
bash this is a unix shell and its used as an entry-point to interact with our container
mysql The second mysql in the command “docker exec -it mysql mysql -uroot -p” serves as the entry point to interact with mysql container just like bash or sh
-u mysql username
-p mysql password
```
**Approach 2**

At this stage you are now able to create a docker container but we will need to add a network. So, stop and remove the previous mysql docker container.
```
docker ps -a
docker stop mysql 
docker rm mysql or <container ID> 
```
verify that the container is deleted
```
docker ps -a
```

![Screenshot from 2023-09-17 13-10-00](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/cbc095fc-5e54-402f-96b5-dd099a6ae046)

First, create a network:
```
docker network create --subnet=172.18.0.0/24 tooling_app_network 
```
Creating a custom network is not necessary because even if we do not create a network, Docker will use the default network for all the containers you run. By default, the network we created above is of DRIVER Bridge. So, also, it is the default network. You can verify this by running the docker network ls command.

But there are use cases where this is necessary. For example, if there is a requirement to control the cidr range of the containers running the entire application stack. This will be an ideal situation to create a network and specify the --subnet


![Screenshot from 2023-09-17 13-12-16](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/181f6055-abe8-486b-93ca-dc131cf7663b)
let us create an environment variable to store the root password:
```
export MYSQL_PW= 
```
```
echo MYSQL_PW= 
```
![Screenshot from 2023-09-17 13-15-27](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/20230235-dbd8-4566-8be1-6985bde8d204)

Then, pull the image and run the container, all in one command like below:

```
docker run --network tooling_app_network -h mysqlserverhost --name=mysql-server -e MYSQL_ROOT_PASSWORD=$MYSQL_PW  -d mysql/mysql-server:latest
```
Flags used
```
-d runs the container in detached mode
--network connects a container to a network
-h specifies a hostname
```

![Screenshot from 2023-09-17 13-18-13](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/836ab889-df45-45e4-bfd9-ed01fd6e7687)

As you already know, it is best practice not to connect to the MySQL server remotely using the root user. Therefore, we will create an SQL script that will create a user we can use to connect remotely.

Create a file and name it create_user.sql and add the below code in the file:
```
CREATE USER ''@'%' IDENTIFIED BY ''; GRANT ALL PRIVILEGES ON * . * TO ''@'%'; 
```
![Screenshot from 2023-09-17 13-21-43](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/50816562-3ff9-4eeb-8feb-75f03a4868fe)

Run the script:
Ensure you are in the directory create_user.sql file is located or declare a path


```
docker exec -i mysql-server mysql -uroot -p$MYSQL_PW < create_user.sql 
```

Had issues here
![Screenshot from 2023-09-17 13-30-12](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/36aa3bff-f179-4d5e-b965-71ca46809624)
i was able to acces it using the root user password gotten from docker logs 
![Screenshot from 2023-09-17 15-55-47](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/fbfc5118-ec61-4c06-b423-64be227c87a6)

buts still the same. i joined the blocker session and my errorsbweere corrected . I didn't add the password i want to use in the script

```
export MYSQL_PW= 
```
now 
```
export MYSQL_PW=password
```
i deleted the container and run the container again and i gained access 
![Screenshot from 2023-09-18 19-47-21](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/88293c87-9c39-41b5-9d23-a83d3972d42e)

**Connecting to the MySQL server from a second container running the MySQL client utility**

The good thing about this approach is that you do not have to install any client tool on your laptop, and you do not need to connect directly to the container running the MySQL server.

* Run the MySQL Client Container:
```
 docker run --network tooling_app_network --name mysql-client -it --rm mysql mysql -h mysqlserverhost -u  -p 
```
Flags used
```
 --name gives the container a name
-it runs in interactive mode and Allocate a pseudo-TTY
--rm automatically removes the container when it exits
--network connects a container to a network
-h a MySQL flag specifying the MySQL server Container hostname
-u user created from the SQL script
admin username-for-user-created-from-the-SQL-script-create_user.sql
-p password specified for the user created from the SQL script
```
![Screenshot from 2023-09-18 19-52-41](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/de0ef0cd-7f48-4f6d-8ccd-d0ad7e7e2416)


**Prepare database schema**
Now you need to prepare a database schema so that the Tooling application can connect to it.

1. Clone the Tooling-app repository from
```
git clone https://github.com/darey-devops/tooling.git
```
2. On your terminal, export the location of the SQL file
```
 export tooling_db_schema=/tooling_db_schema.sql  
```

You can find the tooling_db_schema.sql in the tooling/html/tooling_db_schema.sql folder of cloned repo.

Verify that the path is exported

```
echo $tooling_db_schema 
```
3. Use the SQL script to create the database and prepare the schema. With the docker exec command, you can execute a command in a running container.
```
 docker exec -i mysql-server mysql -uroot -p$MYSQL_PW < $tooling_db_schema  
```
![Screenshot from 2023-09-18 19-57-20](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/8436903f-e55b-48f2-a239-1e0a0a6ee821)

I encountered a difficulty here, but had to change the path to tooling
adn run the command again
![Screenshot from 2023-09-18 20-08-51](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/d22d2ff5-715d-4110-9832-b40ab7fbf163)


4.Update the .env file with connection details to the database

The .env file is located in the html tooling/html/.env folder but not visible in terminal. you can use vi or nano
```
 sudo vi .env

MYSQL_IP=mysqlserverhost
MYSQL_USER=username
MYSQL_PASS=client-secrete-password
MYSQL_DBNAME=toolingdb
```
Flags used 
```
MYSQL_IP mysql ip address “leave as mysqlserverhost”
MYSQL_USER mysql username for user export as environment variable
MYSQL_PASS mysql password for the user exported as environment varaible
MYSQL_DBNAME mysql databse name “toolingdb”  
```
![Screenshot from 2023-09-18 20-15-21](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/917f0891-3690-451e-948d-77d681d85b24)

5. Run the Tooling App
Containerization of an application starts with creation of a file with a special name – ‘Dockerfile’ (without any extensions). This can be considered as a ‘recipe’ or ‘instruction’ that tells Docker how to pack your application into a container. In this project, you will build your container from a pre-created Dockerfile, but as a DevOps, you must also be able to write Dockerfiles.

Docker builds images automatically by reading the instructions from a Dockerfile which is a text file that contains all commands, in order, needed to build a given image.

sample of a Dockerfile
```
#syntax=docker/dockerfile:1

FROM ubuntu:22.04
COPY . /app
RUN make /app
CMD python /app/app.py 
```
here:
FROM creates a layer from the ubuntu:22.04 Docker image.
COPY adds files from your Docker client's current directory.
RUN builds your application with make.
CMD specifies what command to run within the container.


So, let us containerize our Tooling application; here is the plan:

* Make sure you have checked out your Tooling repo to your machine with Docker engine
* First, we need to build the Docker image the tooling app will use. The Tooling repo you cloned above has a Dockerfile for this purpose. Explore it and make sure you understand the code inside it.
* Run docker build command
```
 docker build -t tooling:0.0.1 .  
```

![Screenshot from 2023-09-18 20-43-17](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/b315c838-cc2b-473f-8f9a-527cbe2b4b36)

In the above command, we specify a parameter -t, so that the image can be tagged tooling"0.0.1 – Also, you have to notice the . at the end. This is important as that tells Docker to locate the Dockerfile in the current directory you are running the command. Otherwise, you would need to specify the absolute path to the Dockerfile.

* Launch the container with docker run
* Try to access your application via port exposed from a container


6. Run the container:
```
 docker run --network tooling_app_network -p 8085:80 -it tooling:0.0.1  
```

Let us observe those flags in the command.

We need to specify the --network flag so that both the Tooling app and the database can easily connect on the same virtual network we created earlier.
The -p flag is used to map the container port with the host port. Within the container, apache is the webserver running and, by default, it listens on port 80. You can confirm this with the CMD ["start-apache"] section of the Dockerfile. But we cannot directly use port 80 on our host machine because it is already in use. The workaround is to use another port that is not used by the host machine. In our case, port 8085 is free, so we can map that to port 80 running in the container.

I got this error 
![Screenshot from 2023-09-18 20-49-29](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/3e8f8ef4-1b50-4ae9-870a-15fb4959595e)

but tried it again in my homw directory and it was successful
![Screenshot from 2023-09-18 21-09-35](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/bf4fe6a1-f7c5-4e24-8c53-2b5c5dabf915)

Error gotten while trying to access it
![Screenshot from 2023-09-18 21-14-14](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/ff751fa5-e598-4893-a26c-2f87aa4a272d)
![Screenshot from 2023-09-18 21-42-17](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/871f04b9-f74e-4184-8794-60d7edaf2023)

### PRACTICE TASK
**Practice Task №1 – Implement a POC to migrate the PHP-Todo app into a containerized application.**
Download php-todo repository 

The project below will challenge you a little bit, but the experience there is very valuable for future projects.

Part 1
Write a Dockerfile for the TODO app
Run both database and app on your laptop Docker Engine
Access the application from the browser

![Screenshot from 2023-09-20 18-18-16](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/9771fbc1-8032-405c-88e2-0a1baec48f12)
![Screenshot from 2023-09-20 18-22-06](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/69646581-1ad5-4b31-b9bc-4b99b1bd7894)
![Screenshot from 2023-09-20 18-24-33](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/7e01c6fb-ba49-4c89-82c0-0abe757a7d43)
![Screenshot from 2023-09-20 21-55-30](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/f6a118ce-2b4b-489e-b585-9f6433f613b8)

now running the application using this code 
```
sudo docker run --network php-todo_app_network -p 8090:8000 -idt todo:0.0.1 
```

![Screenshot from 2023-09-20 22-12-41](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/ae7960cd-be1b-425e-b0a2-141495728402)

Part 2
Create an account in Docker Hub
Create a new Docker Hub repository
Push the docker images from your PC to the repository

![Screenshot from 2023-09-20 22-26-23](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/5b661014-e93b-467b-8389-a6046c15f403)
tagging done

![Screenshot from 2023-09-20 22-32-56](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/c67a5718-719f-4452-a2e3-d8e40cb147ae)
Push

![Screenshot from 2023-09-20 22-31-45](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/37bd1c11-56d5-4db1-ab5f-48c9d0e9c15a)

![Screenshot from 2023-09-20 22-35-28](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/05e66a01-9e27-49a3-9139-4144c170db05)

![Screenshot from 2023-09-21 16-08-09](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/19d244b1-b0e0-4bcc-b014-9b20c119a842)
Part 3
Write a Jenkinsfile that will simulate a Docker Build and a Docker Push to the registry
Connect your repo to Jenkins
Create a multi-branch pipeline
Simulate a CI pipeline from a feature and master branch using previously created Jenkinsfile
Ensure that the tagged images from your Jenkinsfile have a prefix that suggests which branch the image was pushed from. For example, feature-0.0.1.
Verify that the images pushed from the CI can be found at the registry.


#### Deployment with Docker Compose
All we have done until now required quite a lot of effort to create an image and launch an application inside it. We should not have to always run Docker commands on the terminal to get our applications up and running. There are solutions that make it easy to write declarative code in YAML, and get all the applications and dependencies up and running with minimal effort by launching a single command.

In this section, we will refactor the Tooling app POC so that we can leverage the power of Docker Compose.

First, install Docker Compose on your workstation 
Create a file, name it **tooling.yaml**
Begin to write the Docker Compose definitions with YAML syntax. The YAML file is used for defining services, networks, and volumes:
```
version: "3.9"
services:
  tooling_frontend:
    build: .
    ports:
      - "5000:80"
    volumes:
      - tooling_frontend:/var/www/html
    links:
      - db
  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: <The database name required by Tooling app >
      MYSQL_USER: <The user required by Tooling app >
      MYSQL_PASSWORD: <The password required by Tooling app >
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    volumes:
      - db:/var/lib/mysql
volumes:
  tooling_frontend:
  db:
```
Run the command to start the containers

```
sudo docker-compose -f tooling.yaml  up -d  
```
**Practice Task №2 – Complete Continous Integration With A Test Stage**

Document your understanding of all the fields specified in the Docker Compose file tooling.yaml
Update your Jenkinsfile with a test stage before pushing the image to the registry.
What you will be testing here is to ensure that the tooling site http endpoint is able to return status code 200. Any other code will be determined a stage failure.
Implement a similar pipeline for the PHP-todo app.
Ensure that both pipelines have a clean-up stage where all the images are deleted on the Jenkins server.

