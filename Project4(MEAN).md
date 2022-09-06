# IMPLEMENTATION OF A SIMPLE BOOK REGISTER WEB FORM

MEAN Stack is a combination of following components:
* MongoDB (Document database) – Stores and allows to retrieve data.
* Express (Back-end application framework) – Makes requests to Database for Reads and Writes.
* Angular (Front-end application framework) – Handles Client and Server Requests
* Node.js (JavaScript runtime environment) – Accepts requests and displays results to end user

Task 1 : Installation of Nodejs, Mongodb and 
1. Ec2 instance connect
![Screenshot from 2022-09-05 22-50-11](https://user-images.githubusercontent.com/110517150/188515321-2d06130d-2e3f-4ee5-8cc8-bafd142ce590.png)

Difficulty experienced while installing Mongodb
![Screenshot from 2022-09-05 23-08-06](https://user-images.githubusercontent.com/110517150/188516271-512999b6-f08a-469d-b6f3-433fc2f2573d.png)

i had to terminate the Instance running because it is Ubuntu 22.04 LTS and the Mongodb version cant work on it.
I created another instance with Ubuntu 20.04 image and ran the following commands to install Mongodb:
 * wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -
 * echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
 * sudo apt-get update
 * sudo apt-get install -y mongodb-org   and it worked
 ![Screenshot from 2022-09-05 23-55-31](https://user-images.githubusercontent.com/110517150/188518770-3aed2603-95d1-4af7-8177-dde7717f1f62.png)
