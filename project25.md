# DEPLOYING AND PACKAGING APPLICATIONS INTO KUBERNETES WITH HELM
In the previous project, you started experiencing helm as a tool used to deploy an application into Kubernetes. You probably also tried installing more tools apart from Jenkins.

In this project, you will experience deploying more DevOps tools, get familiar with some of the real world issues faced during such deployments and how to fix them. You will learn how to tweak helm values files to automate the configuration of the applications you deploy. Finally, once you have most of the DevOps tools deployed, you will experience using them and relate with the DevOps cycle and how they fit into the entire ecosystem.

Our focus will be on the following tools.

Artifactory
Hashicorp Vault
Prometheus
Grafana
Elasticsearch ELK using ECK
For the tools that require paid license, don’t worry, you will also learn how to get the license for free and have true experience exactly how they are used in the real world.

Lets start first with Artifactory. What is it exactly?

Artifactory is part of a suit of products from a company called Jfrog. Jfrog started out as an artifact repository where software binaries in different formats are stored. Today, Jfrog has transitioned from an artifact repository to a DevOps Platform that includes CI and CD capabilities. This has been achieved by offering more products in which Jfrog Artifactory is part of. Other offerings include

JFrog Pipelines – a CI-CD product that works well with its Artifactory repository. Think of this product as an alternative to Jenkins.
JFrog Xray – a security product that can be built-into various steps within a JFrog pipeline. Its job is to scan for security vulnerabilities in the stored artifacts. It is able to scan all dependent code.
In this project, the requirement is to use Jfrog Artifactory as a private registry for the organisation’s Docker images and Helm charts. This requirement will satisfy part of the company’s corporate security policies to never download artifacts directly from the public into production systems. We will eventually have a CI pipeline that initially pulls public docker images and helm charts from the internet, store in artifactory and scan the artifacts for security vulnerabilities before deploying into the corporate infrastructure. Any found vulnerabilities will immediately trigger an action to quarantine such artifacts.

Lets get into action and see how all of these work.

Deploy Jfrog Artifactory into Kubernetes
The best approach to easily get Artifactory into kubernetes is to use helm.

Search for an official helm chart for Artifactory on Artifact Hub

Click on See all results

Use the filter checkbox on the left to limit the return data. As you can see in the image below, “Helm” is selected. In some cases, you might select “Official”. Then click on the first option from the result.

Review the Artifactory page
Click on the install menu on the right to see the installation commands.
Add the jfrog remote repository on your laptop/computer
```
helm repo add jfrog https://charts.jfrog.io
```
Create a namespace called tools where all the tools for DevOps will be deployed. (In previous project, you installed Jenkins in the default namespace. You should uninstall Jenkins there and install in the new namespace)
```
kubectl create ns tools
```


Update the helm repo index on your laptop/computer
```
helm repo update
```

Install artifactory
```
helm upgrade --install artifactory jfrog/artifactory --version 107.38.10 -n tools
```
![Screenshot from 2023-10-24 15-14-19](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/35c0f525-ab87-474d-a2fd-416add3068b5)

NOTE:

We have used upgrade --install flag here instead of helm install artifactory jfrog/artifactory This is a better practice, especially when developing CI pipelines for helm deployments. It ensures that helm does an upgrade if there is an existing installation. But if there isn’t, it does the initial install. With this strategy, the command will never fail. It will be smart enough to determine if an upgrade or fresh installation is required.
The helm chart version to install is very important to specify. So, the version at the time of writing may be different from what you will see from Artifact Hub. So, replace the version number to the desired

**Getting the Artifactory URL**
Lets break down the first Next Step.

1. The artifactory helm chart comes bundled with the Artifactory software, a PostgreSQL database and an Nginx proxy which it uses to configure routes to the different capabilities of Artifactory. Getting the pods after some time, you should see something like the below

```
kubectl get pods -n tools
```

2. Each of the deployed application have their respective services. This is how you will be able to reach either of them.
```
kubectl get svc -n tools
```

3. Notice that, the Nginx Proxy has been configured to use the service type of LoadBalancer. Therefore, to reach Artifactory, we will need to go through the Nginx proxy’s service. Which happens to be a load balancer created in the cloud provider. Run the kubectl command to retrieve the Load Balancer URL.

```
kubectl get svc artifactory-artifactory-nginx -n tools
```
![Screenshot from 2023-10-24 15-40-55](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/85d9382f-d1ee-4b68-9edc-4963055eab05)
i realised that my pods were in init:CrashLoopBackOff, and i tried troubleshooting it

i used eks and no error

![Screenshot from 2023-10-24 19-10-51](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/0ec4af6d-7171-431e-a1a8-ab7b118441c8)

Is the Load Balancer Service type the Ideal configuration option to use in the Real World?
Setting the service type to Load Balancer is the easiest way to get started with exposing applications running in kubernetes externally. But provisioning load balancers for each application can become very expensive over time, and more difficult to manage. Especially when tens or even hundreds of applications are deployed.

The best approach is to use Kubernetes Ingress instead. But to do that, we will have to deploy an Ingress Controller.

A huge benefit of using the ingress controller is that we will be able to use a single load balancer for different applications we deploy. Therefore, Artifactory and any other tools can reuse the same load balancer. Which reduces cloud cost, and overhead of managing multiple load balancers. more on that later.

For now, we will leave Artifactory, move on to the next phase of configuration (Ingress, DNS(Route53) and Cert Manager), and then return to Artifactory to complete the setup so that it can serve as a private docker registry and repository for private helm charts.


## DEPLOYING INGRESS CONTROLLER AND MANAGING INGRESS RESOURCES
Before we discuss what ingress controllers are, it will be important to start off understanding about the Ingress resource.

An ingress is an API object that manages external access to the services in a kubernetes cluster. It is capable to provide load balancing, SSL termination and name-based virtual hosting. In otherwords, Ingress exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource.

Here is a simple example where an Ingress sends all its traffic to one Service:

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/9f2391d3-ca92-4087-95cd-4d4737dd63bc)
image credit: kubernetes.io

An ingress resource for Artifactory would like like below
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: artifactory
spec:
  ingressClassName: nginx
  rules:
  - host: "tooling.artifactory.sandbox.svc.darey.io"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: artifactory
            port:
              number: 8082
```
* An Ingress needs apiVersion, kind, metadata and spec fields
* The name of an Ingress object must be a valid DNS subdomain name
* Ingress frequently uses annotations to configure some options depending on the Ingress controller.
* Different Ingress controllers support different annotations. Therefore it is important to be up to date with the ingress controller’s specific documentation to know what annotations are supported.
* It is recommended to always specify the ingress class name with the spec ingressClassName: nginx. This is how the Ingress controller is selected, especially when there are multiple configured ingress controllers in the cluster.
* The domain darey.io should be replaced with your own domain.

**Ingress controller**
If you deploy the yaml configuration specified for the ingress resource without an ingress controller, it will not work. In order for the Ingress resource to work, the cluster must have an ingress controller running.

Unlike other types of controllers which run as part of the kube-controller-manager. Such as the Node Controller, Replica Controller, Deployment Controller, Job Controller, or Cloud Controller. Ingress controllers are not started automatically with the cluster.

Kubernetes as a project supports and maintains AWS, GCE, and NGINX ingress controllers.

There are many other 3rd party Ingress controllers that provide similar functionalities with their own unique features, but the 3 mentioned earlier are currently supported and maintained by Kubernetes. Some of these other 3rd party Ingress controllers include but not limited to the following;

AKS Application Gateway Ingress Controller (Microsoft Azure), Istio, Traefik, Ambassador, HA Proxy Ingress, Kong and Gloo

An example comparison matrix of some of the controllers can be found here : [.](https://kubevious.io/blog/post/comparing-top-ingress-controllers-for-kubernetes#comparison-matrix) 

Understanding their unique features will help businesses determine which product works well for their respective requirements.

It is possible to deploy any number of ingress controllers in the same cluster. That is the essence of an ingress class. By specifying the spec ingressClassName field on the ingress object, the appropriate ingress controller will be used by the ingress resource.

Lets get into action and see how all of these fits together.

**Deploy Nginx Ingress Controller**
On this project, we will deploy and use the Nginx Ingress Controller. It is always the default choice when starting with Kubernetes projects. It is reliable and easy to use.

Since this controller is maintained by Kubernetes, there is an official guide the installation process. Hence, we wont be using artifacthub.io here. Even though you can still find ready to go charts there, it just makes sense to always use the official guide in this scenario.

Using the Helm approach, according to the official guide;

1. Install Nginx Ingress Controller in the ingress-nginx namespace
```
helm upgrade --install ingress-nginx ingress-nginx \
--repo https://kubernetes.github.io/ingress-nginx \
--namespace ingress-nginx --create-namespace
```

Notice:

This command is idempotent:

if the ingress controller is not installed, it will install it,

if the ingress controller is already installed, it will upgrade it.

Self Challenge Task – Delete the installation after running above command. Then try to re-install it using a slightly different method you are already familiar with. Ensure NOT to use the flag --repo

Hint – Run the helm repo add command before installation
![Screenshot from 2023-10-26 21-50-39](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/1a4061e8-309b-4a9b-a413-363dd21bf753)
![Screenshot from 2023-10-26 21-57-07](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/fc7f057a-d30e-40c5-aaec-526d29e32057)

2. A few pods should start in the ingress-nginx namespace:
```
kubectl get pods --namespace=ingress-nginx
```
![Screenshot from 2023-10-26 22-01-06](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/022bc338-2c0c-4eb2-8584-612442e88038)

3. After a while, they should all be running. The following command will wait for the ingress controller pod to be up, running, and ready:
```
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=120s
```

4. Check to see the created load balancer in AWS.
```
kubectl get service -n ingress-nginx
```
Output:
***
NAME                                 TYPE           CLUSTER-IP      EXTERNAL-IP                                                              PORT(S)                      AGE
ingress-nginx-controller             LoadBalancer   172.16.11.35    a38db84e7d2104dc4b743ee6df1e667b-954094141.eu-west-2.elb.amazonaws.com   80:32516/TCP,443:31942/TCP   50s
ingress-nginx-controller-admission   ClusterIP      172.16.94.137   <none>                                                                   443/TCP                      50s
***
![Screenshot from 2023-10-26 22-02-44](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/377b79dc-358a-4d23-9ebd-f43bf8669327)

The ingress-nginx-controller service that was created is of the type LoadBalancer. That will be the load balancer to be used by all applications which require external access, and is using this ingress controller.


5. Check the IngressClass that identifies this ingress controller.
```
kubectl get ingressclass -n ingress-nginx
```

Output:
***
NAME    CONTROLLER             PARAMETERS   AGE
nginx   k8s.io/ingress-nginx   <none>       105s
***
![Screenshot from 2023-10-26 22-03-53](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/946f8b43-174a-4c8a-91f7-71642ccd6c71)


**Deploy Artifactory Ingress**


Now, it is time to configure the ingress so that we can route traffic to the Artifactory internal service, through the ingress controller’s load balancer.



Notice the spec section with the configuration that selects the ingress controller using the ingressClassName
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: artifactory
spec:
  ingressClassName: nginx
  rules:
  - host: "tooling.artifactory.sandbox.svc.darey.io"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: artifactory
            port:
              number: 8082
```
```
kubectl apply -f <filename.yaml> -n tools
```

Output:
***
NAME          CLASS   HOSTS                                   ADDRESS                                                                  PORTS   AGE
artifactory   nginx   tooling.artifactory.sandbox.svc.darey.io   a38db84e7d2104dc4b743ee6df1e667b-954094141.eu-west-2.elb.amazonaws.com   80      5s
***
![Screenshot from 2023-10-26 22-07-04](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/50e03f2b-ab77-4c97-893e-a5fc00a12a5f)
![Screenshot from 2023-10-26 22-09-35](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/319c6a53-500c-460c-b0f5-51a2e13cb645)

Now, take note of

* CLASS – The nginx controller class name nginx
* HOSTS – The hostname to be used in the browser tooling.artifactory.sandbox.svc.darey.io
* ADDRESS – The loadbalancer address that was created by the ingress controller


**Configure DNS**

If anyone were to visit the tool, it would be very inconvenient sharing the long load balancer address. Ideally, you would create a DNS record that is human readable and can direct request to the balancer. This is exactly what has been configured in the ingress object - host: "tooling.artifactory.sandbox.svc.darey.io" but without a DNS record, there is no way that host address can reach the load balancer.

The sandbox.svc.darey.io part of the domain is the configured HOSTED ZONE in AWS. So you will need to configure Hosted Zone in AWS console or as part of your infrastructure as code using terraform.
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/285c3e28-dfb7-433e-ad35-6d208a8be883)
f you purchased the domain directly from AWS, the hosted zone will be automatically configured for you. But if your domain is registered with a different provider such as freenon or namechaep, you will have to create the hosted zone and update the name servers.

**Create Route53 record**
Within the hosted zone is where all the necessary DNS records will be created. Since we are working on Artifactory, lets create the record to point to the ingress controller’s loadbalancer. There are 2 options. You can either use the CNAME or AWS Alias

**CNAME Method**
1. Select the HOSTED ZONE you wish to use, and click on the create record button

2. Add the subdomain tooling.artifactory, and select the record type CNAME

3. Successfully created record

4. Confirm that the DNS record has been properly propergated. Visit https://dnschecker.org and check the record. Ensure to select CNAME. The search should return green ticks for each of the locations on the left.

**AWS Alias Method**
1. In the create record section, type in the record name, and toggle the alias button to enable an alias. An alias is of the A DNS record type which basically routes directly to the load balancer. In the choose endpoint bar, select Alias to Application and Classic Load Balancer


2. Select the region and the load balancer required. You will not need to type in the load balancer, as it will already populate.


For detailed read on selecting between CNAME and Alias based records, read the official documentation

**Visiting the application from the browser**
So far, we now have an application running in Kubernetes that is also accessible externally. That means if you navigate to https://tooling.artifactory.sandbox.svc.darey.io/ (replace the full URL with your domain), it should load up the artifactory application.

Using Chrome browser will show something like the below. It shows that the site is indeed reachable, but insecure. This is because Chrome browsers do not load insecure sites by default. It is insecure because it either does not have a trusted TLS/SSL certificate, or it doesn’t have any at all.



Nginx Ingress Controller does configure a default TLS/SSL certificate. But it is not trusted because it is a self signed certificate that browsers are not aware of.

To confirm this,

1. Click on the Not Secure part of the browser.

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/4209197a-28c4-41e5-abfc-34b934a7c3d9)
2. Select the Certificate is not valid menu
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/ae912fd5-4c23-4c82-ada1-060155c1e96e)
3. You will see the details of the certificate. There you can confirm that yes indeed there is encryption configured for the traffic, the browser is just not cool with it.

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/643576bd-8fb2-47d7-918b-6f58b32e75e3)
Now try another browser. For example Internet explorer or Safari

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/7edb04c7-c65c-46d3-bcf7-9e97f67749c1)
**Explore Artifactory Web UI**

Now that we can access the application externally, although insecure, its time to login for some exploration. Afterwards we will make it a lot more secure and access our web application on any browser.

1. Get the default username and password – Run a helm command to output the same message after the initial install
```
helm test artifactory -n tools
```
Output:
***
NAME: artifactory
LAST DEPLOYED: Sat May 28 09:26:08 2022
NAMESPACE: tools
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Congratulations. You have just deployed JFrog Artifactory!

1. Get the Artifactory URL by running these commands:

   NOTE: It may take a few minutes for the LoadBalancer IP to be available.
         You can watch the status of the service by running 'kubectl get svc --namespace tools -w artifactory-artifactory-nginx'
   export SERVICE_IP=$(kubectl get svc --namespace tools artifactory-artifactory-nginx -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
   echo http://$SERVICE_IP/

2. Open Artifactory in your browser
   Default credential for Artifactory:
   user: admin
   password: password
***
2. Insert the username and password to load the Get Started page
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/3018a80a-80e1-453c-a497-f106c51dc465)

3. Reset the admin password

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/9904f826-71a8-43a8-a17b-5825b589a918)
4. Activate the Artifactory License. You will need to purchase a license to use Artifactory enterprise features.
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/297fc3c9-1781-44ed-80a6-fe0ca49c45c7)
5. For learning purposes, you can apply for a free trial license. Simply fill the form here and a license key will be delivered to your email in few minutes.
6. In exactly 1 minute, the license key had arrived. Simply copy the key and apply to the console.
7. Set the Base URL. Ensure to use https

8. Skip the Proxy setting
9. Skip creation of repositories for now. You will create them yourself later on.
10. finish the setup

Next, its time to fix the TLS/SSL configuration so that we will have a trusted HTTPS URL

# DEPLOYING CERT-MANAGER AND MANAGING TLS/SSL FOR INGRESS
Transport Layer Security (TLS), the successor of the now-deprecated Secure Sockets Layer (SSL), is a cryptographic protocol designed to provide communications security over a computer network.

The TLS protocol aims primarily to provide cryptography, including privacy (confidentiality), integrity, and authenticity through the use of certificates, between two or more communicating computer applications.

The certificates required to implement TLS must be issued by a trusted Certificate Authority (CA).

To see the list of trusted root Certification Authorities (CA) and their certificates used by Google Chrome, you need to use the Certificate Manager built inside Google Chrome as shown below:

1. Open the settings section of google chrome

2. Search for security
3. Select Manage Certificates
4. View the installed certificates in your browser

**Certificate Management in Kubernetes**
Ensuring that trusted certificates can be requested and issued from certificate authorities dynamically is a tedious process. Managing the certificates per application and keeping track of expiry is also a lot of overhead.

To do this, administrators will have to write complex scripts or programs to handle all the logic.

Cert-Manager comes to the rescue!

cert-manager adds certificates and certificate issuers as resource types in Kubernetes clusters, and simplifies the process of obtaining, renewing and using those certificates.

Similar to how Ingress Controllers are able to enable the creation of Ingress resource in the cluster, so also cert-manager enables the possibility to create certificate resource, and a few other resources that makes certificate management seamless.

It can issue certificates from a variety of supported sources, including Let’s Encrypt, HashiCorp Vault, and Venafi as well as private PKI. The issued certificates get stored as kubernetes secret which holds both the private key and public certificate.

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/8188e634-5ef0-4ac3-abd4-08e8ea7262b0)
In this project, We will use Let’s Encrypt with cert-manager. The certificates issued by Let’s Encrypt will work with most browsers because the root certificate that validates all it’s certificates is called “ISRG Root X1” which is already trusted by most browsers and servers.

You will find ISRG Root X1 in the list of certificates already installed in your browser.

Cert-maanager will ensure certificates are valid and up to date, and attempt to renew certificates at a configured time before expiry.

**Cert-Manager high Level Architecture**
Cert-manager works by having administrators create a resource in kubernetes called certificate issuer which will be configured to work with supported sources of certificates. This issuer can either be scoped globally in the cluster or only local to the namespace it is deployed to.

Whenever it is time to create a certificate for a specific host or website address, the process follows the pattern seen in the image below
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/73fa87ba-f75c-44de-8010-14e1f962b261)
After we have deployed cert-manager, you will see all of this in action.

**Deploying Cert-manager**
* Self Challenge Task Find cert-manager helm chart in Artifact Hub, follow the installation guide and deploy into Kubernetes
You should see an output like this
```
NAME: cert-manager
LAST DEPLOYED: Fri Mar 11 14:15:51 2022
NAMESPACE: cert-manager
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
cert-manager v1.7.1 has been deployed successfully!

In order to begin issuing certificates, you will need to set up a ClusterIssuer
or Issuer resource (for example, by creating a 'letsencrypt-staging' issuer).

More information on the different types of issuers and how to configure them
can be found in our documentation:

https://cert-manager.io/docs/configuration/

For information on how to configure cert-manager to automatically provision
Certificates for Ingress resources, take a look at the `ingress-shim`
documentation:

https://cert-manager.io/docs/usage/ingress/
```

**Certificate Issuer**

Next, is to create an Issuer. We will use a Cluster Issuer so that it can be scoped globally. Assuming that we will be using darey.io domain. Simply update this yaml file and deploy with kubectl. In the section that follows, we will break down each part of the file.
```
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  namespace: "cert-manager"
  name: "letsencrypt-prod"
spec:
  acme:
    server: "https://acme-v02.api.letsencrypt.org/directory"
    email: "infradev@oldcowboyshop.com"
    privateKeySecretRef:
      name: "letsencrypt-prod"
    solvers:
    - selector:
        dnsZones:
          - "darey.io"
      dns01:
        route53:
          region: "eu-west-2"
          hostedZoneID: "Z2CD4NTR2FDPZ"
```
Lets break down the content to undertsand all the sections

Section 1 – The standard kubernetes section that defines the apiVersion, Kind, and metadata. The Kind here is a ClusterIssuer which means it is scoped globally.
```
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
namespace: "cert-manager"
name: "letsencrypt-prod"
```

Section 2 – In the spec section, an ACME – Automated Certificate Management Environment issuer type is specified here. When you create a new ACME Issuer, cert-manager will generate a private key which is used to identify you with the ACME server.

Certificates issued by public ACME servers are typically trusted by client’s computers by default. This means that, for example, visiting a website that is backed by an ACME certificate issued for that URL, will be trusted by default by most client’s web browsers. ACME certificates are typically free.

Let’s Encrypt uses the ACME protocol to verify that you control a given domain name and to issue you a certificate. You can either use the let’s encrypt Production server address https://acme-v02.api.letsencrypt.org/directory which can be used for all production websites. Or it can be replaced with the staging URL https://acme-staging-v02.api.letsencrypt.org/directory for all Non-Production sites.

The privateKeySecretRef has configuration for the private key name you prefer to use to store the ACME account private key. This can be anything you specify, for example letsencrypt-prod
```
spec:
 acme:
    # The ACME server URL
    server: "https://acme-v02.api.letsencrypt.org/directory"
    email: "infradev@darey.io"
    # Name of a secret used to store the ACME account private key
    privateKeySecretRef:
    name: "letsencrypt-prod"
```

section 3 – This section is part of the spec that configures solvers which determines the domain address that the issued certificate will be registered with. dns01 is one of the different challenges that cert-manager uses to verify domain ownership. Read more on DNS01 Challenge here. With the DNS01 configuration, you will need to specify the Route53 DNS Hosted Zone ID and region. Since we are using EKS in AWS, the IAM permission of the worker nodes will be used to access Route53. Therefore if appropriate permissions is not set for EKS worker nodes, it is possible that certificate challenge with Route53 will fail, hence certificates will not get issued.

The other possible option is the HTTP01 challenge, but we won’t be using that here.
```
solvers:
- selector:
    dnsZones:
    - "darey.io"
dns01:
    route53:
    region: "eu-west-2"
    hostedZoneID: "Z2CD4NTR2FDPZ"
```

With the ClusterIssuer properlu configured, it is now time to start getting certificates issued.

Lets see what that looks like in the next section.

## CONFIGURING INGRESS FOR TLS

To ensure that every created ingress also has TLS configured, we will need to update the ingress manifest with TLS specific configurations.
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
    kubernetes.io/ingress.class: nginx
  name: artifactory
spec:
  rules:
  - host: "tooling.artifactory.sandbox.svc.darey.io"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: artifactory
            port:
              number: 8082
  tls:
  - hosts:
    - "tooling.artifactory.sandbox.svc.darey.io"
    secretName: "tooling.artifactory.sandbox.svc.darey.io"
```

The most significat updates to the ingress definition is the annotations and tls sections.

Lets quickly talk about Annotations. Annotations are used similar to labels in kubernetes. They are ways to attach metadata to objects.

**Differences between Annotations and Labels**
Labels are used in conjunction with selectors to identify groups of related resources. Because selectors are used to query labels, this operation needs to be efficient. To ensure efficient queries, labels are constrained by RFC 1123. RFC 1123, among other constraints, restricts labels to a maximum 63 character length. Thus, labels should be used when you want Kubernetes to group a set of related resources.

Annotations are used for “non-identifying information” i.e., metadata that Kubernetes does not care about. As such, annotation keys and values have no constraints. Thus, if you want to add information for other humans about a given resource, then annotations are a better choice.

The Annotation added to the Ingress resource adds metadata to specify the issuer responsible for requesting certificates. The issuer here will be the same one we have created earlier with the nameletsencrypt-prod.
```
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
```

The other section is tls where the host name that will require https is specified. The secretName also holds the name of the secret that will be created which will store details of the certificate key-pair. i.e Private key and public certificate.
```
  tls:
  - hosts:
    - "tooling.artifactory.sandbox.svc.darey.io"
    secretName: "tooling.artifactory.sandbox.svc.darey.io"
```

Redeploying the newly updated ingress will go through the process as shown below.



Once deployed, you can run the following commands to see each resource at each phase.

* kubectl get certificaaterequest
* kubectl get order
* kubectl get challenge
* kubectl get certificate

At each stage you can run describe on each resource to get more information on what cert-manager is doing.

If all goes well, running ```kubectl get certificate ```,you should see an output like below.
***
NAME                                           READY                            SECRET                          AGE
tooling.artifactory.sandbox.svc.darey.io       True             tooling.artifactory.sandbox.svc.darey.io       108s
***

Notice the secret name there in the above output. Executing the command ```kubectl get secrettooling.artifactory.sandbox.svc.darey.io -o yaml ``` , you will see the data with encoded version of both the private key tls.key and the public certificate tls.crt. This is the actual certificate configuration that the ingress controller will use as part of Nginx configuration to terminate TLS/SSL on the ingress.

If you now head over to the browser, you should see the padlock sign without warnings of untrusted certificates.



Finally, one more task for you to do is to ensure that the LoadBalancer created for artifactory is destroyed. If you run a get service kubectl command like below;
```
kubectl get service -n tools
```
You will see that the load balancer is still there.

A task for you is to update the helm values file for artifactory, and ensure that the artifactory-artifactory-nginx service uses ClusterIP

Your final output should look like this.



Finally, update the ingress to use artifactory-artifactory-nginx as the backend service instead of using artifactory. Remember to update the port number as well.

If everything goes well, you will be prompted at login to set the BASE URL. It will pick up the new https address. Simply click next



Skip the proxy part of the setup.



Skip repositories creation because you will do this in the next poject.



Then complete the setup.

Congratulations! for completing Project 25
