### **Day 2: Core Terraform Concepts**

On Day 2, we dive deeper into the core concepts of Terraform, focusing on **state management**, **variables**, and **outputs**. By the end of the day, you'll have a better understanding of how Terraform manages infrastructure and how to make configurations more dynamic and reusable.

---

### **1. Terraform State**

#### **What is Terraform State?**
- Terraform uses a **state file** to store information about your infrastructure resources.
- The state file acts as a single source of truth that tracks:
  - Existing resources created by Terraform.
  - Dependencies between resources.
  - Metadata required to manage infrastructure changes.

#### **Why is State Important?**
- Without a state file, Terraform cannot determine the current status of resources.
- Terraform compares the state file with the desired configuration to determine changes needed.

#### **Types of Backends for State Management**
1. **Local Backend (Default):**
   - State is stored locally on your machine (e.g., in `terraform.tfstate`).
   - Suitable for small projects or testing environments.
2. **Remote Backend:**
   - State is stored in a remote location like AWS S3, Azure Blob Storage, or HashiCorp Consul.
   - Essential for collaboration and ensuring state file safety.
   - Provides features like locking (e.g., using DynamoDB with S3).

#### **Best Practices for State Management**
- Use remote backends for team collaboration.
- Secure the state file (e.g., encrypt it at rest and in transit).
- Avoid manual edits to the state file.

#### **Hands-On: Configure a Remote Backend**
1. Modify the `main.tf` file to include a remote backend configuration:
   ```hcl
   terraform {
     backend "s3" {
       bucket         = "my-terraform-state"
       key            = "terraform/state"
       region         = "us-west-2"
       dynamodb_table = "terraform-lock"
     }
   }
   ```
2. Initialize the backend:
   ```bash
   terraform init
   ```

---

### **2. Variables**

#### **Why Use Variables?**
- Variables make configurations reusable and dynamic.
- They allow you to parameterize your Terraform code and avoid hardcoding values.

#### **Defining Variables**
- Variables are defined in a `.tf` file using the `variable` block:
  ```hcl
  variable "region" {
    description = "AWS region"
    default     = "us-west-2"
  }
  ```

#### **Using Variables**
- Reference variables using the `${}` syntax or directly with `var.`:
  ```hcl
  provider "aws" {
    region = var.region
  }
  ```

#### **Passing Variable Values**
1. **Default Values:** Set in the `variable` block.
2. **Command-Line Flags:**
   ```bash
   terraform apply -var="region=us-east-1"
   ```
3. **Variable Files:** Use `.tfvars` files to define variables:
   - Example: `terraform.tfvars`
     ```hcl
     region = "us-east-1"
     ```
   - Apply with:
     ```bash
     terraform apply -var-file="terraform.tfvars"
     ```

#### **Hands-On: Refactor Configuration with Variables**
1. Define variables for region, instance type, and tags in `variables.tf`:
   ```hcl
   variable "region" {
     description = "AWS region"
     default     = "us-west-2"
   }

   variable "instance_type" {
     description = "Type of EC2 instance"
     default     = "t2.micro"
   }

   variable "tags" {
     description = "Tags for the instance"
     type        = map(string)
     default     = {
       Name        = "TerraformExample"
       Environment = "Development"
     }
   }
   ```
2. Update `main.tf` to use these variables:
   ```hcl
   provider "aws" {
     region = var.region
   }

   resource "aws_instance" "example" {
     ami           = "ami-0c55b159cbfafe1f0"
     instance_type = var.instance_type

     tags = var.tags
   }
   ```

3. Apply the configuration:
   ```bash
   terraform apply
   ```

---

### **3. Outputs**

#### **What Are Outputs?**
- Outputs allow Terraform to display key information after resource creation.
- Useful for sharing data between configurations or with other systems (e.g., CI/CD pipelines).

#### **Defining Outputs**
- Use the `output` block in a `.tf` file:
  ```hcl
  output "instance_id" {
    description = "ID of the EC2 instance"
    value       = aws_instance.example.id
  }

  output "public_ip" {
    description = "Public IP of the EC2 instance"
    value       = aws_instance.example.public_ip
  }
  ```

#### **Accessing Outputs**
- After applying the configuration, outputs are displayed in the terminal:
  ```bash
  terraform apply
  ```
  Example output:
  ```
  instance_id = "i-0123456789abcdef0"
  public_ip = "54.123.45.67"
  ```

- Outputs are also stored in the state file for reuse.

---

### **4. Hands-On: Combine Variables and Outputs**

1. Update the configuration to include outputs:
   - Add the following to `main.tf`:
     ```hcl
     output "instance_id" {
       description = "The ID of the EC2 instance"
       value       = aws_instance.example.id
     }

     output "instance_public_ip" {
       description = "The public IP address of the instance"
       value       = aws_instance.example.public_ip
     }
     ```

2. Apply the configuration:
   ```bash
   terraform apply
   ```

3. Observe the outputs in the terminal.

---

### **5. Key Commands Introduced**
- `terraform init`: Initializes the backend and providers.
- `terraform apply`: Applies the configuration, including variable and output updates.
- `terraform destroy`: Destroys resources and clears outputs.
- `terraform fmt`: Formats `.tf` files to improve readability.

---

### **6. Assignment for Learners**
1. Add new variables for AMI ID and create an additional EC2 instance using these variables.
2. Define outputs for the instance's private IP address and availability zone.
3. Configure a remote backend if using AWS (use S3 and DynamoDB).

