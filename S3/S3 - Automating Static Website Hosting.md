# 🚀 Automated Static Website Deployment using GitHub Actions & AWS S3 (OIDC)

This project uses **GitHub Actions** and **AWS OIDC (OpenID Connect)** to automate the deployment of a static website to **Amazon S3**. Whenever we push updates to our GitHub repository, GitHub Actions securely assumes an IAM role using GitHub's identity provider and uploads the new content to the S3 bucket—without storing AWS access keys.

## 🛠️ Features

- Continuous deployment via GitHub Actions
- Secure AWS access using GitHub OIDC
- Static website hosting on AWS S3
- No AWS access keys stored in GitHub

---

## 🔐 OIDC Setup on AWS

### 1. Create an IAM Role with OIDC Trust

Create a new IAM role with the following trust policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::<ACCOUNT_ID>:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "token.actions.githubusercontent.com:aud": "sts.amazonaws.com",
          "token.actions.githubusercontent.com:sub": "repo:<OWNER>/<REPO>:ref:refs/heads/main"
        }
      }
    }
  ]
}
