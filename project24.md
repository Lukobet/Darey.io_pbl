# BUILDING ELASTIC KUBERNETES SERVICE (EKS) WITH TERRAFORM
Since project 21 and 22, you have had some fragmented experience around kubernetes bootstraping and deployment of containerised applications. This project seeks to solidify your skills by focusing more on real world set up.

You will use Terraform to create a Kubernetes EKS cluster and dynamically add scalable worker nodes
You will deploy multiple applications using HELM
You will experience more kubernetes objects and how to use them with Helm. Such as Dynamic provisioning of volumes to make pods stateful
You will improve upon your CI/CD skills with Jenkins

In Project 21, you created a k8s cluster from Ground-Up. That was quite painful, but very necessary to help you master kubernetes. Going forward, you will not have to do that. Even in the real world, you will hardly ever have to do that. given that cloud providers such as AWS have managed services for kubernetes, they have done all the hard work, and with a few API calls to AWS, you can have a production grade cluster ready to go in minutes. Therefore, in this project, you begin by focusing on EKS, and how to get it up and running using Terraform. Before moving on to other things.

**Building EKS with Terraform**
At this point you already have some Terraform experience. So, you have some work to do. But, you can get started with the steps below. If you have terraform code from Project 16, simply update the code and include EKS starting from number 6 below. Otherwise, follow the steps from number 1

Note: Use Terraform version v1.0.2 and kubectl version v1.23.6

Open up a new directory on your laptop, and name it **EKS-Terraform**
Use AWS CLI to create an S3 bucket
* how to create aws s3 bucket through the CLI
  aws s3api create-bucket --bucket my-kOps-bucket --region us-east-2 --create-bucket-configuration LocationConstraint=us-east-2
1. Create a file – backend.tf Task for you, ensure the backend is configured for remote state in S3
```
terraform {
}
```
![Screenshot from 2023-10-14 11-32-27](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/015c74e4-2c63-49b2-b41b-5dd9fd16bc15)

2. Create a file – network.tf and provision Elastic IP for Nat Gateway, VPC, Private and public subnets.
```
# reserve Elastic IP to be used in our NAT gateway
resource "aws_eip" "nat_gw_elastic_ip" {
vpc = true

tags = {
Name            = "${var.cluster_name}-nat-eip"
iac_environment = var.iac_environment_tag
}
}
```

3. Create VPC using the official AWS module
```
module "vpc" {
source  = "terraform-aws-modules/vpc/aws"

name = "${var.name_prefix}-vpc"
cidr = var.main_network_block
azs  = data.aws_availability_zones.available_azs.names

private_subnets = [
# this loop will create a one-line list as ["10.0.0.0/20", "10.0.16.0/20", "10.0.32.0/20", ...]
# with a length depending on how many Zones are available
for zone_id in data.aws_availability_zones.available_azs.zone_ids :
cidrsubnet(var.main_network_block, var.subnet_prefix_extension, tonumber(substr(zone_id, length(zone_id) - 1, 1)) - 1)
]

public_subnets = [
# this loop will create a one-line list as ["10.0.128.0/20", "10.0.144.0/20", "10.0.160.0/20", ...]
# with a length depending on how many Zones are available
# there is a zone Offset variable, to make sure no collisions are present with private subnet blocks
for zone_id in data.aws_availability_zones.available_azs.zone_ids :
cidrsubnet(var.main_network_block, var.subnet_prefix_extension, tonumber(substr(zone_id, length(zone_id) - 1, 1)) + var.zone_offset - 1)
]

# Enable single NAT Gateway to save some money
# WARNING: this could create a single point of failure, since we are creating a NAT Gateway in one AZ only
# feel free to change these options if you need to ensure full Availability without the need of running 'terraform apply'
# reference: https://registry.terraform.io/modules/terraform-aws-modules/vpc/aws/2.44.0#nat-gateway-scenarios
enable_nat_gateway     = true
single_nat_gateway     = true
one_nat_gateway_per_az = false
enable_dns_hostnames   = true
reuse_nat_ips          = true
external_nat_ip_ids    = [aws_eip.nat_gw_elastic_ip.id]

# Add VPC/Subnet tags required by EKS
tags = {
"kubernetes.io/cluster/${var.cluster_name}" = "shared"
iac_environment                             = var.iac_environment_tag
}
public_subnet_tags = {
"kubernetes.io/cluster/${var.cluster_name}" = "shared"
"kubernetes.io/role/elb"                    = "1"
iac_environment                             = var.iac_environment_tag
}
private_subnet_tags = {
"kubernetes.io/cluster/${var.cluster_name}" = "shared"
"kubernetes.io/role/internal-elb"           = "1"
iac_environment                             = var.iac_environment_tag
}
}
```

**Note:** The tags added to the subnets is very important. The Kubernetes Cloud Controller Manager (cloud-controller-manager) and AWS Load Balancer Controller (aws-load-balancer-controller) needs to identify the cluster’s. To do that, it querries the cluster’s subnets by using the tags as a filter.

3. For public and private subnets that use load balancer resources: each subnet must be tagged
```
Key: kubernetes.io/cluster/cluster-name
Value: shared
```

4. For private subnets that use internal load balancer resources: each subnet must be tagged
```
Key: kubernetes.io/role/internal-elb
Value: 1
```

5. For public subnets that use internal load balancer resources: each subnet must be tagged
```
Key: kubernetes.io/role/elb
Value: 1
```

6. Create a file – variables.tf
```
# create some variables
variable "cluster_name" {
type        = string
description = "EKS cluster name."
}
variable "iac_environment_tag" {
type        = string
description = "AWS tag to indicate environment name of each infrastructure object."
}
variable "name_prefix" {
type        = string
description = "Prefix to be used on each infrastructure object Name created in AWS."
}
variable "main_network_block" {
type        = string
description = "Base CIDR block to be used in our VPC."
}
variable "subnet_prefix_extension" {
type        = number
description = "CIDR block bits extension to calculate CIDR blocks of each subnetwork."
}
variable "zone_offset" {
type        = number
description = "CIDR block bits extension offset to calculate Public subnets, avoiding collisions with Private subnets."
}
```

7. Create a file – data.tf – This will pull the available AZs for use.
```
# get all available AZs in our region
data "aws_availability_zones" "available_azs" {
state = "available"
}
data "aws_caller_identity" "current" {} # used for accesing Account ID and ARN
```
## BUILDING ELASTIC KUBERNETES SERVICE (EKS) WITH TERRAFORM – PART 2
8. Create a file – eks.tf and provision EKS cluster (Create the file only if you are not using your existing Terraform code. Otherwise you can simply append it to the main.tf from your existing code) Read more about this module from the official documentation here – Reading it will help you understand more about the rich features of the module. https://registry.terraform.io/modules/terraform-aws-modules/eks/aws/18.20.5
```
module "eks_cluster" {
  source  = "terraform-aws-modules/eks/aws"
  version = "~> 18.0"
  cluster_name    = var.cluster_name
  cluster_version = "1.22"
  vpc_id     = module.vpc.vpc_id
  subnet_ids = module.vpc.private_subnets
  cluster_endpoint_private_access = true
  cluster_endpoint_public_access = true

  # Self Managed Node Group(s)
  self_managed_node_group_defaults = {
    instance_type                          = var.asg_instance_types[0]
    update_launch_template_default_version = true
  }
  self_managed_node_groups = local.self_managed_node_groups

  # aws-auth configmap
  create_aws_auth_configmap = true
  manage_aws_auth_configmap = true
  aws_auth_users = concat(local.admin_user_map_users, local.developer_user_map_users)
  tags = {
    Environment = "prod"
    Terraform   = "true"
  }
}
```
8.Create a file – **locals.tf** to create local variables. Terraform does not allow assigning variable to variables. There is good reasons for that to avoid repeating your code unecessarily. So a terraform way to achieve this would be to use locals so that your code can be kept DRY
```
# render Admin & Developer users list with the structure required by EKS module
locals {
  admin_user_map_users = [
    for admin_user in var.admin_users :
    {
      userarn  = "arn:aws:iam::${data.aws_caller_identity.current.account_id}:user/${admin_user}"
      username = admin_user
      groups   = ["system:masters"]
    }
  ]
  developer_user_map_users = [
    for developer_user in var.developer_users :
    {
      userarn  = "arn:aws:iam::${data.aws_caller_identity.current.account_id}:user/${developer_user}"
      username = developer_user
      groups   = ["${var.name_prefix}-developers"]
    }
  ]

  self_managed_node_groups = {
    worker_group1 = {
      name = "${var.cluster_name}-wg"

      min_size      = var.autoscaling_minimum_size_by_az * length(data.aws_availability_zones.available_azs.zone_ids)
      desired_size      = var.autoscaling_minimum_size_by_az * length(data.aws_availability_zones.available_azs.zone_ids)
      max_size  = var.autoscaling_maximum_size_by_az * length(data.aws_availability_zones.available_azs.zone_ids)
      instance_type = var.asg_instance_types[0].instance_type

      bootstrap_extra_args = "--kubelet-extra-args '--node-labels=node.kubernetes.io/lifecycle=spot'"

      block_device_mappings = {
        xvda = {
          device_name = "/dev/xvda"
          ebs = {
            delete_on_termination = true
            encrypted             = false
            volume_size           = 10
            volume_type           = "gp2"
          }
        }
      }

      use_mixed_instances_policy = true
      mixed_instances_policy = {
        instances_distribution = {
          spot_instance_pools = 4
        }

        override = var.asg_instance_types
      }
    }
  }
}
```

9.Add more variables to the variables.tf file
```
# create some variables
variable "admin_users" {
  type        = list(string)
  description = "List of Kubernetes admins."
}
variable "developer_users" {
  type        = list(string)
  description = "List of Kubernetes developers."
}
variable "asg_instance_types" {
  description = "List of EC2 instance machine types to be used in EKS."
}
variable "autoscaling_minimum_size_by_az" {
  type        = number
  description = "Minimum number of EC2 instances to autoscale our EKS cluster on each AZ."
}
variable "autoscaling_maximum_size_by_az" {
  type        = number
  description = "Maximum number of EC2 instances to autoscale our EKS cluster on each AZ."
}

```
10. Create a file – variables.tfvars to set values for variables. i changed this to terraform.tfvars
```
cluster_name            = "tooling-app-eks"
iac_environment_tag     = "development"
name_prefix             = "darey-io-eks"
main_network_block      = "10.0.0.0/16"
subnet_prefix_extension = 4
zone_offset             = 8

# Ensure that these users already exist in AWS IAM. Another approach is that you can introduce an iam.tf file to manage users separately, get the data source and interpolate their ARN.
admin_users                    = ["darey", "solomon"]
developer_users                = ["leke", "david"]
asg_instance_types             = [ { instance_type = "t3.small" }, { instance_type = "t2.small" }, ]
autoscaling_minimum_size_by_az = 1
autoscaling_maximum_size_by_az = 10
```

11. Create file – provider.tf
```
provider "aws" {
  region = "us-west-1"
}

provider "random" {
}
```

13. Run terraform init

14. Run Terraform plan – Your plan should have an output
***
Plan: 41 to add, 0 to change, 0 to destroy.


![Screenshot from 2023-10-14 17-53-34](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/67fa56ac-4401-450c-b6dd-b902c479c81e)
15. Run Terraform apply
This will begin to create cloud resources, and fail at some point with the error
***
╷
│ Error: Post "http://localhost/api/v1/namespaces/kube-system/configmaps": dial tcp [::1]:80: connect: connection refused
│ 
│   with module.eks-cluster.kubernetes_config_map.aws_auth[0],
│   on .terraform/modules/eks-cluster/aws_auth.tf line 63, in resource "kubernetes_config_map" "aws_auth":
│   63: resource "kubernetes_config_map" "aws_auth" {
***
That is because for us to connect to the cluster using the kubeconfig, Terraform needs to be able to connect and set the credentials correctly.

Let fix the problem in the next section
# FIXING THE ERROR
To fix this problem

Append to the file **data.tf**
```
# get EKS cluster info to configure Kubernetes and Helm providers
data "aws_eks_cluster" "cluster" {
  name = module.eks_cluster.cluster_id
}
data "aws_eks_cluster_auth" "cluster" {
  name = module.eks_cluster.cluster_id
}
```

Append to the file provider.tf
```
# get EKS authentication for being able to manage k8s objects from terraform
provider "kubernetes" {
  host                   = data.aws_eks_cluster.cluster.endpoint
  cluster_ca_certificate = base64decode(data.aws_eks_cluster.cluster.certificate_authority.0.data)
  token                  = data.aws_eks_cluster_auth.cluster.token
}
```
![Screenshot from 2023-10-16 16-08-01](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/d9d5af78-db94-4271-a3e9-9a76463f609c)

Run the init and plan again – This time you will see
***
  # module.eks-cluster.kubernetes_config_map.aws_auth[0] will be created
  + resource "kubernetes_config_map" "aws_auth" {
      + data = {
          + "mapAccounts" = jsonencode([])
          + "mapRoles"    = <<-EOT
                - "groups":
                  - "system:bootstrappers"
                  - "system:nodes"
                  "rolearn": "arn:aws:iam::696742900004:role/tooling-app-eks20210718113602300300000009"
                  "username": "system:node:{{EC2PrivateDNSName}}"
            EOT
          + "mapUsers"    = <<-EOT
                - "groups":
                  - "system:masters"
                  "userarn": "arn:aws:iam::696742900004:user/dare"
                  "username": "dare"
                - "groups":
                  - "system:masters"
                  "userarn": "arn:aws:iam::696742900004:user/solomon"
                  "username": "solomon"
                - "groups":
                  - "darey-io-eks-developers"
                  "userarn": "arn:aws:iam::696742900004:user/leke"
                  "username": "leke"
                - "groups":
                  - "darey-io-eks-developers"
                  "userarn": "arn:aws:iam::696742900004:user/david"
                  "username": "david"
            EOT
        }
      + id   = (known after apply)

      + metadata {
          + generation       = (known after apply)
          + labels           = {
              + "app.kubernetes.io/managed-by" = "Terraform"
              + "terraform.io/module"          = "terraform-aws-modules.eks.aws"
            }
          + name             = "aws-auth"
          + namespace        = "kube-system"
          + resource_version = (known after apply)
          + uid              = (known after apply)
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.
***
![Screenshot from 2023-10-16 16-09-14](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/e92ca590-85c7-46b8-b915-287da70cf7b5)
![Screenshot from 2023-10-16 16-37-37](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/1fe261c8-cfc7-4564-9c02-f7abaa9ed0e6)

Create kubeconfig file using awscli.
```
aws eks update-kubeconfig --name tooling-app-eks --region us-east-2 --kubeconfig kubeconfig
```
![Screenshot from 2023-11-08 23-00-22](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/a9743ca3-2bb9-40a2-a1f4-c95389cef012)


# DEPLOY APPLICATIONS WITH HELM
In Project 22, you experienced the use of manifest files to define and deploy resources like pods, deployments, and services into Kubernetes cluster. Here, you will do the same thing except that it will not be passed through kubectl. In the real world, Helm is the most popular tool used to deploy resources into kubernetes. That is because it has a rich set of features that allows deployments to be packaged as a unit. Rather than have multiple YAML files managed individually – which can quickly become messy.

A Helm chart is a definition of the resources that are required to run an application in Kubernetes. Instead of having to think about all of the various deployments/services/volumes/configmaps/ etc that make up your application, you can use a command like

### here practicing with Helm
![Screenshot from 2023-10-16 21-53-08](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/007a049d-039c-444c-8556-c1b40d1351d5)
![Screenshot from 2023-10-16 21-54-39](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/a1029f23-fd35-4798-a9a6-d829cc0a2524)
![Screenshot from 2023-10-16 21-55-36](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/be90e736-9964-46de-b91b-f5907b8831f6)
```
helm install stable/mysql
```
and Helm will make sure all the required resources are installed. In addition you will be able to tweak helm configuration by setting a single variable to a particular value and more or less resources will be deployed. For example, enabling slave for MySQL so that it can have read only replicas.

Behind the scenes, a helm chart is essentially a bunch of YAML manifests that define all the resources required by the application. Helm takes care of creating the resources in Kubernetes (where they don’t exist) and removing old resources.

**Lets begin to gradually walk through how to use Helm (Credit – https://andrewlock.net/series/deploying-asp-net-core-applications-to-kubernetes/)**
1. Parameterising YAML manifests using Helm templates

Let’s consider that our Tooling app have been Dockerised into an image called tooling-app, and that you wish to deploy with Kubernetes. Without helm, you would create the YAML manifests defining the deployment, service, and ingress, and apply them to your Kubernetes cluster using kubectl apply. Initially, your application is version 1, and so the Docker image is tagged as tooling-app:1.0.0. A simple deployment manifest might look something like the following:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tooling-app-deployment
  labels:
    app: tooling-app
spec:
  replicas: 3
  strategy: 
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
  selector:
    matchLabels:
      app: tooling-app
  template:
    metadata:
      labels:
        app: tooling-app
    spec:
      containers:
      - name: tooling-app
        image: "tooling-app:1.0.0"
        ports:
        - containerPort: 80
```
Now lets imagine you produce another version of your app, version 1.1.0. How do you deploy that? Assuming nothing needs to be changed with the service or ingress, it may be as simple as copying the deployment manifest and replacing the image defined in the spec section. You would then re-apply this manifest to the cluster, and the deployment would be updated, performing a rolling-update as I described in my first post.

The main problem with this is that all of the values specific to your application – the labels and the image names etc – are mixed up with the "mechanical" definition of the manifest.

Helm tackles this by splitting the configuration of a chart out from its basic definition. For example, instead of baking the name of your app or the specific container image into the manifest, you can provide those when you install the chart into the cluster.

For example, a simple templated version of the previous deployment might look like the following:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-deployment
  labels:
    app: "{{ template "name" . }}"
spec:
  replicas: 3
  strategy: 
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
  selector:
    matchLabels:
      app: "{{ template "name" . }}"
  template:
    metadata:
      labels:
        app: "{{ template "name" . }}"
    spec:
      containers:
      - name: "{{ template "name" . }}"
        image: "{{ .Values.image.name }}:{{ .Values.image.tag }}"
        ports:
        - containerPort: 80
```
This example demonstrates a number of features of Helm templates:

The template is based on YAML, with {{ }} mustache syntax defining dynamic sections.
Helm provides various variables that are populated at install time. For example, the {{.Release.Name}} allows you to change the name of the resource at runtime by using the release name. Installing a Helm chart creates a release (this is a Helm concept rather than a Kubernetes concept).
You can define helper methods in external files. The {{template "name"}} call gets a safe name for the app, given the name of the Helm chart (but which can be overridden). By using helper functions, you can reduce the duplication of static values (like tooling-app), and hopefully reduce the risk of typos.

You can manually provide configuration at runtime. The {{.Values.image.name}} value for example is taken from a set of default values, or from values provided when you call helm install. There are many different ways to provide the configuration values needed to install a chart using Helm. Typically, you would use two approaches:

A values.yaml file that is part of the chart itself. This typically provides default values for the configuration, as well as serving as documentation for the various configuration values.

When providing configuration on the command line, you can either supply a file of configuration values using the -f flag. We will see a lot more on this later on.

**Now lets setup Helm and begin to use it.**

According to the official documentation here, there are different options to installing Helm. But we will build the source code to create the binary.

1. Download the tar.gz file from the project’s Github release page. Or simply use wget to download version 3.6.3 directly
```
wget https://github.com/helm/helm/archive/refs/tags/v3.6.3.tar.gz
```
2. Unpack the tar.gz file
```
tar -zxvf v3.6.3.tar.gz 
```
3. cd into the unpacked directory
```
cd helm-3.6.3
```
4. Build the source code using make utility
```
make build
```
If you do not have make installed or for any other reason, you cannot install the tool, simply use the official documentation here for other options.

5. Helm binary will be in the bin folder. Simply move it to the bin directory on your system. You cna check other tools to know where that is. fOr example, check where pwd utility is being called from by running which pwd. Assuming the output is /usr/local/bin. You can move the helm binary there.
```
sudo mv bin/helm /usr/local/bin/
```

6. Check that Helm is installed
```
helm version
```
output:
***
version.BuildInfo{Version:"v3.6+unreleased", GitCommit:"13f07e8adbc57b0e3f96b42340d6f44bdc8d5016", GitTreeState:"dirty", GoVersion:"go1.15.5"}
***
**I didnt use the above for my helm installation, i went through the helm documentation itself**

![Screenshot from 2023-10-16 22-32-53](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/2df45e6b-f1e6-43c5-b6af-30872fa974b8)


## DEPLOY JENKINS WITH HELM
Before we begin to develop our own helm charts, lets make use of publicly available charts to deploy all the tools that we need.

One of the amazing things about helm is the fact that you can deploy applications that are already packaged from a public helm repository directly with very minimal configuration. An example is Jenkins.

1. Visit Artifact Hub to find packaged applications as Helm Charts
2. Search for Jenkins
![Screenshot from 2023-10-16 22-35-25](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/991d0e8c-20c0-4291-b325-e80b5af6dd8e)

3. Add the repository to helm so that you can easily download and deploy
```
helm repo add jenkins https://charts.jenkins.io
```
![Screenshot from 2023-10-16 22-36-21](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/6c18fb6e-8ba2-4c0c-823a-b9fb309c563d)

4. Update helm repo
```
helm repo update 
```
![Screenshot from 2023-10-16 22-37-46](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/0eacb0f2-6476-4ab8-a44e-089fc2400af6)


5. Install the chart
```
helm install [RELEASE_NAME] jenkins/jenkins --kubeconfig [kubeconfig file]
```
The chart i saw is 
```
helm install my-jenkins jenkinsci/jenkins --version 4.7.2
```
helm install my-jenkins jenkinsci/jenkins --kubeconfig kubeconfig
helm install my-jenkins bitnami/jenkins --kubeconfig kubeconfig
![Screenshot from 2023-10-16 22-39-49](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/61d3c5ac-27e2-4cbd-87b5-871bd77fcc24)
Got this error repeatedly, so i tried bitnami/jenkins
it worked because i was on minikube
![Screenshot from 2023-10-16 22-49-13](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/949184b9-02a2-4114-9210-6fe77db5cf4e)
after several errors in trying to install jenkins, it was successful

![Screenshot from 2023-10-24 13-19-22](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/7a1bf6cf-10a0-43f3-9406-e80fcead09ef)

You should see an output like this
***
NAME: jenkins
LAST DEPLOYED: Sun Aug  1 12:38:53 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get your 'admin' user password by running:
  kubectl exec --namespace default -it svc/jenkins -c jenkins -- /bin/cat /run/secrets/chart-admin-password && echo
2. Get the Jenkins URL to visit by running these commands in the same shell:
  echo http://127.0.0.1:8080
  kubectl --namespace default port-forward svc/jenkins 8080:8080

3. Login with the password from step 1 and the username: admin
4. Configure security realm and authorization strategy
5. Use Jenkins Configuration as Code by specifying configScripts in your values.yaml file, see documentation: http:///configuration-as-code and examples: https://github.com/jenkinsci/configuration-as-code-plugin/tree/master/demos

For more information on running Jenkins on Kubernetes, visit:
https://cloud.google.com/solutions/jenkins-on-container-engine

For more information about Jenkins Configuration as Code, visit:
https://jenkins.io/projects/jcasc/

NOTE: Consider using a custom image with pre-installed plugins
***

6. Check the Helm deployment
```
helm ls --kubeconfig [kubeconfig file]
```
Output:
***
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
jenkins default         1               2021-08-01 12:38:53.429471 +0100 BST    deployed        jenkins-3.5.9   2.289.3 
***
![Screenshot from 2023-10-16 22-52-32](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/32ac957f-626b-4e43-9ecd-9f3cea6e6b69)
![Screenshot from 2023-11-08 23-21-16](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/425165b2-9d48-4271-aaea-b394ff531fdc)

7.Check the pods
```
kubectl get pods --kubeconfig kubeconfig
```

Output:
***
NAME        READY   STATUS    RESTARTS   AGE
jenkins-0   2/2     Running   0          6m14s
***
![Screenshot from 2023-10-24 13-39-57](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/4e3eaa09-c2b2-4012-a124-7391599cc91e)
pods not running yet
running after another trial
![Screenshot from 2023-11-08 23-22-52](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/9a6bc5df-59f4-4400-bc34-86d06196b792)
8. Describe the running pod (review the output and try to understand what you see)
```
kubectl describe pod my-jenkins-69c85ccb58-lkfwm --kubeconfig kubeconfig
```
![Screenshot from 2023-11-08 23-24-00](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/612e0072-5be4-4e61-901a-c7fe10ca4951)

9. Check the logs of the running pod
```
kubectl logs my-jenkins-69c85ccb58-lkfwm --kubeconfig kubeconfig
```
![Screenshot from 2023-10-24 13-41-50](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/bbdb4f74-7fe9-48cc-94c5-7f6714b19cfa)


![Screenshot from 2023-10-16 22-57-39](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/96977fad-4a78-4a2b-a394-6c2cafc68db9)
![Screenshot from 2023-10-16 22-59-31](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/048ffc9f-3e16-40e7-b9f4-495ad79c53cd)
![Screenshot from 2023-11-08 23-24-59](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/7932f55b-509e-4c14-909f-f05c8417f37a)
You will notice an output with an error
***
error: a container name must be specified for pod jenkins-0, choose one of: [jenkins config-reload] or one of the init containers: [init]
***

This is because the pod has a Sidecar container alongside with the Jenkins container. As you can see fromt he error output, there is a list of containers inside the pod [jenkins config-reload] i.e jenkins and config-reload containers. The job of the config-reload is mainly to help Jenkins to reload its configuration without recreating the pod.

Therefore we need to let kubectl know, which pod we are interested to see its log. Hence, the command will be updated like:
```
kubectl logs jenkins-0 -c jenkins --kubeconfig [kubeconfig file]
```
![Screenshot from 2023-10-24 13-45-06](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/00103036-0310-4801-9fce-94ddd0dd3ddb)

![Screenshot from 2023-10-16 23-03-46](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/ef64d99b-f778-466c-b4eb-e8e761594c94)

10. Now lets avoid calling the [kubeconfig file] everytime. Kubectl expects to find the default kubeconfig file in the location ~/.kube/config. But what if you already have another cluster using that same file? It doesn’t make sense to overwrite it. What you will do is to merge all the kubeconfig files together using a kubectl plugin called [konfig](https://github.com/corneliusweig/konfig) and select whichever one you need to be active.

* Install a package manager for kubectl called krew so that it will enable you to install plugins to extend the functionality of kubectl. Read more about it [Here](https://github.com/kubernetes-sigs/krew)
  i Couldnt install KREW
* Install the [konfig plugin](https://github.com/corneliusweig/konfig)
```
  kubectl krew install konfig
```
* Import the kubeconfig into the default kubeconfig file. Ensure to accept the prompt to overide.
```
  sudo kubectl konfig import --save kubeconfig
```
* Show all the contexts – Meaning all the clusters configured in your kubeconfig. If you have more than 1 Kubernetes clusters configured, you will see them all in the output.
```
  kubectl config get-contexts
```
* Set the current context to use for all kubectl and helm commands
```
  kubectl config use-context [name of EKS cluster]
```
* Test that it is working without specifying the --kubeconfig flag
```
  kubectl get po
```

Output:
***
  NAME        READY   STATUS    RESTARTS   AGE
  jenkins-0   2/2     Running   0          84m
***

* Display the current context. This will let you know the context in which you are using to interact with Kubernetes.
```
  kubectl config current-context
```
![Screenshot from 2023-11-09 00-19-00](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/d55e07c4-e7f7-4c4e-a642-f011e3e413fa)

11. Now that we can use kubectl without the --kubeconfig flag, Lets get access to the Jenkins UI. (In later projects we will further configure Jenkins. For now, it is to set up all the tools we need)

* There are some commands that was provided on the screen when Jenkins was installed with Helm. See number 5 above. Get the password to the admin user
```
  kubectl exec --namespace default -it svc/jenkins -c jenkins -- /bin/cat /run/secrets/chart-admin-password && echo
```
since i am using the Bitanmi Jenkins i used the following to get the password

kubectl get secret --kubeconfig kubeconfig my-jenkins -o json
path="{.data.jenkins-password}" | base64 -d 


![Screenshot from 2023-11-09 00-34-16](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/02e446c8-b354-430e-b283-2ac4e4b18fbe)

![Screenshot from 2023-11-09 00-35-54](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/aff38fa2-01fc-4a8b-a92e-bef2394acb72)

* Use port forwarding to access Jenkins from the UI
```
kubectl --namespace default port-forward svc/jenkins 8080:8080
```
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/0be63110-3abf-4200-88f8-f51fbf3423e3)


Go to the browser localhost:8080 and authenticate with the username and password from number 1 above

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/cb0a4cb9-727d-419d-a095-14369a12d906)

![Screenshot from 2023-11-09 00-35-54](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/aff38fa2-01fc-4a8b-a92e-bef2394acb72)


**QUICK TASK FOR YOU**
Now setup the following tools using Helm
This section will be quite challenging for you because you will need to spend some time to research the charts, read their documentations and understand how to get an application running in your cluster by simply running a helm install command.

Artifactory
Hashicorp Vault
Prometheus
Grafana
Elasticsearch ELK using ECK
Succesfully installing all the 5 tools is a great experience to have. But, joining the Masterclass you will be able to see how this should be done end to end.

In the next project,

You will write custom Helm charts
Configure Ingress for all the tools and applications running in the cluster
Integrate Secrets management using Hashicorp Vault
Integrate Logging with ELK
Inetegrate monitoring with Prometheus and Grafana

