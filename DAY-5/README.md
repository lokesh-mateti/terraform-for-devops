### **Day 5: Advanced Terraform Features - Modules, Workspaces, and Best Practices**

On the final day, we dive into **advanced Terraform concepts** such as **modules** for reusability, **workspaces** for managing environments, and **best practices** to follow in real-world scenarios. These topics help you write efficient, maintainable, and scalable infrastructure-as-code.

---

### **1. Terraform Modules**

#### **What Are Modules?**
- A **module** is a container for multiple Terraform resources that are used together.
- Modules allow you to organize and reuse infrastructure code.
- Every Terraform configuration file (`.tf`) is part of the **root module**.
- Modules can be:
  - **Local**: Defined in the same repository or directory.
  - **Remote**: Sourced from GitHub, Terraform Registry, or other repositories.

---

#### **Why Use Modules?**
- Promote code reuse and reduce duplication.
- Improve readability and maintainability.
- Enable separation of concerns.
- Facilitate collaboration by abstracting complex configurations.

---

#### **Creating a Module**

1. **Define Module Structure**
   - Example: `vpc-module`
     ```
     vpc-module/
     ├── main.tf        # Core resources
     ├── variables.tf   # Input variables
     ├── outputs.tf     # Output values
     └── README.md      # Documentation
     ```

2. **Write the Module**
   - `main.tf`:
     ```hcl
     resource "aws_vpc" "main" {
       cidr_block = var.cidr_block
       tags = {
         Name = var.name
       }
     }
     ```

   - `variables.tf`:
     ```hcl
     variable "cidr_block" {
       description = "CIDR block for the VPC"
       type        = string
     }

     variable "name" {
       description = "Name of the VPC"
       type        = string
     }
     ```

   - `outputs.tf`:
     ```hcl
     output "vpc_id" {
       value = aws_vpc.main.id
     }
     ```

3. **Use the Module**
   - In another Terraform configuration, call the module:
     ```hcl
     module "vpc" {
       source      = "./vpc-module"
       cidr_block  = "10.0.0.0/16"
       name        = "my-vpc"
     }

     output "vpc_id" {
       value = module.vpc.vpc_id
     }
     ```

---

#### **Using Remote Modules**
- Example of using a module from Terraform Registry:
  ```hcl
  module "s3_bucket" {
    source  = "terraform-aws-modules/s3-bucket/aws"
    version = "3.0.0"

    bucket = "my-unique-bucket-name"
    acl    = "private"
  }
  ```

---

### **2. Terraform Workspaces**

#### **What Are Workspaces?**
- Workspaces allow you to manage multiple environments (e.g., **dev**, **staging**, **prod**) within a single Terraform configuration.
- Each workspace has its own state file.

#### **Why Use Workspaces?**
- Simplifies managing different environments.
- Reduces the need for separate Terraform configurations.

---

#### **Commands for Workspaces**

1. **List Workspaces**
   - List available workspaces:
     ```bash
     terraform workspace list
     ```

2. **Create a Workspace**
   - Create a new workspace:
     ```bash
     terraform workspace new dev
     ```

3. **Switch Workspaces**
   - Switch to an existing workspace:
     ```bash
     terraform workspace select dev
     ```

4. **Show Current Workspace**
   - Display the active workspace:
     ```bash
     terraform workspace show
     ```

---

#### **Using Workspaces in Configuration**

- Reference the workspace name dynamically:
  ```hcl
  resource "aws_s3_bucket" "example" {
    bucket = "my-app-${terraform.workspace}"
    acl    = "private"
  }
  ```

---

#### **Workspace Limitations**
- Workspaces are not ideal for managing completely separate infrastructure.
- Use **separate backends** or **separate configurations** for isolated environments.

---

### **3. Best Practices**

#### **Organizing Terraform Code**
1. **Use Modules**:
   - Encapsulate reusable components (e.g., VPC, EC2, S3).
   - Keep modules in a separate repository or directory.

2. **Separate Environments**:
   - Use different directories or backends for `dev`, `staging`, and `prod`.

3. **Follow Naming Conventions**:
   - Use descriptive names for resources and modules.

---

#### **State File Management**
- Always secure state files (e.g., enable encryption in remote backends).
- Use remote backends with locking enabled (e.g., S3 with DynamoDB).

---

#### **Collaboration and Versioning**
- Use version control for Terraform configurations.
- Use a **Git branching strategy** (e.g., feature branches for changes).

---

#### **Variable Management**
1. **Avoid Hardcoding**:
   - Use variables for dynamic configurations.

2. **Protect Sensitive Values**:
   - Store secrets securely (e.g., use AWS Secrets Manager or HashiCorp Vault).
   - Mark variables as sensitive:
     ```hcl
     variable "password" {
       description = "Database password"
       type        = string
       sensitive   = true
     }
     ```

---

#### **Testing Infrastructure**
- Use tools like **Terratest** or **Checkov** for testing and validating infrastructure code.
- Manually review `terraform plan` before applying changes.

---

#### **Optimizing Performance**
- Enable resource targeting during testing:
  ```bash
  terraform apply -target=resource_type.resource_name
  ```

- Avoid unnecessary resource recreation by using `lifecycle` blocks:
  ```hcl
  resource "aws_instance" "example" {
    ami           = "ami-12345678"
    instance_type = "t2.micro"

    lifecycle {
      prevent_destroy = true
    }
  }
  ```

---

### **4. Practice Assignments**

1. **Create a Reusable Module**
   - Build a module for an AWS EC2 instance.
   - Add parameters for AMI ID, instance type, and tags.

2. **Implement Workspaces**
   - Set up `dev` and `prod` workspaces for an S3 bucket configuration.
   - Use the workspace name to distinguish bucket names.

3. **Organize a Multi-Environment Setup**
   - Separate configurations for `dev` and `prod` with different state backends.

4. **Secure State Files**
   - Configure an S3 backend with encryption and state locking.

---

### **Key Points to Remember**
- **Modules** improve reusability and maintainability.
- **Workspaces** help manage multiple environments but are not substitutes for isolation.
- Follow **best practices** for secure, scalable, and maintainable infrastructure.

