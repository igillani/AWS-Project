# P4: Scalable Cloud Infrastructure with AWS

## Project Overview
P4 demonstrates the creation of a scalable and secure cloud infrastructure using AWS. This project involves deploying a multi-tier architecture that integrates VPC, EC2, RDS, and security features. The infrastructure is designed for a PHP web application connected to a MySQL database hosted on RDS. This project highlights essential cloud engineering practices, including network segmentation, secure access control, and database management.

## Features
- **Virtual Private Cloud (VPC):** Custom VPC with public and private subnets for enhanced security.
- **Internet Gateway:** Enables internet access for resources in public subnets.
- **EC2 Instances:** Used for hosting the PHP web application.
- **RDS Instance:** MySQL database for persistent data storage.
- **Security Groups:** Fine-grained access control for EC2 and RDS.
- **Monitoring:** CloudWatch integration for monitoring resource health.

## Components
### 1. VPC Configuration
- **CIDR Block:** 10.0.0.0/16
- **Subnets:**
  - Public Subnet 1: 10.0.1.0/24
  - Private Subnet 1: 10.0.2.0/24
- **Internet Gateway:** Attached to the VPC for public internet access.

### 2. EC2 Instance
- **AMI:** Amazon Linux 2
- **Instance Type:** t2.micro (free tier eligible)
- **Purpose:** Hosts the PHP web application.
- **Key Pair:** Used for secure SSH access.

### 3. RDS MySQL Database
- **Instance Type:** db.t2.micro (free tier eligible)
- **Engine:** MySQL
- **Database Name:** `testdb`
- **Tables:** `users` table for storing user data.

### 4. Security Groups
- **EC2 Security Group:**
  - Allow SSH (Port 22) from your IP.
  - Allow HTTP (Port 80) from anywhere.
- **RDS Security Group:**
  - Allow MySQL (Port 3306) from the EC2 Security Group.

### 5. Application
- A simple PHP application hosted on the EC2 instance to interact with the RDS database for CRUD operations.

## Deployment Steps
### Prerequisites
1. AWS Account with IAM user permissions.
2. AWS CLI installed and configured.
3. Key pair for EC2 instance SSH access.

### Step-by-Step Instructions
1. **VPC Setup:**
   - Create a VPC with the specified CIDR block.
   - Add public and private subnets.
   - Attach an Internet Gateway to the VPC.

2. **Security Groups:**
   - Create security groups for EC2 and RDS with appropriate rules.

3. **RDS Instance:**
   - Launch an RDS MySQL instance in the private subnet.
   - Note the database endpoint for later use.

4. **EC2 Instance:**
   - Launch an EC2 instance in the public subnet.
   - Install PHP, Apache, and MySQL client.

5. **Application Deployment:**
   - Upload the PHP application to the EC2 instance.
   - Configure the application to connect to the RDS database.

6. **Test the Setup:**
   - Access the PHP application via the EC2 public IP.
   - Verify CRUD operations on the database.

### Monitoring and Metrics
- Enable CloudWatch for EC2 and RDS to monitor resource utilization and health.

## Challenges and Solutions
- **Connecting EC2 to RDS:** Used the correct security group rules to allow EC2 to access the RDS instance.
- **Database Connectivity Issues:** Verified the RDS endpoint and database credentials.

## Future Enhancements
- Automate the infrastructure setup using CloudFormation or Terraform.
- Add load balancing and auto-scaling for the EC2 layer.
- Integrate a CI/CD pipeline for automated deployments.

## Directory Structure
```
AWS-Project/
├── VPC/
├── EC2/
├── RDS/
├── Application/
└── README.md
```

## References
- [AWS Documentation](https://aws.amazon.com/documentation/)
- [PHP Manual](https://www.php.net/manual/en/)

## Author
**Imran Gillani**
