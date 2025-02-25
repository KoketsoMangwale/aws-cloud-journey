# Creating an S3 Bucket Using AWS CloudFormation

## Prerequisites
- AWS account with appropriate permissions
- Access to the AWS Management Console or AWS CLI
- Basic understanding of YAML (or JSON) and CloudFormation

---

## Step 1: Define the CloudFormation Template
Create a YAML file (e.g., `s3-bucket-template.yaml`) with the following content:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: CloudFormation template to create an S3 bucket

Resources:
  MyS3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-unique-bucket-name-12345
      AccessControl: Private
      VersioningConfiguration:
        Status: Enabled
```

---

## Step 2: Validate the CloudFormation Template
Before deployment, ensure your template is valid:

```sh
aws cloudformation validate-template --template-body file://s3-bucket-template.yaml
```

---

## Step 3: Deploy the CloudFormation Stack
Using the AWS CLI, deploy the stack:

```sh
aws cloudformation create-stack --stack-name MyS3BucketStack --template-body file://s3-bucket-template.yaml
```

Or if using the AWS Management Console:
1. Navigate to **CloudFormation** service.
2. Click **Create stack** > **With new resources (standard)**.
3. Upload the `s3-bucket-template.yaml` file.
4. Click **Next** and follow the prompts.

---

## Step 4: Verify the Deployment
Check the status of your stack:

```sh
aws cloudformation describe-stacks --stack-name MyS3BucketStack
```

Or in the AWS Console, confirm the stack’s status is `CREATE_COMPLETE`.

---

## Step 5: Confirm the S3 Bucket Exists
Confirm the bucket has been created:

```sh
aws s3 ls
```

---

## Step 6: Clean Up (Optional)
If you want to delete the bucket and stack:

```sh
aws cloudformation delete-stack --stack-name MyS3BucketStack
```

