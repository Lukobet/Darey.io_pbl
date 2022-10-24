# LOAD BALANCER SOLUTION WITH NGINX AND SSL/TLS
Task 1:
* Configure Nginx as a Load Balancer
Step 1: Launch an instance for the Nginx Load Balancer

![Screenshot from 2022-10-23 21-36-03](https://user-images.githubusercontent.com/110517150/197416989-b067a09b-a2fe-49bb-8346-67e364511cc2.png)

Step 2: Install and launch Nginx

![Screenshot from 2022-10-23 21-39-47](https://user-images.githubusercontent.com/110517150/197417179-a767da7f-f24d-45e2-979d-09407d908587.png)

Step 3: Update /etc/hosts file for local DNS with Web Servers’ names 

  
* Register a new domain name and configure secured connection using SSL/TLS certificates
Step 1: Register a new domain name with any registrar of your choice in any domain zone (e.g. .com, .net, .org, .edu, .info, .xyz or any other)

![Screenshot from 2022-10-23 22-16-18](https://user-images.githubusercontent.com/110517150/197418705-18b7bd1f-0d1a-4fe6-a116-ec13813f3321.png)

Step 2: Assign an Elastic IP to your Nginx LB server and associate your domain name with the IP address and Update A record in your registrar to point to Nginx LB using IP address

![Screenshot from 2022-10-23 22-27-33](https://user-images.githubusercontent.com/110517150/197419137-9fadf8a2-e649-4ff9-ba50-7dafc4e3616f.png)

![Screenshot from 2022-10-23 22-45-09](https://user-images.githubusercontent.com/110517150/197419721-f8c90e91-091e-43db-a10a-1b2398b64ae3.png)

*Difficulties encountered
my Toolingysf.cf is displaying
![Screenshot from 2022-10-24 22-07-08](https://user-images.githubusercontent.com/110517150/197629549-28390e49-987e-457f-96aa-cde40ddf7e50.png)
