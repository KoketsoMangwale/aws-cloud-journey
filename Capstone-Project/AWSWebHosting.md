# 🌐 Capstone Project: Secure, Scalable Web Hosting on AWS

## 📘 Project Scenario

**Client:** Example Social Research Organization  
**Goal:** Secure and scale a PHP website and MySQL database hosted originally on a single EC2 instance. Ensure best practices for access control, scalability, availability, and data protection.

---

## 📍 Objectives

- ✅ Provide secure hosting for the MySQL database
- ✅ Ensure secure access to the database
- ✅ Enable anonymous access to the website
- ✅ Host the PHP website in a private subnet using a t2.micro instance
- ✅ Grant SSH access to administrators securely
- ✅ Provide high availability with a load balancer
- ✅ Enable automatic scaling with a launch template

---

## 🗺️ Step-by-Step Solution

### 1. **VPC and Networking**
- Create a **VPC** with:
  - 2 **public subnets** (e.g., `subnet-a`, `subnet-b`)
  - 2 **private subnets** (e.g., `subnet-c`, `subnet-d`)
- Add an **Internet Gateway** and route public subnets through it
- Add a **NAT Gateway** in one public subnet and route private subnets through it

---

### 2. **Security Groups**
- Create 3 Security Groups:
  - `SG-ALB`: Allow inbound HTTP (port 80) from `0.0.0.0/0`
  - `SG-Web`: Allow inbound traffic from `SG-ALB` (port 80)
  - `SG-DB`: Allow inbound MySQL traffic (port 3306) from `SG-Web`

---

### 3. **Bastion Host (Jump Box)**
- Launch an EC2 instance in a **public subnet**
- Assign it a public IP address and restrict access by IP in its security group
- Use this instance for **SSH access** into private instances

---

### 4. **RDS Setup**
- Launch **Amazon RDS (MySQL)** in private subnets
- Use `SG-DB` for security
- Enable **automated backups**, **multi-AZ** if available
- Enforce **SSL-only connections**

---

### 5. **Launch Template & Auto Scaling Group**
- Create a **Launch Template**:
  - Use Amazon Linux 2
  - Install Apache + PHP + MySQL client in `user data`
  - Pull website files from S3 or use EC2 User Data to deploy
  - Use `SG-Web` for security
- Create an **Auto Scaling Group**:
  - Attach it to **private subnets**
  - Use the launch template
  - Define scaling policies (e.g., CPU > 70%)

---

### 6. **Application Load Balancer**
- Launch an **ALB** in public subnets
- Attach `SG-ALB`
- Set listener on **port 80**
- Target group: attach Auto Scaling Group EC2 instances

---

### 7. **Database Connectivity**
- Ensure the PHP app connects to the RDS instance via its **private endpoint**
- Store DB credentials securely using **AWS Systems Manager Parameter Store** or **Secrets Manager**

---

### 8. **SSH Access for Admins**
- Admins SSH into **Bastion Host**
- From Bastion, connect to private EC2 instances
- Use **key pair authentication**

---

### 9. **Monitoring & Logging**
- Enable **CloudWatch Logs** for EC2 and ALB
- Set up **alarms** for CPU utilization, unhealthy hosts
- Monitor **RDS metrics** like connections, latency, storage

---

### 10. **Testing & Validation**
| Test                                      | Expected Result                                |
|-------------------------------------------|-------------------------------------------------|
| Load site using ALB URL                   | Website loads correctly                         |
| Form submits and queries DB successfully  | Results are returned without errors             |
| RDS not reachable from outside            | Connection times out from public IPs            |
| SSH only works via Bastion Host           | Admins must connect through jump box            |
| Auto Scaling adds/removes instances       | Instances respond to CPU load or traffic        |

---

## ✅ Final Architecture Summary

