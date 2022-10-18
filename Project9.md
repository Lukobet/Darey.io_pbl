# TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS


Task 1
# Install and Configure Jenkins server
* Create an Instance for Jenkins alongside the other running servers

![Screenshot from 2022-10-18 23-09-22](https://user-images.githubusercontent.com/110517150/196554413-091b6c87-c51d-4e10-99be-65ab7a9d57b7.png)

* Jenkins installation
![Screenshot from 2022-10-18 23-44-11](https://user-images.githubusercontent.com/110517150/196559004-67aec374-ee48-44e8-87ed-d9644aa4eaa8.png)

Retrieving password for Jenkins

`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

![Screenshot from 2022-10-18 23-51-14](https://user-images.githubusercontent.com/110517150/196560088-ac005bcb-30b4-4e67-97f1-4d4f062394ac.png)

![Screenshot from 2022-10-18 23-51-14](https://user-images.githubusercontent.com/110517150/196560100-0734f9bf-0181-4863-bb67-102198293a1c.png)

# Task 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks
![Screenshot from 2022-10-19 00-22-34](https://user-images.githubusercontent.com/110517150/196563102-49f9d758-1556-4c3c-a34d-4963fced8373.png)
Project built successfully
![Screenshot from 2022-10-19 00-23-53](https://user-images.githubusercontent.com/110517150/196563195-9af80b09-d2e4-4c85-aa72-18e2d0463ea6.png)

Second builld after putting a write up in the README file
![Screenshot from 2022-10-19 00-30-06](https://user-images.githubusercontent.com/110517150/196563943-507b3ebd-d63d-4d22-8b18-9ad434d53cb9.png)


# Task 3 Configure Jenkins to copy files to NFS server via SSH

