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
