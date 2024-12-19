# P4 Project Documentation

## Overview
This project involved setting up a scalable cloud infrastructure with AWS, deploying a PHP application connected to an RDS database, and integrating monitoring with CloudWatch.

---

## Infrastructure Setup

### **1. VPC Setup**
- **VPC Name**: `P4-VPC`
- **CIDR Block**: `10.0.0.0/16`

### **2. Subnets**
- **Public Subnet**:
  - Name: `Public-Subnet`
  - CIDR Block: `10.0.1.0/24`
  - Associated with Internet Gateway (IGW).
- **Private Subnet**:
  - Name: `Private-Subnet`
  - CIDR Block: `10.0.2.0/24`

### **3. Route Tables**
- **Public Route Table**:
  - Routes: `0.0.0.0/0` directed to IGW.
- **Private Route Table**:
  - Default route for internal communication.

### **4. Security Groups**
- **EC2 Security Group**:
  - Inbound: Allow SSH (22) and HTTP (80) from any IP.
  - Outbound: Allow all.
- **RDS Security Group**:
  - Inbound: Allow MySQL/Aurora (3306) from EC2 Security Group.
  - Outbound: Allow all.

---

## EC2 Instance Setup

### **1. Instance Details**
- **Instance Type**: `t2.micro`
- **Operating System**: Amazon Linux 2.

### **2. Configuration**
- Updated the instance:
  ```bash
  sudo yum update -y
  ```

- Installed Apache and PHP:
  ```bash
  sudo yum install httpd php php-mysqlnd -y
  ```

- Started and enabled Apache:
  ```bash
  sudo systemctl start httpd
  sudo systemctl enable httpd
  ```

### **3. Application Deployment**
- **Application Code**:
  File: `index.php`

```php
<?php
$servername = "<RDS-ENDPOINT>";
$username = "admin";
$password = "<PASSWORD>";
$database = "testdb";

$conn = new mysqli($servername, $username, $password, $database);
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    if (isset($_POST['add'])) {
        $name = $_POST['name'];
        $email = $_POST['email'];
        $sql = "INSERT INTO users (name, email) VALUES ('$name', '$email')";
        $conn->query($sql);
    }
    if (isset($_POST['delete'])) {
        $id = $_POST['id'];
        $sql = "DELETE FROM users WHERE id=$id";
        $conn->query($sql);
    }
}

$sql = "SELECT * FROM users";
$result = $conn->query($sql);
?>
<!DOCTYPE html>
<html>
<head>
    <title>User Management</title>
</head>
<body>
    <h1>Manage Users</h1>
    <form method="post">
        <input type="text" name="name" placeholder="Name" required>
        <input type="email" name="email" placeholder="Email" required>
        <button type="submit" name="add">Add User</button>
    </form>
    <h2>User List</h2>
    <table border="1">
        <tr><th>ID</th><th>Name</th><th>Email</th><th>Action</th></tr>
        <?php while ($row = $result->fetch_assoc()) { ?>
        <tr>
            <td><?= $row['id'] ?></td>
            <td><?= $row['name'] ?></td>
            <td><?= $row['email'] ?></td>
            <td>
                <form method="post" style="display:inline;">
                    <input type="hidden" name="id" value="<?= $row['id'] ?>">
                    <button type="submit" name="delete">Delete</button>
                </form>
            </td>
        </tr>
        <?php } ?>
    </table>
</body>
</html>
```

- Uploaded `index.php` to `/var/www/html/p4app/`.
- Set permissions:
  ```bash
  sudo chown -R ec2-user:ec2-user /var/www/html/p4app/
  ```

---

## RDS Instance Setup

### **1. Database Details**
- **Engine**: MySQL
- **Instance Type**: db.t2.micro
- **Database Name**: `testdb`
- **Endpoint**: `<RDS-ENDPOINT>`
- **User**: `admin`
- **Password**: `<PASSWORD>`

### **2. Database Table Setup**
- Connected to RDS via EC2:
  ```bash
  mysql -h <RDS-ENDPOINT> -u admin -p
  ```

- Created `users` table:
  ```sql
  CREATE TABLE users (
      id INT AUTO_INCREMENT PRIMARY KEY,
      name VARCHAR(255) NOT NULL,
      email VARCHAR(255) NOT NULL,
      created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
  );
  ```

---

## Monitoring Setup

### **1. CloudWatch Agent on EC2**
- Installed the CloudWatch Agent:
  ```bash
  sudo yum install amazon-cloudwatch-agent -y
  ```

- Created configuration file:
  ```bash
  sudo nano /opt/aws/amazon-cloudwatch-agent/bin/config.json
  ```

  Content:
  ```json
  {
    "agent": {
      "metrics_collection_interval": 60
    },
    "logs": {
      "logs_collected": {
        "files": {
          "collect_list": [
            {
              "file_path": "/var/log/httpd/access_log",
              "log_group_name": "EC2_Apache_Access_Log",
              "log_stream_name": "{instance_id}"
            },
            {
              "file_path": "/var/log/httpd/error_log",
              "log_group_name": "EC2_Apache_Error_Log",
              "log_stream_name": "{instance_id}"
            }
          ]
        }
      }
    }
  }
  ```

- Started the agent:
  ```bash
  sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a start -m ec2 -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json -s
  ```

### **2. RDS Monitoring**
- Verified metrics in CloudWatch for:
  - `CPUUtilization`
  - `FreeStorageSpace`
  - `DatabaseConnections`
- Created alarms for critical thresholds:
  - **CPUUtilization > 75% for 5 minutes**
  - **FreeStorageSpace < 10 GB for 5 minutes**

---

## Cleanup
- Terminated EC2 instance and deleted RDS instance to avoid costs.
- Removed associated VPC, subnets, security groups, and monitoring resources.

---

## Future Improvements
- Add automated scaling for EC2 and RDS.
- Enhance monitoring with custom CloudWatch dashboards.
- Automate infrastructure setup using CloudFormation or Terraform.

