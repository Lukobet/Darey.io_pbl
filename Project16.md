
# AUTOMATE INFRASTRUCTURE WITH IAC USING TERRAFORM PART 1


After you have built AWS infrastructure for 2 websites manually, it is time to automate the process using Terraform.
##### Prerequisites before you begin writing Terraform code

Create an IAM user, name it terraform (ensure that the user has only programatic access to your AWS account) and grant this user AdministratorAccess permissions. ![Screenshot from 2023-08-24 20-28-17](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/ce7a24d2-d7ab-4633-8733-0fb9e1430507)


Copy the secret access key and access key ID. Save them in a notepad temporarily **====DONE**

Configure programmatic access from your workstation to connect to AWS using the access keys copied above and a Python SDK (boto3). You must have Python 3.6 or higher on your workstation.


For easier authentication configuration – use AWS CLI with aws configure command.**===DONE**

* Create an S3 bucket to store Terraform state file. You can name it something like <yourname>-dev-terraform-bucket (Note: S3 bucket names must be unique unique within a region partition, you can read about S3 bucken naming in this article).![Screenshot from 2023-08-25 17-11-45](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/af1b2e0a-7671-4384-941b-4cf62e08bfde)


 
When you have configured authentication and installed boto3, make sure you can programmatically access your AWS account by running following commands in >python:
```
import boto3
s3 = boto3.resource('s3')
for bucket in s3.buckets.all():
    print(bucket.name)
```

You shall see your previously created S3 bucket name – <yourname>-dev-terraform-bucket
![Screenshot from 2023-08-25 17-20-37](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/3a7ae430-ef3f-4f6b-8576-4851f6fa29fc)
The secrets of writing quality Terraform code
The secret recipe of a successful Terraform projects consists of:

Your understanding of your goal (desired AWS infrastructure end state)
Your knowledge of the IaC technology used (in this case – Terraform)
Your ability to effectively use up to date Terraform documentation here
As you go along completing this project, you will get familiar with Terraform-specific terminology, such as:

* **Attribute**: In Terraform's configuration language: a named piece of data that belongs to some kind of object. The value of an attribute can be referenced in expressions using a dot-separated notation, like aws_instance.example.id.

Terraform resources and data sources make all of their arguments available as readable attributes, and also typically export additional read-only attributes.


* **Resource:** Also "infrastructure resource".

In Terraform's configuration language: A block that describes one or more infrastructure objects. Resources can be things like virtual networks, compute instances, or higher-level components such as DNS records.

In other Terraform-related contexts: An infrastructure object of a type that could be managed by Terraform.

A resource block in a configuration instructs Terraform to manage the described resource — during a run, Terraform will create a matching real infrastructure object if one doesn't already exist, and will modify the existing object if it doesn't match the desired configuration. Terraform uses state to keep track of which real infrastructure objects correspond to resources in a configuration.

Terraform uses cloud provider APIs to create, edit, and destroy resources. Terraform providers are responsible for defining resource types and mapping transitions in a resource's state to a specific series of API calls that will carry out the necessary changes.


* **Interpolations**: Using a special placeholder to insert a computed value into a string.

Terraform's configuration language supports interpolation in strings using ${<EXPRESSION>} placeholders. For example:

"Hello, ${var.name}!"

Prior to Terraform 0.12, interpolation was the only way to use non-literal expressions in Terraform configurations; in 0.12 and later, expressions can be used independently.

Interpolation is a very common feature in programming languages; for example, Ruby uses #{<EXPRESSION>} placeholders in double-quoted strings, and JavaScript (ES6 and up) uses ${<EXPRESSION>} placeholders in backtick-quoted strings
* **Argument** : In Terraform's configuration language: a syntax construct that assigns a value to a name. Arguments have the form <IDENTIFIER> = <EXPRESSION>, and they appear within blocks.

Most of a Terraform configuration consists of using arguments to configure Terraform resources. Each resource type defines the arguments its resources can use, the allowed values for each argument, and which arguments are required or optional. Information about a given resource type can be found in the docs for that resource's provider.
* **Providers**: Terraform relies on plugins called providers to interact with cloud providers, SaaS providers, and other APIs.

Terraform configurations must declare which providers they require so that Terraform can install and use them. Additionally, some providers require configuration (like endpoint URLs or cloud regions) before they can be used.


* **Provisioners** : You can use provisioners to model specific actions on the local machine or on a remote machine in order to prepare servers or other infrastructure objects for service.


* **Input Variables**: Variables
Also "input variables".

In general-purpose programming, a variable is a symbolic name associated with a value.

In Terraform, "variables" almost always refers to input variables, which are key/value pairs used in a run. Terraform modules can declare variables to allow customization. For child modules, the parent module provides a value when calling the module; for the root module, values are provided at run time.

Terraform Cloud lets you set values for root input variables in a workspace, so all collaborators can use the same values. Variable values marked as "sensitive" become unreadable in the UI and API, and all variable values are protected by Vault.
* **Output Variables** : Also "outputs".

Data exported by a Terraform module, which can be displayed to a user and/or programmatically used by other Terraform code.
* **Module:** Also "Terraform module".

A self-contained collection of Terraform configurations that manages a collection of related infrastructure resources.

Other Terraform configurations can call a module, which tells Terraform to manage any resources described by that module.

Modules define input variables (which the calling module can set values for) and output values (which the calling module can reference in expressions).
* **Data Source**: A resource-like object that can be configured in Terraform's configuration language.

Unlike resources, data sources do not create or manage infrastructure. Instead, they return information about some kind of external object in the form of readable attributes. This allows a Terraform configuration to make use of information defined outside of Terraform, or defined by another separate Terraform configuration.

Data sources are implemented by providers
* **Local Values**: Local values assign a name to an expression, that can then be used multiple times within a module.

Comparing modules to functions in a traditional programming language, if variables are analogous to function arguments and outputs are analogous to function return values then local values are comparable to a function's local variables.

This page assumes you're already familiar with the configuration syntax.

Examples
Local values are defined in locals blocks:
```
# Ids for multiple sets of EC2 instances, merged together
locals {
  instance_ids = "${concat(aws_instance.blue.*.id, aws_instance.green.*.id)}"
}

# A computed default name prefix
locals {
  default_name_prefix = "${var.project_name}-web"
  name_prefix         = "${var.name_prefix != "" ? var.name_prefix : local.default_name_prefix}"
}

# Local values can be interpolated elsewhere using the "local." prefix.
resource "aws_s3_bucket" "files" {
  bucket = "${local.name_prefix}-files"
  # ...
}

```

* **Backend**: The part of Terraform's core that determines how Terraform stores state and performs operations (like plan, apply, import, etc.). Terraform has multiple backends to choose from, which can be configured in a variety of ways. Backends are not plugins, so it is not possible to install additional backends.

In a general computer science sense, a backend is any lower-level implementation that enables a higher-level feature. But in the context of Terraform, "backend" always means the built-in code that handles state and operations.


Make sure you understand them and know when to use each of them.

Another concept you must know is data type. This is a general programing concept, it refers to how data represented in a programming language and defines how a compiler or interpreter can use the data. Common data types are:Integer,Float,String,Boolean, etc.
Best practices

