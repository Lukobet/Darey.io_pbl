#  Load Balancer Solution With Apache
 
 A Load Balancer (LB) distributes clients’ requests among underlying Web Servers and makes sure that the load is distributed in an optimal way. 

I will be enhancing tooling Website solution by adding a Load Balancer to distribute traffic between Web Servers and allow users to access our website using a single URL.
Prerequisites for this projects are the installation and configuration of the following:

1. Two RHEL8 Web Servers
2. One MySQL DB Server (based on Ubuntu 20.04)
3. One RHEL8 NFS server

![Screenshot from 2022-09-28 16-51-54](https://user-images.githubusercontent.com/110517150/192826692-0c879604-1fdd-45da-a856-c1697220bc61.png)

![Screenshot from 2022-09-28 17-11-26](https://user-images.githubusercontent.com/110517150/192831239-baf3c253-b46d-4200-897d-8da799230af5.png)

Configuring Load Balancer

![Screenshot from 2022-10-05 13-28-06](https://user-images.githubusercontent.com/110517150/194060388-50d5c4bd-a905-4b86-b96d-59316faecfec.png)

Verifying that the configuration works using the Load Bancer Public IP Address/index.php
![Screenshot from 2022-10-05 13-33-50](https://user-images.githubusercontent.com/110517150/194061262-ccb712b2-af2a-4b7f-9378-4a2deae96eae.png)

unmounting the /var/log/httpd/ using 
*  `sudo tail -f /var/log/httpd/access_log` *

![Screenshot from 2022-10-05 13-37-52](https://user-images.githubusercontent.com/110517150/194062550-beca459d-0248-4f08-954d-3618d5efb513.png)

The two Webservers receiving requests
![Screenshot from 2022-10-05 13-44-32](https://user-images.githubusercontent.com/110517150/194063221-f7fe5970-7777-4381-852d-b49dc5121391.png)

![Screenshot from 2022-10-05 13-45-09](https://user-images.githubusercontent.com/110517150/194063327-003d71e1-f3e6-44a0-9cae-4fef22054e48.png)

*Configure Local DNS Names Resolution
![Screenshot from 2022-10-05 13-59-40](https://user-images.githubusercontent.com/110517150/194066377-c26aab31-090c-49b2-a09b-1f52255c4cb8.png)

![Screenshot from 2022-10-05 14-09-31](https://user-images.githubusercontent.com/110517150/194068210-7f014b5f-228d-46b7-8f27-434c5eef4a64.png)

Trying to curl my Web Servers from Load Balancer locally using `curl http://Web1` or `curl http://Web2` it brough the html version

![Screenshot from 2022-10-05 13-56-18](https://user-images.githubusercontent.com/110517150/194065529-98d8ec39-fd4b-4ce8-8b76-c9fb3bd97263.png)
![Screenshot from 2022-10-05 13-56-39](https://user-images.githubusercontent.com/110517150/194065621-9744d89d-089e-4418-85fc-1ff995fd4d5c.png)

![Screenshot from 2022-10-05 13-56-39](https://user-images.githubusercontent.com/110517150/194065641-7e7bfe4d-69e7-4a81-93f4-93d966ead9c5.png)
