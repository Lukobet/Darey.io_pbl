# TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS
In previous Project 8 we introduced horizontal scalability concept, which allow us to add new Web Servers to our Tooling Website and you have successfully deployed a set up with 2 Web Servers and also a Load Balancer to distribute traffic between them. If it is just two or three servers – it is not a big deal to configure them manually. Imagine that you would need to repeat the same task over and over again adding dozens or even hundreds of servers.

DevOps is about Agility, and speedy release of software and web solutions. One of the ways to guarantee fast and repeatable deployments is Automation of routine tasks.

In this project we are going to start automating part of our routine tasks with a free and open source automation server – Jenkins. It is one of the most popular CI/CD tools, it was created by a former Sun Microsystems developer Kohsuke Kawaguchi and the project originally had a named “Hudson”.

According to Circle CI, Continuous integration (CI) is a software development strategy that increases the speed of development while ensuring the quality of the code that teams deploy. Developers continually commit code in small increments (at least daily, or even several times a day), which is then automatically built and tested before it is merged with the shared repository.

In our project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub https://github.com/<yourname>/tooling will be automatically be updated to the Tooling Website.

Side Self Study
Read about Continuous Integration, Continuous Delivery and Continuous Deployment.

Task
Enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server.

Here is how your updated architecture will look like upon competition of this project:

![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/da5e6e16-0bec-41b7-b347-7e48bc6fd429)


Task 1
# Install and Configure Jenkins server
* Create an Instance for Jenkins alongside the other running servers

![Screenshot from 2022-10-18 23-09-22](https://user-images.githubusercontent.com/110517150/196554413-091b6c87-c51d-4e10-99be-65ab7a9d57b7.png)

* Jenkins installation
Install JDK (since Jenkins is a Java-based application)
```
sudo apt update
sudo apt install default-jdk-headless
```

Install Jenkins
```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
```

Make sure Jenkins is up and running

```
sudo systemctl status jenkins
```

By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group

Perform initial Jenkins setup.

From your browser access http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080

You will be prompted to provide a default admin password


![Screenshot from 2022-10-18 23-44-11](https://user-images.githubusercontent.com/110517150/196559004-67aec374-ee48-44e8-87ed-d9644aa4eaa8.png)

Retrieving password for Jenkins

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

![Screenshot from 2022-10-18 23-51-14](https://user-images.githubusercontent.com/110517150/196560100-0734f9bf-0181-4863-bb67-102198293a1c.png)

![Screenshot from 2022-10-18 23-54-11](https://user-images.githubusercontent.com/110517150/196567373-de38a577-4b8c-4ba8-b7a4-b2dbc9a95483.png)

# Task 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks
Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks
In this part, you will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will will be triggered by GitHub webhooks and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.

Enable webhooks in your GitHub repository settings
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/cccb3c26-2b2a-45ca-8fce-f887dadea0eb)

Go to Jenkins web console, click “New Item” and create a “Freestyle project”
        

To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself

 

In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

 ![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/09543da8-5333-4545-aeee-4483582ed0a8)


Save the configuration and let us try to run the build. For now we can only do it manually.
Click “Build Now” button, if you have configured everything correctly, the build will be successfull and you will see it under #1

 ![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/b379e0a1-4e5d-4dce-9c36-a978634d5c27)


You can open the build and check in “Console Output” if it has run successfully.

If so – congratulations! You have just made your very first Jenkins build!

But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.

Click “Configure” your job/project and add these two configurations

Configure triggering the job from GitHub webhook:

 ![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/6395cd9f-69e5-46a5-a35f-463af9f19d48)

Configure “Post-build Actions” to archive all the files – files resulted from a build are called “artifacts”.

 ![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/c8862209-37e9-4774-8d4f-e2c606bef181)


Now, go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.

You will see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on Jenkins server.

 

You have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

By default, the artifacts are stored on Jenkins server locally

ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/

# RESULTS
![Screenshot from 2022-10-19 00-22-34](https://user-images.githubusercontent.com/110517150/196563102-49f9d758-1556-4c3c-a34d-4963fced8373.png)
Project built successfully
![Screenshot from 2022-10-19 00-23-53](https://user-images.githubusercontent.com/110517150/196563195-9af80b09-d2e4-4c85-aa72-18e2d0463ea6.png)

Second builld after putting a write up in the README file
![Screenshot from 2022-10-19 00-30-06](https://user-images.githubusercontent.com/110517150/196563943-507b3ebd-d63d-4d22-8b18-9ad434d53cb9.png)

Checking the Jenkins file stored locally through the Terminal

![Screenshot from 2023-01-12 22-49-21](https://user-images.githubusercontent.com/110517150/212188661-33619643-840a-48e2-a887-e5f773f6f8c2.png)


# Task 3 Configure Jenkins to copy files to NFS server via SSH
 Step 3 – Configure Jenkins to copy files to NFS server via SSH
Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.

Jenkins is a highly extendable application and there are 1400+ plugins available. We will need a plugin that is called “Publish Over SSH”.

Install “Publish Over SSH” plugin.
On main dashboard select “Manage Jenkins” and choose “Manage Plugins” menu item.

On “Available” tab search for “Publish Over SSH” plugin and install it 
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/68a3a144-1ab6-4dde-80ca-706fe70cf004)

Configure the job/project to copy artifacts over to NFS server.
On main dashboard select “Manage Jenkins” and choose “Configure System” menu item.

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)
Arbitrary name
Hostname – can be private IP address of your NFS server
Username – ec2-user (since NFS server is based on EC2 with RHEL 8)
Remote directory – /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server
Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.

 ![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/349d7a01-8469-416e-b7e7-330811a41207)
   

Save the configuration, open your Jenkins job/project configuration page and add another one “Post-build Action”
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/0337c3ae-60dc-4ddd-9352-57f9067adbd7)

 

Configure it to send all files produced by the build into our previously define remote directory. In our case we want to copy all files and directories – so we use **.
If you want to apply some particular pattern to define which files to send – use this syntax.
![image](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/7b6d9ef2-7223-41a5-8811-9091612fab96)

 

Save this configuration and go ahead, change something in README.MD file in your GitHub Tooling repository.

Webhook will trigger a new job and in the “Console Output” of the job you will find something like this:

SSH: Transferred 25 file(s)
Finished: SUCCESS
To make sure that the files in /mnt/apps have been updated – connect via SSH/Putty to your NFS server and check README.MD file

cat /mnt/apps/README.md
If you see the changes you had previously made in your GitHub – the job works as expected.


I encountered a difficulty connecting to the NFS Server via ssh but was able to resovle it 

![Screenshot from 2022-10-22 20-24-48](https://user-images.githubusercontent.com/110517150/197358948-d5ad5aad-95fe-48b9-b460-4ede42586027.png)

solved by viewing the content of my .pem key
![Screenshot from 2022-10-22 20-43-15](https://user-images.githubusercontent.com/110517150/197359514-9f711a1d-efb4-4863-b570-3b75da8bbd55.png)

Files transfered successfully
![Screenshot from 2022-10-22 20-52-52](https://user-images.githubusercontent.com/110517150/197359878-51ceaea6-7434-4a03-90bc-7b79a342dde8.png)
![Screenshot from 2022-10-22 20-55-06](https://user-images.githubusercontent.com/110517150/197359924-38f53997-8e58-49ea-a601-342028ece02d.png)




# JENKINS INTEGRATON WITH GITLAB CI (Watch week4 on AFRICASTARTUP)
