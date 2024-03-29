## AUTOMATE INFRASTRUCTURE WITH IAC USING TERRAFORM. PART 3 – REFACTORING
### REFACTORING USING MODULES
Break down your Terraform codes to have all resources in their respective modules. Combine resources of a similar type into directories within a ‘modules’ directory, for example, like this:
```
- modules
  - ALB: For Apllication Load balancer and similar resources
  - EFS: For Elastic file system resources
  - RDS: For Databases resources
  - Autoscaling: For Autosacling and launch template resources
  - compute: For EC2 and rlated resources
  - VPC: For VPC and netowrking resources such as subnets, roles, e.t.c.
  - security: for creating security group resources
```
![Screenshot from 2023-08-28 14-36-31](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/89580073-3597-4fdf-9c78-8141515c4719)

Each module shall contain outputs.tf,main.tf and variable.tf
![Screenshot from 2023-08-28 15-51-43](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/affccdfa-98e9-4e89-a4af-c7c07104915c)
![Screenshot from 2023-08-28 15-51-34](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/4ac4372e-7ab5-4bd2-abc7-fb8f74e84281)
![Screenshot from 2023-08-28 15-51-11](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/3b83515f-e6e0-47b7-9b1b-3fda72d64f92)

terraform plan successfully done

![Screenshot from 2023-08-28 16-29-15](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/687dc703-549f-487e-ab0b-97a44975f3ea)
terrform apply done
resources created




![Screenshot from 2023-08-28 16-47-45](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/a3f996dd-c526-42eb-a183-9cba23ea0cf4)
![Screenshot from 2023-08-28 16-47-16](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/3f9140d8-a41c-489e-aa31-b11bea3a07ce)
![Screenshot from 2023-08-28 16-46-47](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/fcd0800b-368a-473a-9947-e4007e1f9937)
![Screenshot from 2023-08-28 16-45-44](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/bfeee219-702b-43ff-bd08-c86fe9006bcb)
![Screenshot from 2023-08-28 16-44-54](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/8443afeb-b3e9-4ef2-a1ed-f4d819b9a5e6)
![Screenshot from 2023-08-28 16-44-28](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/904f205d-95be-4c56-8535-c35f966f945f)
![Screenshot from 2023-08-28 16-44-02](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/84bc1ba7-4106-4392-a51f-1950be347cc1)
![Screenshot from 2023-08-28 16-43-25](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/c1b05666-3731-435b-a06e-2dbd8ab34a5d)
![Screenshot from 2023-08-28 16-59-36](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/a3710a74-a47c-4fa1-ad90-ea793d31a889)
![Screenshot from 2023-08-28 16-59-10](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/b06fd605-149e-4ca9-be9e-6ac50af63047)
![Screenshot from 2023-08-28 16-58-26](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/3f258a46-5329-4b48-ae1e-39db75b31f76)

#### Introducing Backend on S3
Each Terraform configuration can specify a backend, which defines where and how operations are performed, where state snapshots are stored, etc

So far, we have been using the default **backend(terraform.tfstate),** which is the local backend – it requires no configuration, and the states file is stored locally. This mode can be suitable for learning purposes, but it is not a robust solution, so it is better to store it in some more reliable and durable storage.

The second problem with storing this file locally is that, in a team of multiple DevOps engineers, other engineers will not have access to a state file stored locally on your computer.

To solve this, we will need to configure a backend where the state file can be accessed remotely by other DevOps team members. There are plenty of different standard backends supported by Terraform that you can choose from. Since we are already using AWS – we can choose an S3 bucket as a backend.

Another useful option that is supported by S3 backend is State Locking – it is used to lock your state for all operations that could write state. This prevents others from acquiring the lock and potentially corrupting your state. State Locking feature for S3 backend is optional and requires another AWS service – DynamoDB.

Let us configure it!

Here is our plan to Re-initialize Terraform to use S3 backend:

* Add S3 and DynamoDB resource blocks before deleting the local state file


Create a file and name it **backend.tf**. Add the below code and replace the name of the S3 bucket you created in Project-16.
  ```
terraform {
  backend "s3" {
    bucket         = "tsn-dev-terraform-bucket"
    key            = "global/s3/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```
![Screenshot from 2023-08-28 10-56-25](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/1146032c-9101-4d92-b911-84eb3be3ed4c)

* Update terraform block to introduce backend and locking

* Re-initialize terraform

![Screenshot from 2023-08-28 17-06-04](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/d2ea9dca-b7b1-4516-aa94-aa081c094836)

![Screenshot from 2023-08-28 17-08-10](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/a5b05ec4-522e-4c2d-acdb-c09fcc8cc6a3)



* Delete the local tfstate file and check the one in S3 bucket
* Add outputs
* terraform apply
