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

#### ** We need to have AWS CLI and Terraform to be installed and configured, details given below.

#### **Step 1: Download and Install AWS CLI**
AWS CLI (Command Line Interface) can be installed on various operating systems. Follow the appropriate steps for your system:

#### **For Windows**
1. **Download the AWS CLI MSI installer** from the [AWS CLI official download page](https://aws.amazon.com/cli/).  
   Alternatively, you can directly access the installer for Windows [here](https://awscli.amazonaws.com/AWSCLIV2.msi).
2. Run the MSI installer and follow the on-screen instructions.

#### **For macOS**
1. Open a terminal.
2. Install using Homebrew:
   ```bash
   brew install awscli
   ```
3. Verify the installation:
   ```bash
   aws --version
   ```

#### **For Linux**
1. Use the following commands:
   ```bash
   curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
   unzip awscliv2.zip
   sudo ./aws/install
   ```
2. Verify installation:
   ```bash
   aws --version
   ```

---

### **Step 2: Set Environment Path**
After installation, ensure the AWS CLI executable is in your system's `PATH`.

#### **For Windows**
1. Right-click on **This PC** or **My Computer** and select **Properties**.
2. Go to **Advanced System Settings > Environment Variables**.
3. Under **System Variables**, find **Path**, and click **Edit**.
4. Add the path to your AWS CLI installation, typically:
   ```plaintext
   C:\Program Files\Amazon\AWSCLI\bin
   ```
5. Click **OK** to save the changes.

#### **For macOS/Linux**
The installer typically adds AWS CLI to the system path. You can confirm by running:
```bash
echo $PATH
```
If it’s not in the path, manually add the installation directory to your shell configuration file (`~/.bashrc`, `~/.zshrc`, etc.) like so:
```bash
export PATH=$PATH:/usr/local/bin/aws
```
Then reload the configuration:
```bash
source ~/.bashrc
```

---

### **Step 3: Configure AWS CLI**
To set up AWS CLI with your credentials:

1. Run the following command:
   ```bash
   aws configure
   ```
2. Enter the required details:
   - **AWS Access Key ID**: Get it from your AWS IAM account.
   - **AWS Secret Access Key**: Also from AWS IAM.
   - **Default Region**: For example, `us-east-1`.
   - **Default Output Format**: Choose `json`, `text`, or `table`.

---

### **Step 4: Verify AWS CLI Configuration**
Test the configuration by running a simple AWS CLI command, such as listing all S3 buckets:
```bash
aws s3 ls
```

---

### **Resources and Official Site**
- **AWS CLI Documentation**: [https://docs.aws.amazon.com/cli/latest/userguide/](https://docs.aws.amazon.com/cli/latest/userguide/)
- **AWS CLI Downloads**: [https://aws.amazon.com/cli/](https://aws.amazon.com/cli/)

**Prerequisites:**
- A computer with a supported operating system (Windows, macOS, Linux).
- Internet access.
- A cloud provider account (e.g., AWS Free Tier for practice).

**Terraform Installation Steps:**
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
