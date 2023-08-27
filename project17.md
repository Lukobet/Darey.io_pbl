# AUTOMATE INFRASTRUCTURE WITH IAC USING TERRAFORM. PART 2

Networking
Private subnets & best practices
Create 4 private subnets keeping in mind following principles:

* Make sure you use variables or length() function to determine the number of AZs
* Use variables and cidrsubnet() function to allocate vpc_cidr for subnets
* Keep variables and resources in separate files for better code structure and readability
* Tags all the resources you have created so far. Explore how to use format() and count functions to automatically tag subnets with its respective number.
![Screenshot from 2023-08-27 04-41-23](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/4ba55cb0-91ed-462e-8847-83fd63ad994a)
##### Tagging
Tagging is a straightforward, but a very powerful concept that helps you manage your resources much more efficiently:

Resources are much better organized in ‘virtual’ groups
They can be easily filtered and searched from console or programmatically
Billing team can easily generate reports and determine how much each part of infrastructure costs how much (by department, by type, by environment, etc.)
You can easily determine resources that are not being used and take actions accordingly
If there are different teams in the organisation using the same account, tagging can help differentiate who owns which resources.
Tag all you resources using the format below
```
tags = merge(
    var.tags,
    {
      Name = "Name of the resource"
    },
  )

```
![Screenshot from 2023-08-27 05-14-31](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/12851419-9e17-4b4f-ba10-ebf8224345c2)

Update the variables.tf to declare the variable tags used in the format above;
 ```
variable "tags" {
  description = "A mapping of tags to assign to all resources."
  type        = map(string)
  default     = {}
}
```
![Screenshot from 2023-08-27 05-19-15](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/2bf1139b-78d1-4d65-b40e-0f11b3b12d77)

