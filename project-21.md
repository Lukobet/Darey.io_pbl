
# ORCHESTRATING CONTAINERS ACROSS MULTIPLE VIRTUAL SERVERS WITH KUBERNETES. PART 1
Containers are the most lightweight and easily transferrable workloads, they start faster than VMs, consume less space and memory and, they perfectly fit to accommodate microservice architecture. It means that number of containers will be usually significantly higher that number of VMs and with number comes complexity of managing containers.

To manage a fleet of containers you can use different software solutions, but the most widely used one is Kubernetes (also referred as K8s). It was originally developed by Google and now being maintained by Cloud Native Computing Foundation (CNCF).
**Why migrate from Docker Compose to K8s?**
In the previous project you successfully deployed your Docker containers using Docker Compose, it is a great tool that helps avoiding execution of multiple CLI commands by preparing a declarative configuration file. It is handy when you deploy one or a few containers, but in most cases, it does not fit for production deployments.

Because of the many limitations that Docker Compose has, it is very important for us to consider migrating our solution to more an advanced technology. The most common alternatives to Compose, amongst a few others, are Docker Swarm and Kubernetes.

**What is wrong with Docker Compose?**
It is important to understand that, DevOps is about "Culture" and NOT "Tools" Therefore, it is not correct to say that one tool is better than another; different organizations have different needs and a good tool for one team may be bad for another just because their needs are not the same. In some teams, Docker Compose fit their needs perfectly, despite the perceived limitations. The major limitation of Docker Compose is that it can only be used to run workloads on a single computer host. Now, that is an obvious limitation because if our Tooling Application and its MySQL Database are all running on a single VM, like we did in Project 20, then this host is considered as a SPOF (i.e. – Single Point Of Failure).

So, could we say there is something wrong with Docker Compose? Not exactly, as a matter of fact, it is being used a lot in the industry. It fits well into some use cases that require speedy development and Proof of Concepts. As you will soon see, Kubernetes is a lot more complex technology, and it may be an overkill for some use cases.

**Container Orchestration with Kubernetes
What is Container Orchestration?**
Two important things to remember about Docker containers are:

Unlike virtual machines, they are not designed to run for a very long time. By design, Docker containers are ephemeral. By “ephemeral” it means that a container can be stopped and destroyed, and a new one can be built from the same Docker image and put in place with an absolute minimum set-up and configuration requirement.

To ensure that container workloads are highly scalable, they must be configured to run across multiple compute nodes. A compute node can be a physical server or a virtual machine with Docker engine installed to run your containers.

If we had two compute nodes to run our containers, let us consider a following scenarios:

Given the two points mentioned above, if containers are configured to run across 2 computer nodes, and a particular container, running on Node 1 dies, how will it know that it can spin up again on Node 2?
Let us imagine that Tooling website container is running on Node 1, and MySQL container is running on Node 2, how will both containers be able to communicate with each other? Remember in Project 20, we had to create a custom network on the same host and ensure that they can communicate through that network. But in the case of 2 separate hosts, this is natively not possible.
Container orchestration is a concept that allows to address these two scenarios, it provides automation of all the aspects of coordinating and managing containers. Container orchestration is focused on managing life cycle of containers and their dynamic environments.

It is about automating the entire lifecycle of containers running across multiple nodes:

* Configuring and scheduling of containers on nodes
* Ensuring the availability of containers, even when they die
* Scaling of containers to equally balance application workloads across infrastructure
* Allocation of resources between containers
* Load balancing, traffic routing and service discovery of containers
* Health monitoring of containers
* Securing the interactions between containers.

Kubernetes is a tool designed to do Container Orchestration and it does its job very well when correctly configured.

As mentioned earlier, there are other alternatives to Docker Compose. But, throughout the entire PBL program, we will not focus on Docker Swarm. We will rather spend more time with Kubernetes. Part of the reason for this is because Kubernetes has more functionalities and is widely in use in the industry.

To know when to choose between Docker Swarm and Kubernetes,"https://dzone.com/articles/quotdocker-swarm-or-kubernetesquot-is-it-the-right" Here is an interesting article to read with some very enlightening stats.

## KUBERNETES ARCHITECTURE

Kubernetes is a not a single package application that you can install with one command, it is comprised of several components, some of them can be deployed as services, some can be also deployed as separate containers.

the components can be found here " https://kubernetes.io/docs/concepts/overview/components/ "

#### Kubernetes From-Ground-Up"
**K8s installation options**
So far, Kubernetes sounds like a lot of fun, right? With its intuitive architecture, and rich configuration options, you may already want to jump right in, spin up a few VMs and begin to install and configure a Kubernetes cluster. But hold on for a second. Installing and configuring Kubernetes is far from being a walk in the park, i.e., it is very difficult to implement and get it ready for production. Especially, if you want to setup a highly available, and secure Kubernetes cluster.

The good news is, there are open-source tools available today that already has all the hard work done and you can plug into them easily. An example of that is **minikube**, which can be used during testing and development.

For a better understanding of each aspect of spinning up a Kubernetes cluster, we will do it without any automated helpers. You will install each and every component manually from scratch and learn how to make them work together – we call this approach "K8s From-Ground-Up".

To successfully implement "K8s From-Ground-Up", the following and even more will be done by you as a K8s administrator:

* Install and configure master (also known as control plane) components and worker nodes (or just nodes).
* Apply security settings across the entire cluster (i.e., encrypting the data in transit, and at rest)
***** In transit encryption means encrypting communications over the network using HTTPS
***** At rest encryption means encrypting the data stored on a disk
* Plan the capacity for the backend data store etcd
* Configure network plugins for the containers to communicate
* Manage periodical upgrade of the cluster
* Configure observability and auditing

**Note:** Unless you have any business or compliance restrictions, ALWAYS consider to use managed versions of K8s – Platform as a Service offerings, such as Azure Kubernetes Service (AKS), Amazon Elastic Kubernetes Service (Amazon EKS), or Google Kubernetes Engine (GKE) as they usually have better default security settings, and the costs for maintaining the control plane are very low.

You will be able to appreciate automation tools and managed versions of Kubernetes much more after you have experienced all the lessons from the struggles and failures from the "K8s From-Ground-Up".
Let us begin building out Kubernetes cluster from the ground
DISCLAIMER: The following setup of Kubernetes should be used for learning purpose only, and not to be considered for production. This is because setting up a K8s cluster for production use has a lot more moving parts, especially when it comes to planning the nodes, and securing the cluster. The purpose of "K8s From-Ground-Up" is to get you much closer to the different components as shown in the architecture diagram and relate with what you have been learning about Kubernetes.

Tools to be used and expected result of the Project 20
* VM: AWS EC2
* OS: Ubuntu 20.04 lts+
* Docker Engine
* kubectl console utility
* cfssl and cfssljson utilities
* Kubernetes cluster
You will create 3 EC2 Instances, and in the end, we will have the following parts of the cluster properly configured:

* One Kubernetes Master
* Two Kubernetes Worker Nodes
![Screenshot from 2023-09-25 16-50-47](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/9af4751a-e856-42c1-9b03-4f17ea1c5a4f)

* Configured SSL/TLS certificates for Kubernetes components to communicate securely
* Configured Node Network
* Configured Pod Network
##### TASK 0: INSTALL CLIENT TOOLS BEFORE BOOTSTRAPPING THE CLUSTER.

First, you will need some client tools installed and configurations made on your client workstation:

* awscli – is a unified tool to manage your AWS services
Generate access keys and store them in a safe place.
  Install and configure AWS CLI
  ![Screenshot from 2023-09-25 17-09-42](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/45baf144-fa75-4433-97c4-8cc5020e45bc)
Test your AWS CLI by running:
```
aws ec2 describe-vpcs
```
![Screenshot from 2023-09-25 17-15-27](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/cd92d2f6-c07b-4c18-9f40-1e67b21d2038)
* kubectl – this command line utility will be your main control tool to manage your K8s cluster. You will use this tool so many times, so you will be able to type ‘kubetcl’ on your keyboard with a speed of light. You can always make a shortcut (alias) to just one character ‘k’. Also, add this extremely useful official kubectl

  Kubernetes cluster has a Web API that can receive HTTP/HTTPS requests, but it is quite cumbersome to curl an API each and every time you need to send some command, so kubectl command tool was developed to ease a K8s administrator’s life.

With this tool you can easily interact with Kubernetes to deploy applications, inspect and manage cluster resources, view logs and perform many more administrative operations.

###### Install Kbectl
**Download the binary**
```
wget https://storage.googleapis.com/kubernetes-release/release/v1.21.0/bin/linux/amd64/kubectl
```
**make it executable**
```
chmod +x kubectl
```
**move the bin directory**
```
sudo mv kubectl /usr/local/bin/
```
**Verify that kubectl version 1.21.0 or higher is installed:**
```
kubectl version --client
```
![Screenshot from 2023-09-25 17-18-51](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/b6ca8b54-549e-4a77-849f-13fd7df376e9)


* cheat Sheet to your bookmarks, it has examples of the most used ‘kubectl’ commands.

  ##### Install CFSSL and CFSSLJSON
* cfssl – an open source toolkit for everything TLS/SSL from Cloudflare
**cfssl** is an open source tool by Cloudflare used to setup a Public Key Infrastructure (PKI Infrastructure) for generating, signing and bundling TLS certificates. In previous projects you have experienced the use of Letsencrypt for the similar use case. Here, cfssl will be configured as a Certificate Authority which will issue the certificates required to spin up a Kubernetes cluster.


###### Install CFSSL
**Download the binary**
```
wget -q --show-progress --https-only --timestamping \
  https://storage.googleapis.com/kubernetes-the-hard-way/cfssl/1.4.1/linux/cfssl \
  https://storage.googleapis.com/kubernetes-the-hard-way/cfssl/1.4.1/linux/cfssljson
```
**make it executable**

```
chmod +x cfssl cfssljson
```
**Move to the Bin directory**
```
sudo mv cfssl cfssljson /usr/local/bin/
```
this above didnt work out, so i tried the steps bellow
```
VERSION=$(curl --silent "https://api.github.com/repos/cloudflare/cfssl/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')
VNUMBER=${VERSION#"v"}
wget https://github.com/cloudflare/cfssl/releases/download/${VERSION}/cfssl_${VNUMBER}_linux_amd64 -O cfssl

```
**make it executable**

```
sudo mv cfssl /usr/local/bin
```
**Move to the Bin directory**
```
sudo mv cfssl /usr/local/bin
```
![Screenshot from 2023-09-25 17-50-03](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/2870c564-9c46-4b52-892b-5f062b455e4f)


###### Install CFSSL

```
VERSION=$(curl --silent "https://api.github.com/repos/cloudflare/cfssl/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')
VNUMBER=${VERSION#"v"}
wget https://github.com/cloudflare/cfssl/releases/download/${VERSION}/cfssljson_${VNUMBER}_linux_amd64 -O cfssljson
```
**make it executable**

```
chmod +x cfssljson
```

**Move to the Bin directory**

```
sudo mv cfssljson /usr/local/bin
```

**verify the version**

```
cfssljson -version
```

![Screenshot from 2023-09-25 17-56-12](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/95c5acc9-308d-423f-89f7-e88154d0fecf)

### AWS CLOUD RESOURCES FOR KUBERNETES CLUSTER

##### Step 1 – Configure Network Infrastructure
Virtual Private Cloud – VPC

Create a directory named **k8s-cluster-from-ground-up**
* Create a VPC and store the ID as a variable:
```
VPC_ID=$(aws ec2 create-vpc \
--cidr-block 172.31.0.0/16 \
--output text --query 'Vpc.VpcId'
)
```
* Tag the VPC so that it is named:

```
NAME=k8s-cluster-from-ground-up

aws ec2 create-tags \
  --resources ${VPC_ID} \
  --tags Key=Name,Value=${NAME}
```
**Domain Name System – DNS**

* Enable DNS support for your VPC:

```
aws ec2 modify-vpc-attribute \
--vpc-id ${VPC_ID} \
--enable-dns-support '{"Value": true}'
```
* Enable DNS support for hostnames:

```
aws ec2 modify-vpc-attribute \
--vpc-id ${VPC_ID} \
--enable-dns-hostnames '{"Value": true}'
```
**AWS Region**
* Set the required region
```
AWS_REGION=us-east-1
```


##### TASK 2: INSTALL CLIENT TOOLS BEFORE BOOTSTRAPPING THE CLUSTER.
##### TASK 3: INSTALL CLIENT TOOLS BEFORE BOOTSTRAPPING THE CLUSTER.
