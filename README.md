# AWS Custom VPC Deployment using Terraform (Infrastructure as Code)

A hands-on cloud engineering portfolio project demonstrating how to design, automate, and deploy a secure, custom Virtual Private Cloud (VPC) network architecture on Amazon Web Services (AWS) using HashiCorp Terraform and Git Bash CLI.

---

## 📌 Architecture Overview

Instead of manually configuring networking resources in the AWS Management Console, this project uses **Infrastructure as Code (IaC)** to provision a complete, reproducible cloud networking foundation.

### Provisioned Resources:
* **Custom VPC (`portfolio-tf-vpc`):** Isolated network environment with CIDR block `10.0.0.0/16`[cite: 7, 8].
* **Public Subnet (`portfolio-tf-public-subnet`):** Configured to automatically assign public IP addresses (`map_public_ip_on_launch = true`)[cite: 5, 7].
* **Internet Gateway (`portfolio-tf-igw`):** Provides bidirectional communication between instances in the public subnet and the internet[cite: 7].
* **Public Route Table (`portfolio-tf-public-rt`):** Routes all outbound internet traffic (`0.0.0.0/0`) to the Internet Gateway[cite: 7].
* **Route Table Association (`portfolio-tf-public-assoc`):** Binds the public subnet to the custom route table[cite: 7, 8].

---

## 🛠️ Tech Stack & Prerequisites

* **Cloud Provider:** Amazon Web Services (AWS)
* **IaC Tool:** HashiCorp Terraform
* **Command Line Interface:** Git Bash (Windows) / AWS CLI[cite: 4]
* **Language:** HashiCorp Configuration Language (HCL)

### Prerequisites:
1. Active **AWS Account** with configured IAM credentials (`aws configure`).
2. **Terraform executable (`terraform.exe`)** placed in the project working directory[cite: 7, 8].

---

## 📄 Terraform Configuration (`main.tf`)

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

# Custom VPC
resource "aws_vpc" "portfolio_vpc" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = {
    Name = "portfolio-tf-vpc"
  }
}

# Public Subnet
resource "aws_subnet" "public_subnet" {
  vpc_id                  = aws_vpc.portfolio_vpc.id
  cidr_block              = "10.0.1.0/24"
  map_public_ip_on_launch = true

  tags = {
    Name = "portfolio-tf-public-subnet"
  }
}

# Internet Gateway
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.portfolio_vpc.id

  tags = {
    Name = "portfolio-tf-igw"
  }
}

# Public Route Table
resource "aws_route_table" "public_rt" {
  vpc_id = aws_vpc.portfolio_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }

  tags = {
    Name = "portfolio-tf-public-rt"
  }
}

# Route Table Association
resource "aws_route_table_association" "public_assoc" {
  subnet_id      = aws_subnet.public_subnet.id
  route_table_id = aws_route_table.public_rt.id
}

## 📸 Proof of Execution
<img width="1920" height="1080" alt="Screenshot (3558)" src="https://github.com/user-attachments/assets/efd92e62-8811-4818-9f65-1ade7c6957d8" />
<img width="1920" height="1080" alt="Screenshot (3569)" src="https://github.com/user-attachments/assets/01e880b8-856f-4824-ab1c-422a941d9619" />
<img width="1920" height="1080" alt="Screenshot (3571)" src="https://github.com/user-attachments/assets/7a816482-b6dc-42bd-9d1c-febbe9d03b18" />



