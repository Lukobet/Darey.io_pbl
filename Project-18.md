## AUTOMATE INFRASTRUCTURE WITH IAC USING TERRAFORM. PART 3 – REFACTORING
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
#Note: The bucket name may not work for you since buckets are unique globally in AWS, so you must give it a unique name.
resource "aws_s3_bucket" "terraform_state" {
  bucket = "dev-terraform-bucket"
  #Enable versioning so we can see the full revision history of our state files
  versioning {
    enabled = true
  }
  #Enable server-side encryption by default
  server_side_encryption_configuration {
    rule {
      apply_server_side_encryption_by_default {
        sse_algorithm = "AES256"
      }
    }
  }
}
```
![Screenshot from 2023-08-28 10-56-25](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/1146032c-9101-4d92-b911-84eb3be3ed4c)



* Update terraform block to introduce backend and locking
* Re-initialize terraform
* Delete the local tfstate file and check the one in S3 bucket
* Add outputs
* terraform apply
