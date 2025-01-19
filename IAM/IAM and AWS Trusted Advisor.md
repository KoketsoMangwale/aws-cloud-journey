# AWS Trusted Advisor

## Audit Security with AWS Trusted Advisor

## Objectives

- Use AWS Trusted Advisor to perform a basic audit of your AWS resources
- Update Amazon EC2 Security Groups to align with best practice guidelines

### Step 1: Review the Trusted Advisor checks

- Under summary, there will be status indicators:
  - **(Red circle)** Critical – action recommended  
  - **(Blue triangle)** Investigation is recommended  
  - **(Orange triangle)** Warning action recommended  
  - **(Green circle)** No recommended actions  
  - **(Gray)** Checks with excluded items  
- Any non-green status will list the criteria for the status and provide a Recommended Action

As shown below, there are 2 critical recommendations: **MFA on Root Account** and **Security Groups - Specific Ports Unrestricted**. 
You can expand on the recomendations to 

![Alt text](URL_or_path_to_image)

### Step 2: Apply recommended actions

### Step 2.1: Modify Security Groups with Unrestricted Ports
Here you will identify and resolve an incorrectly opened port on a security group. 
In this step, you will remove the inbound security group rule allowing traffic on port 21, as it was accidentally left open.

1. In the navigation panel on the left, select **Security**.
2. In the **Checks** section, expand **Security Groups - Specific Ports Unrestricted**.
3. Scroll down to view additional details.
4. In the **Security Groups** table, identify any groups marked as **Red** in the Status column.

Resolve Port 21 Issue

1. Locate the item with **tcp** in the Protocol column and **21** in the From Port column.
2. Select the **WordPress Security Group** link to open the EC2 management console.
3. In the **Security Groups** panel, select the **Inbound rules** tab.
4. Click **Edit inbound rules**.
5. Locate the **Custom TCP rule** for port 21 and click **Delete**.
6. Select **Save rules** at the bottom of the panel.

### Step 2.2: Limit Inbound Traffic to a Specific Instance
In this step, you will restrict access to a security group, allowing inbound traffic only from a specific Amazon EC2 instance.

1. In the **Services** search field at the top of the console, type **Trusted Advisor**.
2. Select **Refresh all checks** to update the status.
3. In the navigation panel, select **Security**.
4. Expand **Security Groups - Specific Ports Unrestricted** under **Checks**.

There is one more rule that needs to be addressed. The security group is allowing unrestricted access to the MySQL database port 3306 access.
This needs to be restricted to a single IP address.

The rule is permitting incoming traffic to port 3306 from 0.0.0.0/0, which means traffic will be permitted from any computer on the Internet. This is a poor security practice for a database. It is best to limit traffic to and from the database.

In this case, the database should only permit access from the web server.

#### Resolve Port 3306 Issue

1. Locate the **tcp / port 3306** row flagged as **Red** by Trusted Advisor.
2. Select the **MySQL Security Group** link to return to the EC2 management console.
3. In the **Security Groups** panel, select the **Inbound rules** tab.
4. Click **Edit inbound rules**.

   - Identify the rule permitting traffic to port 3306 from `0.0.0.0/0` (open to all). This is a poor security practice for a database.

5. Click **Delete** next to the **MYSQL/Aurora rule**.
6. Click **Save rules**.

#### Add New Rule

1. Click **Add rule**.
2. Select **MySQL/Aurora** from the **Type** drop-down field. The **Protocol** and **Port range** fields will auto-populate with **TCP** and **3306**.
3. In the **Source** drop-down, type `sg` and select the item starting with **Web Security Group**.
   - This rule permits traffic on port 3306 only for members of the **Web Security Group**. Any other traffic over this port will be denied.

4. Click **Save rules**.
