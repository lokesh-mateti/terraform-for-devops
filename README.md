### **Day 1: Introduction to Terraform**

---

#### **1. What is Terraform?**
Terraform is an open-source Infrastructure as Code (IaC) tool created by HashiCorp. It enables you to define and provision infrastructure using a declarative configuration language. With Terraform, you can manage various infrastructure components, such as servers, storage, networking, and more, across multiple cloud providers and on-premises environments.

**Key Features of Terraform:**
- **Cloud Agnostic:** Supports AWS, Azure, GCP, Kubernetes, and more.
- **Declarative Approach:** You specify *what* you want, and Terraform determines *how* to achieve it.
- **State Management:** Tracks resource states in a state file for efficient updates.
- **Idempotent:** Ensures consistent results, even if applied multiple times.

**Use Cases:**
- Automating cloud resource provisioning.
- Managing hybrid or multi-cloud environments.
- Building reusable infrastructure modules.

---

#### **2. Setting Up the Environment**

**Prerequisites:**
- A computer with a supported operating system (Windows, macOS, Linux).
- Internet access.
- A cloud provider account (e.g., AWS Free Tier for practice).

**Installation Steps:**
1. **Download Terraform:**
   - Go to the [official Terraform website](https://www.terraform.io/downloads).
   - Download the appropriate version for your operating system.

2. **Install Terraform:**
   - **Windows:**
     - Extract the downloaded `.zip` file.
     - Move the `terraform.exe` to a directory in your PATH (e.g., `C:\Windows\System32`).
   - **macOS/Linux:**
     - Extract the `.zip` file.
     - Move the `terraform` binary to `/usr/local/bin/` or another directory in your PATH.

3. **Verify Installation:**
   Run the following command in your terminal or command prompt:
   ```bash
   terraform version
   ```
   This should display the installed version of Terraform.

---

#### **3. Understanding Basic Concepts**

- **Providers:**
  Providers are plugins that enable Terraform to interact with APIs of external services. Examples include AWS, Azure, GCP, and Kubernetes.
  - Example:
    ```hcl
    provider "aws" {
      region = "us-west-2"
    }
    ```

- **Resources:**
  A resource is a component of your infrastructure, such as an EC2 instance or an S3 bucket.
  - Example:
    ```hcl
    resource "aws_instance" "example" {
      ami           = "ami-123456"
      instance_type = "t2.micro"
    }
    ```

- **Declarative Syntax:**
  Terraform uses HashiCorp Configuration Language (HCL). You write *what* you want in `.tf` files, and Terraform figures out *how* to create or modify resources.

---

#### **4. Hands-On: Write Your First Terraform Configuration**

**Goal:** Launch an EC2 instance in AWS.

**Steps:**
1. **Set Up a Directory:**
   - Create a directory for your project:
     ```bash
     mkdir terraform-day1
     cd terraform-day1
     ```

2. **Create a Configuration File:**
   - Create a file named `main.tf` with the following content:
     ```hcl
     provider "aws" {
       region = "us-west-2"
     }

     resource "aws_instance" "example" {
       ami           = "ami-0c55b159cbfafe1f0" # Amazon Linux 2
       instance_type = "t2.micro"

       tags = {
         Name = "TerraformExample"
       }
     }
     ```

3. **Initialize Terraform:**
   - Run the following command to download the provider plugins:
     ```bash
     terraform init
     ```

4. **Preview Changes:**
   - Use the `terraform plan` command to see what Terraform will create:
     ```bash
     terraform plan
     ```

5. **Apply the Configuration:**
   - Apply the configuration to create the EC2 instance:
     ```bash
     terraform apply
     ```
   - Type `yes` when prompted.

6. **Verify the Resource:**
   - Log in to the AWS Management Console and navigate to the EC2 Dashboard to verify the instance.

7. **Clean Up (Optional):**
   - Destroy the created resources to avoid incurring costs:
     ```bash
     terraform destroy
     ```
   - Type `yes` when prompted.

---

#### **5. Key Commands Introduced**
- `terraform init`: Initializes the working directory and downloads provider plugins.
- `terraform plan`: Previews changes without applying them.
- `terraform apply`: Applies the changes and provisions resources.
- `terraform destroy`: Destroys all resources defined in the configuration.

---

#### **6. Assignment for Learners**
- Modify the `main.tf` file to launch an EC2 instance in a different region.
- Add a new tag to the instance (e.g., `Environment = "Development"`).

---
