Here are some **examples for Day 2** to help you practice and deepen your understanding of Terraform's state, variables, and outputs.

---

### **Practice Examples**

---

#### **1. Example: Managing Terraform State with a Remote Backend**

**Objective:** Store your Terraform state in an S3 bucket and enable state locking with DynamoDB.

**Steps:**
1. **Create an S3 Bucket and DynamoDB Table** manually or using the AWS Management Console:
   - S3 bucket name: `my-terraform-state-bucket`.
   - DynamoDB table name: `terraform-lock`.

2. **Write the Terraform Configuration:**
   - `main.tf`:
     ```hcl
     terraform {
       backend "s3" {
         bucket         = "my-terraform-state-bucket"
         key            = "terraform/state"
         region         = "us-west-2"
         dynamodb_table = "terraform-lock"
       }
     }

     provider "aws" {
       region = "us-west-2"
     }

     resource "aws_instance" "example" {
       ami           = "ami-0c55b159cbfafe1f0"
       instance_type = "t2.micro"
     }
     ```

3. **Initialize the Backend:**
   ```bash
   terraform init
   ```

4. **Apply the Configuration:**
   ```bash
   terraform apply
   ```

**What to Learn:**
- How the `terraform.tfstate` file moves to the S3 bucket.
- The DynamoDB table ensures no concurrent modifications (state locking).

---

#### **2. Example: Using Variables for Reusability**

**Objective:** Use variables to make the configuration dynamic, allowing easy updates without modifying the code.

**Steps:**
1. **Define Variables:** Create a `variables.tf` file:
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
     description = "Tags for the EC2 instance"
     type        = map(string)
     default     = {
       Name        = "TerraformPractice"
       Environment = "Development"
     }
   }
   ```

2. **Update the `main.tf` File:**
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

3. **Apply the Configuration:**
   ```bash
   terraform apply
   ```

4. **Practice Questions:**
   - Change the default `region` to `us-east-1` and observe the behavior.
   - Add a new tag (e.g., `Owner = "YourName"`) to the `tags` variable.

---

#### **3. Example: Outputs for Resource Details**

**Objective:** Display critical resource details after deployment.

**Steps:**
1. **Add Outputs to `main.tf`:**
   ```hcl
   output "instance_id" {
     description = "The ID of the EC2 instance"
     value       = aws_instance.example.id
   }

   output "instance_public_ip" {
     description = "The public IP address of the EC2 instance"
     value       = aws_instance.example.public_ip
   }
   ```

2. **Apply the Configuration:**
   ```bash
   terraform apply
   ```

3. **Observe the Outputs:**
   After Terraform completes, note the instance ID and public IP in the terminal.

4. **Practice Questions:**
   - Add an output for the private IP address of the instance.
   - Modify the `instance_type` variable to launch a larger instance type and observe the changes in outputs.

---

#### **4. Example: Combining Variables and Outputs**

**Objective:** Parameterize the AMI ID and generate outputs for all relevant instance details.

**Steps:**
1. **Define Variables:**
   - Add the following to `variables.tf`:
     ```hcl
     variable "ami_id" {
       description = "AMI ID for the instance"
       default     = "ami-0c55b159cbfafe1f0"
     }
     ```

2. **Update the `main.tf` File:**
   ```hcl
   provider "aws" {
     region = var.region
   }

   resource "aws_instance" "example" {
     ami           = var.ami_id
     instance_type = var.instance_type

     tags = var.tags
   }

   output "instance_details" {
     description = "All details about the instance"
     value = {
       instance_id = aws_instance.example.id
       public_ip   = aws_instance.example.public_ip
       private_ip  = aws_instance.example.private_ip
       type        = aws_instance.example.instance_type
     }
   }
   ```

3. **Apply the Configuration:**
   ```bash
   terraform apply
   ```

4. **Observe the Outputs:**
   Terraform will display a structured output with all instance details.

5. **Practice Questions:**
   - Pass a custom `ami_id` using the `-var` flag.
   - Modify the output to include the instance's availability zone.

---

#### **5. Example: Using `.tfvars` Files**

**Objective:** Use a `.tfvars` file to simplify variable management.

**Steps:**
1. **Create a Variable File (`terraform.tfvars`):**
   ```hcl
   region        = "us-east-1"
   instance_type = "t2.medium"
   tags = {
     Name        = "PracticeWithTFVars"
     Environment = "Testing"
   }
   ```

2. **Apply the Configuration:**
   Terraform automatically picks up the `terraform.tfvars` file during execution:
   ```bash
   terraform apply
   ```

3. **Practice Questions:**
   - Create an additional `.tfvars` file (e.g., `staging.tfvars`) with different values.
   - Apply the new file using the `-var-file` flag:
     ```bash
     terraform apply -var-file="staging.tfvars"
     ```

---

### **Challenges**

1. **Challenge 1: Create Multiple EC2 Instances**
   - Use the `count` feature to provision multiple EC2 instances.
   - Define a variable for the number of instances:
     ```hcl
     variable "instance_count" {
       description = "Number of EC2 instances"
       default     = 3
     }
     ```

   - Update the resource block:
     ```hcl
     resource "aws_instance" "example" {
       count         = var.instance_count
       ami           = "ami-0c55b159cbfafe1f0"
       instance_type = var.instance_type

       tags = {
         Name        = "TerraformInstance-${count.index}"
         Environment = "Test"
       }
     }
     ```

2. **Challenge 2: Dynamically Name Resources**
   - Use a combination of variables and the `format` function to create meaningful resource names.

3. **Challenge 3: Explore Remote Backends**
   - If using another cloud provider, configure its backend (e.g., Azure Blob Storage or Google Cloud Storage) for state management.

