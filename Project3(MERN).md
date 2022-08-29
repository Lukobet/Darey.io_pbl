## SIMPLE TO-DO APPLICATION ON MERN WEB STACK

MERN Web stack consists of following components:

MongoDB: A document-based, No-SQL database used to store application data in a form of documents.

ExpressJS: A server side Web Application framework for Node.js.

ReactJS: A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.

Node.js: A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.

In this project i am to deploy a simple To-Do application that creates To-Do lists by:

* CONFIGURNG BACKEND
1) Updating and ugrading Ubuntu using: sudo apt update && sudo apt upgrade

![Screenshot from 2022-08-29 17-30-57](https://user-images.githubusercontent.com/110517150/187282687-cc946c93-e56d-4eae-8af6-21e58fb07dc5.png)

2) Package.json file created.

![Screenshot from 2022-08-29 17-45-57](https://user-images.githubusercontent.com/110517150/187282525-a772101f-bf78-4dc1-a945-07e6c91b03e4.png)

3) Installing Expressj

![Screenshot from 2022-08-29 18-28-36](https://user-images.githubusercontent.com/110517150/187282438-b7db0265-67b3-41e5-acd3-e7180029074b.png)

4) Creation of the Todo list

![Screenshot from 2022-08-29 19-17-44](https://user-images.githubusercontent.com/110517150/187282179-e0a01416-26e9-4ecd-bff8-9399c6a18eae.png)


5) Setting up MongoDB Database

![Screenshot from 2022-08-29 19-28-36](https://user-images.githubusercontent.com/110517150/187282062-ca0e9bf9-4225-4f75-a55f-6701d71c06fc.png)

6) Problems encountered

![Screenshot from 2022-08-29 20-11-55](https://user-images.githubusercontent.com/110517150/187281867-6cf3c51e-225a-43ed-9115-403731fd1680.png)

7) problem solved by using the command: lsof -i tcp:5000 & kill -9 and node code

![Screenshot from 2022-08-29 20-20-51](https://user-images.githubusercontent.com/110517150/187281562-10bb42b0-cf0e-4ba4-8d7b-012f5a22180b.png)

8) creating a post request for API

![Screenshot from 2022-08-29 21-15-41](https://user-images.githubusercontent.com/110517150/187293224-019ea35a-bf98-43d0-9dc7-e3c38677f624.png)

9) Getting a request from API

![Screenshot from 2022-08-29 21-27-31](https://user-images.githubusercontent.com/110517150/187292286-08303b74-217f-49b2-a517-6bb89e3a1d81.png)

* CONFIGURING FRONTEND
1) installation of React App

![Screenshot from 2022-08-29 21-40-30](https://user-images.githubusercontent.com/110517150/187294931-03145d84-ec4a-4651-92da-f0b7348e0ce4.png)

2) Problems encountered after creating proxy in package.json

![Screenshot from 2022-08-29 22-06-12](https://user-images.githubusercontent.com/110517150/187299267-e6ffdb68-61c4-4793-b5bb-dc0df7084a5f.png)

 problem resolved by rearranging the details in package.json
 
 ![Screenshot from 2022-08-29 23-30-54](https://user-images.githubusercontent.com/110517150/187310429-6c640e5c-02f9-4fe9-8263-c36c2eedb564.png)


![Screenshot from 2022-08-29 23-41-25](https://user-images.githubusercontent.com/110517150/187311484-ef892942-4466-4b9c-bb6b-24a820ff0f42.png)

3) Creating the React Components
