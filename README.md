# AWS-Project# Documentation: P4 Scalable Cloud Infrastructure Project

## Overview
The P4 project focused on building a scalable and highly available infrastructure using AWS services. The setup included essential components such as VPC, subnets, an Application Load Balancer (ALB), Auto Scaling Groups (ASG), a relational database (RDS), and security groups to ensure security and availability. Additionally, a CloudFormation template was created to automate the deployment of this infrastructure.

---

## Components

### 1. **Networking**
- **VPC**: A virtual private cloud (`10.0.0.0/16`) to host all resources.
- **Subnets**:
  - Public Subnet 1: `10.0.1.0/24` in `us-east-2a`.
  - Public Subnet 2: `10.0.3.0/24` in `us-east-2c`.
  - Private Subnet 1: '10.0.2.0/24' in 'us-east-2a'.
- **Internet Gateway**: Enabled internet access for resources in public subnets.

### 2. **Security**
- **ALB Security Group**:
  - Allowed inbound traffic on port `80` (HTTP) from anywhere (`0.0.0.0/0`).
  - Allowed outbound traffic to any destination.
- **EC2 Security Group**:
  - Allowed inbound traffic on:
    - Port `22` (SSH) from anywhere.
    - Port `80` (HTTP) from the ALB Security Group.
  - Allowed outbound traffic to any destination.
- **RDS Security Group**:
  - Allowed inbound traffic on port `3306` (MySQL) from the EC2 Security Group.

### 3. **Compute Resources**
- **Auto Scaling Group**:
  - Managed EC2 instances to ensure scalability and availability.
  - Scaling policy:
    - Minimum instances: 1
    - Maximum instances: 3
    - Desired instances: 1
  - Health checks: EC2 instance status and ALB health checks.
- **Launch Template**:
  - Configured EC2 instances with:
    - Instance Type: `t2.micro`.
    - Amazon Linux 2 AMI.
    - Associated security group and key pair for SSH access.

### 4. **Application Load Balancer (ALB)**
- Internet-facing ALB to distribute traffic to the EC2 instances.
- Configured with a Target Group for health checks and traffic routing.
- Health Check Path: `/` (Default root path).

### 5. **Database**
- **Amazon RDS**:
  - MySQL database hosted on RDS for persistent storage.
  - Configured with a private subnet for enhanced security.
  - Connected to the application running on EC2.
  - Security configuration:
    - Allowed access only from the EC2 Security Group.
  - Example Database: `my_test_db` with a `users` table.

---

## Automation

### **CloudFormation Template**
A CloudFormation template was generated and customized to include the following resources:
- VPC and Subnets.
- Internet Gateway and its attachment to the VPC.
- ALB and Target Group.
- Auto Scaling Group and Launch Template.
- Security Groups for ALB, EC2, and RDS.
- Amazon RDS instance.

This template was saved as `p4-cloudformation.yaml` and can be used to recreate the infrastructure.

---

## Testing and Validation
1. **ALB Health Checks**:
   - Verified that the ALB correctly marked EC2 instances as healthy.
   - Simulated instance failures to ensure automatic recovery.

2. **Application Availability**:
   - Tested application accessibility via the ALB DNS name.
   - Ensured load balancing between multiple instances.

3. **Database Connection**:
   - Verified EC2 instances could connect to the RDS database.
   - Tested database queries to ensure data persistence and accessibility.

4. **CloudWatch Alarms**:
   - Configured alarms for monitoring metrics such as unhealthy hosts and CPU utilization.

---

## Cleanup Process
To avoid incurring additional costs, all resources were deleted:
1. Deleted the Auto Scaling Group.
2. Deleted the ALB and its Target Group.
3. Terminated all EC2 instances.
4. Deleted the RDS instance and its associated subnet group.
5. Removed Security Groups, Subnets, and the VPC.
6. Deleted the Launch Template.

---

## Lessons Learned
- **Automation**: CloudFormation simplifies the deployment and management of AWS resources.
- **High Availability**: Configuring an ALB and ASG ensures application availability and scalability.
- **Database Management**: Using RDS simplifies database maintenance and improves security.
- **Monitoring**: CloudWatch metrics and alarms are crucial for proactive resource management.

---

## Next Steps
- Enhance the infrastructure by adding HTTPS support using AWS Certificate Manager.
- Transition to a serverless architecture for comparison (P4.1).
- Add more robust monitoring with advanced metrics and logging.

---

## Repository
All artifacts, including the CloudFormation template, can be saved and managed in a GitHub repository for future reference and collaboration.

---

### Contributors
This project was completed as part of an advanced cloud infrastructure learning initiative.

