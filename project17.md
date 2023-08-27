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

Update the variables.tf to declare the variable tags used in this format
 ```
variable "tags" {
  description = "A mapping of tags to assign to all resources."
  type        = map(string)
  default     = {}
}
```
![Screenshot from 2023-08-27 05-19-15](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/2bf1139b-78d1-4d65-b40e-0f11b3b12d77)

Update the terraform.tfvars to declare the variable tags used in this format;
 ```
tags = {
  Enviroment      = "production" 
  Owner-Email     = "dare@darey.io"
  Managed-By      = "Terraform"
  Billing-Account = "1234567890"
}
```
![Screenshot from 2023-08-27 05-31-29](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/6e9fc0a2-ee53-4f19-b49b-48b93a0b2e51)

### Internet Gateways & format() function

Create an Internet Gateway in a separate Terraform file internet_gateway.tf

 ```
resource "aws_internet_gateway" "ig" {
  vpc_id = aws_vpc.main.id
  tags = merge(
    var.tags,
    {
      Name = format("%s-%s-%s!", var.name, aws_vpc.main.id, "IG")
    },
  )
}
```
![Screenshot from 2023-08-27 05-47-49](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/36c7746b-8d57-48be-9294-686077fd62bc)
#### NAT Gateways
Create 1 NAT Gateways and 1 Elastic IP (EIP) addresses

Now use similar approach to create the NAT Gateways in a new file called natgateway.tf.

```
resource "aws_eip" "nat_eip" {
  vpc        = true
  depends_on = [aws_internet_gateway.ig]

  tags = merge(
    var.tags,
    {
      Name = format("%s-EIP", var.name)
    },
  )
}

resource "aws_nat_gateway" "nat" {
  allocation_id = aws_eip.nat_eip.id
  subnet_id     = element(aws_subnet.public.*.id, 0)
  depends_on    = [aws_internet_gateway.ig]

  tags = merge(
    var.tags,
    {
      Name = format("%s-Nat", var.name)
    },
  )
}

```
![Screenshot from 2023-08-27 05-44-28](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/01d25b0e-d4a0-4355-86b8-d4354745934a)

I keep getting this error
![Screenshot from 2023-08-27 06-04-36](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/05e62a80-a0ae-4948-a83b-89d06c11a8ad)
But was solved by changing my region to us.east.1

![Screenshot from 2023-08-27 12-39-39](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/c9bb334e-5b91-4ae8-b439-708bf919d8fc)

### AWS ROUTES
* Create a file called route_tables.tf and use it to create routes for both public and private subnets, create the below resources. Ensure they are properly tagged.

aws_route_table
aws_route
aws_route_table_association

 ```
# create private route table
resource "aws_route_table" "private-rtb" {
  vpc_id = aws_vpc.main.id

  tags = merge(
    var.tags,
    {
      Name = format("%s-Private-Route-Table", var.name)
    },
  )
}

# associate all private subnets to the private route table
resource "aws_route_table_association" "private-subnets-assoc" {
  count          = length(aws_subnet.private[*].id)
  subnet_id      = element(aws_subnet.private[*].id, count.index)
  route_table_id = aws_route_table.private-rtb.id
}

# create route table for the public subnets
resource "aws_route_table" "public-rtb" {
  vpc_id = aws_vpc.main.id

  tags = merge(
    var.tags,
    {
      Name = format("%s-Public-Route-Table", var.name)
    },
  )
}

# create route for the public route table and attach the internet gateway
resource "aws_route" "public-rtb-route" {
  route_table_id         = aws_route_table.public-rtb.id
  destination_cidr_block = "0.0.0.0/0"
  gateway_id             = aws_internet_gateway.ig.id
}

# associate all public subnets to the public route table
resource "aws_route_table_association" "public-subnets-assoc" {
  count          = length(aws_subnet.public[*].id)
  subnet_id      = element(aws_subnet.public[*].id, count.index)
  route_table_id = aws_route_table.public-rtb.id
}

```
