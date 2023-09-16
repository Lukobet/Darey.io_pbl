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
**Step 2: Deploy the MySQL Container to your Docker Engine**
* Once you have the image, move on to deploying a new MySQL container with:

```
docker run --name <container_name> -e MYSQL_ROOT_PASSWORD=<my-secret-pw> -d mysql/mysql-server:latest
```
* Replace <container_name> with the name of your choice. If you do not provide a name, Docker will generate a random one
* The -d option instructs Docker to run the container as a service in the background
* Replace <my-secret-pw> with your chosen password
* In the command above, we used the latest version tag. This tag may differ according to the image you downloaded


