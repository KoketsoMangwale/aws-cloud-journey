# AWS Auto Scaling Group with Windows Instances

## Objective
To create a Virtual Private Cloud (VPC) and launch a web server 
This setup helps maintain high availability by ensuring there are always 2 instances running.

To create a Virtual Private Cloud (VPC) and launch a web server, follow this step-by-step guide. This process assumes you're using Amazon Web Services (AWS), though the general principles apply to most cloud providers.

---

### **Step 1: Create a VPC**
1. **Log in to AWS Management Console**:
   - Go to the **VPC Dashboard**.

2. **Create the VPC**:
   - Click **Create VPC**.
   - Provide a name tag for your VPC (e.g., `MyVPC`).
   - Specify an IPv4 CIDR block (e.g., `10.0.0.0/16`).
   - For IPv6, choose **No IPv6 CIDR block** (optional).
   - Leave tenancy as **default** and click **Create VPC**.

3. **Create Subnets**:
   - Click **Subnets** > **Create subnet**.
   - Select your VPC and a specific Availability Zone.
   - Assign a CIDR block within the VPC range (e.g., `10.0.1.0/24`).
   - Repeat this to create a public and private subnet if needed.

4. **Create and Attach an Internet Gateway**:
   - Go to **Internet Gateways**, and click **Create Internet Gateway**.
   - Attach it to your VPC under the **Actions** menu.

5. **Set Up a Route Table**:
   - Navigate to **Route Tables**, and associate it with your public subnet.
   - Add a route: Destination = `0.0.0.0/0`, Target = Internet Gateway.

---

### **Step 2: Launch an EC2 Instance**
1. **Navigate to the EC2 Dashboard**:
   - Click **Launch Instances**.

2. **Choose an Amazon Machine Image (AMI)**:
   - Select an AMI with your preferred operating system (e.g., Amazon Linux 2, Ubuntu).

3. **Choose an Instance Type**:
   - Select an instance type (e.g., `t2.micro` for a free tier-eligible option).

4. **Configure Instance Details**:
   - Select the VPC and public subnet.
   - Enable **Auto-assign Public IP**.

5. **Add Storage**:
   - Specify storage (default is usually fine for a web server).

6. **Add Tags**:
   - Add a tag for easier identification (e.g., `Key=Name`, `Value=MyWebServer`).

7. **Configure Security Group**:
   - Create a security group to allow:
     - **SSH (port 22)** for remote access.
     - **HTTP (port 80)** for web traffic.

8. **Review and Launch**:
   - Launch the instance, and create/download a key pair if you don’t have one.

---

### **Step 3: Configure the Web Server**
1. **Connect to the Instance**:
   - Use SSH: `ssh -i "key.pem" ec2-user@<Public_IP>`

2. **Install Web Server Software**:
   - For Amazon Linux 2:
     ```bash
     sudo yum update -y
     sudo yum install -y httpd
     sudo systemctl start httpd
     sudo systemctl enable httpd
     ```
   - For Ubuntu:
     ```bash
     sudo apt update
     sudo apt install -y apache2
     sudo systemctl start apache2
     sudo systemctl enable apache2
     ```

3. **Verify Web Server**:
   - Open a browser and navigate to `http://<Public_IP>`.

---

### **Step 4: (Optional) Use Elastic IP**
1. **Allocate an Elastic IP**:
   - In the **Elastic IPs** section, allocate a new Elastic IP address.

2. **Associate with the Instance**:
   - Attach the Elastic IP to your EC2 instance for a static IP.

---

You now have a working VPC and a running web server! Let me know if you need help with additional configurations, like SSL/TLS or custom domain integration.## Skills
- 