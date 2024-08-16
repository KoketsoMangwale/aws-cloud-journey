# Launching and Connecting to a Windows EC2 Instance via RDP

## Objective
To launch a Windows EC2 instance in AWS and connect to it using Remote Desktop Protocol (RDP).

## Prerequisites
- An AWS account.
- A Remote Desktop client (e.g., Remote Desktop Connection on Windows, Microsoft Remote Desktop on macOS).

## Steps

### 1. Launch a Windows EC2 Instance

1. **Navigate to EC2 Dashboard:**
   - Log in to the AWS Management Console.
   - Navigate to the EC2 dashboard.

2. **Click "Launch Instance":**
   - Click the "Launch Instance" button.

3. **Choose an Amazon Machine Image (AMI):**
   - Select a Windows AMI, for example, `Microsoft Windows Server 2022 Base`.

4. **Choose an Instance Type:**
   - Select `t2.micro` (eligible for the free tier).

5. **Configure Instance Details:**
   - Keep the default settings.

6. **Add Storage:**
   - Keep the default settings (30 GB of General Purpose SSD).

7. **Add Tags (Optional):**
   - Optionally add tags to help identify your instance.

8. **Configure Security Group:**
   - Create a new security group that allows RDP (port 3389) from your IP address.
   - Example rule:
     ```
     Type: RDP
     Protocol: TCP
     Port Range: 3389
     Source: Your IP (e.g., 203.0.113.0/32)
     ```

9. **Review and Launch:**
   - Review the settings and click "Launch."
   - Choose an existing key pair or create a new one for RDP access.
   - **Important:** Download the key pair file (`.pem`) if you're creating a new one.

10. **Wait for the Instance to Launch:**
    - Your instance will take a few minutes to launch. Once it's running, you'll see it in the EC2 dashboard.

### 2. Connect to the Windows EC2 Instance via RDP

1. **Get the Public IP Address:**
   - Find the public IP address of your instance from the EC2 dashboard.

2. **Retrieve the Administrator Password:**
   - Select your instance in the EC2 dashboard.
   - Click on "Connect" and then "RDP Client."
   - Click "Get Password."
   - Upload your key pair file (`.pem`) and click "Decrypt Password."
   - Copy the decrypted Administrator password.

3. **Use a Remote Desktop Client:**
   - Open your Remote Desktop client (e.g., Remote Desktop Connection on Windows).
   - Enter the public IP address of your EC2 instance in the Computer field
   - Enter the username in my case it is Administrator in the User name field
   - Click "Connect."

4. **Enter the Credentials:**
   - When prompted, enter "Administrator" as the username.
   - Paste the decrypted password you retrieved earlier.

5. **Verify Connection:**
   - If successful, you should see the Windows desktop of your EC2 instance.

### 3. Post-Connection Steps

- **Initial Setup:** Complete any initial setup required by Windows Server, such as setting time zone, configuring updates, etc.
- **Installing Software:** You can now install any required software or configure your server as needed.

## Outcome
You have successfully launched a Windows EC2 instance and connected to it using RDP.

## Troubleshooting

- **Connection Issues:** Ensure that the security group allows RDP access from your IP and that the instance is running.
- **Wrong Password:** Double-check that you correctly decrypted and entered the Administrator password.

## Additional Resources
- [Amazon EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [RDP Connection Guide](https://docs.microsoft.com/en-us/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)
