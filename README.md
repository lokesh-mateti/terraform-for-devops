
### Directory Structure
Your project directory should look like this:
```
.
├── main.tf
├── outputs.tf
├── provider.tf
├── variables.tf
└── terraform.tfvars
```

### Step 1: Define Variables

#### `variables.tf`
Define your variables in `variables.tf`:
```hcl
variable "aws_region" {
  description = "The AWS region to deploy the resources in"
  type        = string
}

variable "instance_type" {
  description = "Type of EC2 instance"
  type        = string
}

variable "instance_name" {
  description = "Name of the EC2 instance"
  type        = string
}

variable "tags" {
  description = "A map of tags to assign to the resources"
  type        = map(string)
}
```

### Step 2: Provide Variable Values

#### `terraform.tfvars`
Provide values for these variables in `terraform.tfvars`:
```hcl
aws_region    = "us-east-1"
instance_type = "t3.micro"
instance_name = "example-instance"
tags = {
  Environment = "development"
  Project     = "example-project"
}
```

### Step 3: Configure Provider

#### `provider.tf`
Configure the AWS provider in `provider.tf`:
```hcl
provider "aws" {
  region = var.aws_region
}
```

### Step 4: Create Resources

#### `main.tf`
Create resources using the variables and locals in `main.tf`:
```hcl
locals {
  full_instance_name = "${var.instance_name}-${var.tags["Environment"]}"
}

resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0" # Example AMI ID, ensure it is valid in your region
  instance_type = var.instance_type

  tags = merge(
    var.tags,
    {
      "Name" = local.full_instance_name
    }
  )
}

output "instance_id" {
  description = "The ID of the EC2 instance"
  value       = aws_instance.example.id
}

output "instance_name" {
  description = "The full name of the EC2 instance"
  value       = local.full_instance_name
}

output "aws_region" {
  description = "The AWS region in use"
  value       = var.aws_region
}

output "instance_type" {
  description = "The instance type used for the EC2 instance"
  value       = var.instance_type
}

output "tags" {
  description = "Tags assigned to the resources"
  value       = var.tags
}
```

### Step 5: Initialize and Apply

Run Terraform commands to initialize, plan, and apply the configuration. The output variables will be printed after applying the configuration.

```sh
terraform init
terraform plan -var-file="terraform.tfvars"
terraform apply -var-file="terraform.tfvars"
```

### Output

After running `terraform apply`, you will see the output variables printed in the terminal. For example:

```plaintext
Outputs:

aws_region = "us-east-1"
instance_id = "i-0abcd1234efgh5678"
instance_name = "example-instance-development"
instance_type = "t3.micro"
tags = {
  "Environment" = "development"
  "Project" = "example-project"
  "Name" = "example-instance-development"
}
```


- **Variables**: Defined in `variables.tf` and values provided in `terraform.tfvars`.
- **Provider**: Configured in `provider.tf` using the `aws_region` variable.
- **Resource**: An EC2 instance is created in `main.tf` using variables and local values.
- **Locals**: Used to create a composite instance name combining `instance_name` and the `Environment` tag.
- **Outputs**: Print various values, including the AWS region, instance ID, instance name, instance type, and tags.

By following this example, you get a complete cycle of defining, using, and outputting Terraform variables, along with utilizing local values.
