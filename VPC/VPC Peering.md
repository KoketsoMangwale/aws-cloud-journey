# VPC Peering - Connecting VPC's
## VPC Peering between Marketing and Finance Data

### Create Peering Connection

### Accept Requested Connection

# VPC Peering Connection Setup

## Overview
This guide walks you through creating a Virtual Private Cloud (VPC) peering connection between two VPCs: **Lab VPC** and **Shared VPC**. Lab VPC hosts an inventory application on an EC2 instance in a public subnet, while Shared VPC hosts a database instance in a private subnet.

---

## Tasks

### Task 1: Creating a VPC Peering Connection
1. **Open the VPC Management Console:**
   - In the AWS Management Console, search for and choose **VPC**.

2. **Create the Peering Connection:**
   - In the left navigation pane, select **Peering connections**.
   - Choose **Create peering connection** and configure:
     - **Name (optional):** Lab-Peer
     - **VPC ID (Requester):** Lab VPC
     - **VPC ID (Accepter):** Shared VPC
   - Choose **Create peering connection**.

3. **Accept the Peering Request:**
   - Once the connection is created, go to **Actions** and choose **Accept request**.

---

### Task 2: Configuring Route Tables
1. **Update Lab VPC Route Table:**
   - Go to **Route Tables** and select **Lab Public Route Table**.
   - In the **Routes** tab, choose **Edit routes** and add:
     - **Destination:** `10.5.0.0/16` (CIDR block of Shared VPC)
     - **Target:** Peering Connection → Lab-Peer
   - Choose **Save changes**.

2. **Update Shared VPC Route Table:**
   - Select **Shared-VPC Route Table**.
   - In the **Routes** tab, choose **Edit routes** and add:
     - **Destination:** `10.0.0.0/16` (CIDR block of Lab VPC)
     - **Target:** Peering Connection → Lab-Peer
   - Choose **Save changes**.

---

### Task 3: Enabling VPC Flow Logs
1. **Create VPC Flow Logs:**
   - Go to **Your VPCs** and select **Shared VPC**.
   - In the **Flow logs** tab, choose **Create flow log** and configure:
     - **Name (optional):** SharedVPCLogs
     - **Maximum aggregation interval:** 1 minute
     - **Destination:** Send to CloudWatch Logs
     - **Destination log group:** ShareVPCFlowLogs (new log group)
     - **IAM Role:** vpc-flow-logs-Role
   - Choose **Create flow log**.

2. **Verify Flow Logs:**
   - In the **Flow logs** tab, confirm that **SharedVPCLogs** was created.
   - Choose the **ShareVPCFlowLogs** hyperlink to open the CloudWatch log group.

---

### Task 4: Testing the VPC Peering Connection
1. **Access the Inventory Application:**
   - Copy the **EC2PublicIP** and open it in a new browser tab.
   - You should see the message: _"Please configure Settings to connect to database."_

2. **Configure Database Connection:**
   - Choose **Settings** and enter:
     - **Endpoint:** Database endpoint from AWS Details
     - **Database:** inventory
     - **Username:** admin
     - **Password:** lab-password
   - Choose **Save**.

3. **Confirm Database Access:**
   - The application should now show data from the database, confirming that the peering connection works.

---

### Task 5: Analyzing VPC Flow Logs
1. **Open Flow Logs:**
   - Go to the **ShareVPCFlowLogs** in CloudWatch.
   - Select the **Log stream** (`eni-*`).

2. **Analyze Traffic Patterns:**
   - After a few minutes, network traffic will appear.
   - Look for logs with port `3306` (RDS database port) to see traffic between the application and database.

---

## Key Fields in Flow Logs
| Field             | Description                            |
|-------------------|---------------------------------------|
| **Network Interface** | Elastic network interface ID         |
| **From IP**       | Private IP of the database instance    |
| **To IP**         | Private IP of the EC2 application      |
| **Port**          | `3306` (RDS database port)             |
| **Action**        | Accept/Reject                          |
| **Status**        | OK                                     |

---

## Conclusion
🎉 Congratulations! You successfully completed the following:
- Created a VPC peering connection
- Configured route tables to use the peering connection
- Enabled VPC Flow Logs
- Tested the peering connection
- Analyzed VPC flow logs

Your VPCs are now securely and efficiently communicating with each other! 🚀


