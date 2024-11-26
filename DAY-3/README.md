### **Day 3: Terraform Provisioners, Modules, and Resource Dependencies**

On Day 3, we focus on advanced concepts like **provisioners**, **modules**, and **resource dependencies**, enabling you to build more complex, reusable, and robust Terraform configurations.

---

### **1. Provisioners**

#### **What Are Provisioners?**
- Provisioners allow you to execute scripts or commands on a resource after it's created or before it's destroyed.
- Typically used for configuration management tasks like installing software, copying files, or running custom scripts.

#### **Types of Provisioners**
1. **File Provisioner:**
   - Used to copy files from the local machine to a remote resource.
2. **Remote-Exec Provisioner:**
   - Executes commands on the remote resource.
3. **Local-Exec Provisioner:**
   - Executes commands on the local machine running Terraform.

#### **When to Use Provisioners**
- Use provisioners sparingly since they introduce tight coupling between Terraform and the provisioned infrastructure.
- Prefer using configuration management tools like Ansible, Chef, or Puppet when possible.

---

#### **Examples of Provisioners**

1. **File Provisioner**
   - Copies a file from the local system to the instance:
     ```hcl
     resource "aws_instance" "example" {
       ami           = "ami-0c55b159cbfafe1f0"
       instance_type = "t2.micro"

       provisioner "file" {
         source      = "app-config.json"
         destination = "/tmp/app-config.json"
       }
     }
     ```

2. **Remote-Exec Provisioner**
   - Runs commands on the remote instance using SSH:
     ```hcl
     resource "aws_instance" "example" {
       ami           = "ami-0c55b159cbfafe1f0"
       instance_type = "t2.micro"

       provisioner "remote-exec" {
         inline = [
           "sudo apt update",
           "sudo apt install -y nginx",
         ]
       }

       connection {
         type        = "ssh"
         user        = "ubuntu"
         private_key = file("~/.ssh/id_rsa")
         host        = self.public_ip
       }
     }
     ```

3. **Local-Exec Provisioner**
   - Runs a command on the local machine:
     ```hcl
     resource "aws_instance" "example" {
       ami           = "ami-0c55b159cbfafe1f0"
       instance_type = "t2.micro"

       provisioner "local-exec" {
         command = "echo ${self.public_ip} >> ip_addresses.txt"
       }
     }
     ```

---

### **2. Modules**

#### **What Are Modules?**
- A **module** is a container for multiple resources that are used together.
- Helps organize and reuse configurations across projects.

#### **Why Use Modules?**
- Simplifies large configurations by splitting them into smaller, manageable pieces.
- Promotes reusability and standardization.
- Easier to share and maintain infrastructure code.

#### **Structure of a Module**
- A module typically consists of three files:
  1. `main.tf`: Contains the primary resources.
  2. `variables.tf`: Defines input variables.
  3. `outputs.tf`: Exposes output values.

#### **Using a Module**
- A module is used by calling it in another Terraform configuration:
  ```hcl
  module "ec2_instance" {
    source       = "./modules/ec2"
    instance_type = "t2.micro"
    ami           = "ami-0c55b159cbfafe1f0"
    region        = "us-west-2"
  }
  ```

---

#### **Creating a Custom Module**

1. **Create the Module Directory:**
   - Directory structure:
     ```
     .
     ├── main.tf
     ├── variables.tf
     ├── outputs.tf
     └── modules/
         └── ec2/
             ├── main.tf
             ├── variables.tf
             ├── outputs.tf
     ```

2. **Define the Module (`modules/ec2`):**
   - `main.tf`:
     ```hcl
     resource "aws_instance" "example" {
       ami           = var.ami
       instance_type = var.instance_type

       tags = {
         Name = "ModuleExample"
       }
     }
     ```

   - `variables.tf`:
     ```hcl
     variable "ami" {
       description = "AMI ID for the instance"
     }

     variable "instance_type" {
       description = "Type of instance"
       default     = "t2.micro"
     }
     ```

   - `outputs.tf`:
     ```hcl
     output "instance_id" {
       value = aws_instance.example.id
     }
     ```

3. **Use the Module in the Root Configuration:**
   - `main.tf`:
     ```hcl
     module "ec2_instance" {
       source        = "./modules/ec2"
       ami           = "ami-0c55b159cbfafe1f0"
       instance_type = "t2.micro"
     }
     ```

4. **Run Terraform Commands:**
   ```bash
   terraform init
   terraform apply
   ```

---

### **3. Resource Dependencies**

#### **What Are Dependencies?**
- Terraform automatically builds a dependency graph to determine the order of resource creation, modification, or destruction.
- Dependencies are inferred from:
  - Explicit resource references.
  - Variables and outputs.

#### **Explicit Dependencies**
- Use the `depends_on` argument to define explicit dependencies.
- Example:
  ```hcl
  resource "aws_instance" "example" {
    ami           = "ami-0c55b159cbfafe1f0"
    instance_type = "t2.micro"
  }

  resource "aws_eip" "example" {
    instance = aws_instance.example.id

    depends_on = [aws_instance.example]
  }
  ```

---

#### **Examples of Dependencies**

1. **Basic Dependency Example**
   - Ensure an EBS volume is attached only after the instance is created:
     ```hcl
     resource "aws_instance" "example" {
       ami           = "ami-0c55b159cbfafe1f0"
       instance_type = "t2.micro"
     }

     resource "aws_volume_attachment" "example" {
       instance_id = aws_instance.example.id
       volume_id   = aws_ebs_volume.example.id
     }
     ```

2. **Using `depends_on`:**
   - Explicitly define a dependency:
     ```hcl
     resource "null_resource" "wait_for_instance" {
       depends_on = [aws_instance.example]
     }
     ```

---

### **4. Practice Assignments**

1. **Provisioners:**
   - Create an EC2 instance and:
     - Use a file provisioner to copy a configuration file.
     - Use a remote-exec provisioner to install and start a web server.
     - Use a local-exec provisioner to save the instance's IP address locally.

2. **Modules:**
   - Create a reusable module to deploy an EC2 instance with the following parameters:
     - AMI ID.
     - Instance type.
     - Tags.

3. **Dependencies:**
   - Create a configuration where:
     - An EC2 instance is created.
     - An Elastic IP is associated with the instance.
     - Explicitly define dependencies.

---

### **5. Key Commands Introduced**
- `terraform plan`: Shows the dependency graph.
- `terraform init`: Ensures modules and plugins are downloaded.
- `terraform apply`: Creates and manages resources.

