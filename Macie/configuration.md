# 🚨 Amazon Macie Configuration Guide

This guide outlines the steps to configure **Amazon Macie** for scanning and discovering sensitive data in uploaded compliance documents stored in S3. Macie helps automate compliance checks and protect against data leaks in a multi-tenant SaaS architecture.

---

## 📦 Prerequisites

- An active AWS account with appropriate IAM permissions.
- Macie must be enabled in the AWS region of your S3 bucket.
- S3 bucket containing uploaded compliance documents.
- Lambda function with permissions to invoke Macie jobs.
- (Optional) Tagging strategy for tenant identification.

---

## 🛠️ 1. Enable Amazon Macie

1. Go to the [Macie Console](https://console.aws.amazon.com/macie/).
2. Select your AWS region (must match your S3 bucket).
3. Click **"Enable Macie"**.
4. Macie will begin monitoring your account for sensitive data.

---

## 📁 2. Configure S3 Bucket

Ensure that:
- The S3 bucket used for document uploads is **Macie-scannable** (must not use requester-pays).
- Bucket has proper policies allowing Macie to read objects.
- Use prefixes to isolate tenants (e.g., `s3://complimate-docs/<tenant-id>/`).

**Example Policy Snippet**:
```json
{
  "Effect": "Allow",
  "Principal": {
    "Service": "macie.amazonaws.com"
  },
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::complimate-docs/*"
}
```

## ⚙️ 3. Set Up Macie Classification Job (via Lambda)

Create a Macie Classification Job for newly uploaded S3 objects.

**Example Compliance Scan Lambda Function**:
```python
{
import boto3

def start_macie_scan(bucket_name, object_key, tenant_id):
    client = boto3.client('macie2')
    response = client.create_classification_job(
        jobType='ONE_TIME',
        name=f"CompliMateScan-{tenant_id}-{object_key.split('/')[-1]}",
        s3JobDefinition={
            'bucketDefinitions': [
                {
                    'accountId': 'YOUR_AWS_ACCOUNT_ID',
                    'buckets': [bucket_name]
                }
            ],
            'scoping': {
                'includes': {
                    'and': [
                        {
                            'simpleScopeTerm': {
                                'comparator': 'STARTS_WITH',
                                'key': 'OBJECT_KEY',
                                'values': [object_key]
                            }
                        }
                    ]
                }
            }
        },
        customDataIdentifierIds=[],
        tags={'tenantId': tenant_id}
    )
    return response['jobId']
```
## 🔐 4. IAM Role Permissions

Lambda functions or services calling Macie must have the following permissions:

```json
{
  "Effect": "Allow",
  "Action": [
    "macie2:CreateClassificationJob",
    "macie2:DescribeClassificationJob",
    "macie2:GetFindings",
    "macie2:ListFindings"
  ],
  "Resource": "*"
}
```
## 📁 5. Viewing Macie Findings

- Go to the Macie Dashboard to view alerts, findings, and severity levels.
- Use Macie > Findings filters to search by:
    - Job ID
    - S3 object key
    - Tenant ID (if tagged)
 
## 💡 Best Practices

- Use tags (e.g., tenantId) on Macie jobs for multi-tenant traceability.
- Export Macie findings to CloudWatch Logs, S3, or OpenSearch.
- Automate job triggering via S3 Event Notifications or Step Functions.
