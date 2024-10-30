# AWS Auto Scaling Group with Windows Instances

## Objective
This guide provides steps to create an Auto Scaling group with a minimum of 2 Windows instances and a maximum of 2 instances. 
This setup helps maintain high availability by ensuring there are always 2 instances running.

## Prerequisites
1. **AWS Account**: Make sure you have an AWS account.
2. **IAM Permissions**: Ensure you have permissions to create EC2 instances, Auto Scaling groups, Launch Configurations (or Templates), and Security Groups.
3. **AWS CLI**: Install and configure the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) with your credentials.
4. **Key Pair**: Create an EC2 key pair to securely connect to your instances.

## Steps

### 1. Create a Security Group

Create a security group to allow RDP access (port 3389) to your Windows instances.

Add an inbound rule to allow RDP access.
aws ec2 authorize-security-group-ingress --group-name WindowsASGSecurityGroup --protocol tcp --port 3389 --cidr 0.0.0.0/0

```bash
aws ec2 create-security-group --group-name WindowsASGSecurityGroup --description "Security group for Windows Auto Scaling Group"
```

### 2. Create a Launch Template
Create a launch template to define the configuration of the instances that the Auto Scaling group will launch.

```bash
aws ec2 create-launch-template \
  --launch-template-name WindowsASGLaunchTemplate \
  --version-description "Windows instance launch template" \
  --launch-template-data '
{
  "ImageId": "ami-0abcdef12345abcdef", # Use the latest Windows Server AMI
  "InstanceType": "t3.medium",
  "KeyName": "YourKeyPairName", # Replace with your actual key pair name
  "SecurityGroupIds": ["sg-0abc12345abc12345"] # Replace with your security group ID
}'

```

### 3. Create an Auto Scaling Group
Now create the Auto Scaling group, 
using the launch template from Step 2, and configure it to maintain exactly 2 instances.

```bash
aws autoscaling create-auto-scaling-group \
  --auto-scaling-group-name WindowsASG \
  --launch-template "LaunchTemplateName=WindowsASGLaunchTemplate,Version=1" \
  --min-size 2 \
  --max-size 2 \
  --desired-capacity 2 \
  --vpc-zone-identifier "subnet-0abcd1234abcd1234,subnet-0abcd1234abcd1235" # Replace with your subnet IDs

```
### 4. Verify the Auto Scaling Group
You can verify the Auto Scaling group configuration and its instances by running:

```bash
aws autoscaling describe-auto-scaling-groups --auto-scaling-group-names WindowsASG

```
There should be 2 instances running within the Auto Scaling group.

## Additional Notes
Ensure your VPC and subnets are properly configured for Windows instances.
Adjust the instance type, AMI, or other configurations as needed.
This setup uses a fixed desired capacity of 2 instances. You can configure Auto Scaling policies for dynamic scaling if needed.
Resources


## Troubleshooting

- **Connection Issues:** Ensure that the security group allows RDP access from your IP and that the instance is running.
- **Wrong Password:** Double-check that you correctly decrypted and entered the Administrator password.

## Additional Resources
- [Amazon EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [AWS Auto Scaling Documentation](https://docs.aws.amazon.com/autoscaling/)
- [RDP Connection Guide](https://docs.microsoft.com/en-us/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)
