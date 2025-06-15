# 🚀 Automated Static Website Deployment using GitHub Actions & AWS S3 (OIDC)

This project uses **GitHub Actions** and **AWS OIDC (OpenID Connect)** to automate the deployment of a static website to **Amazon S3**. Whenever we push updates to our GitHub repository, GitHub Actions securely assumes an IAM role using GitHub's identity provider and uploads the new content to the S3 bucket—without storing AWS access keys.

## 🛠️ Features

- Continuous deployment via GitHub Actions
- Secure AWS access using GitHub OIDC
- Static website hosting on AWS S3
- No AWS access keys stored in GitHub

---

## 🔐 OIDC Setup on AWS

### 1. Create an IAM Role (Web Identity) with OIDC Trust

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
```
Add S3 Access Permissions to the Role


## ⚙️ GitHub Actions Workflow
Create a workflow file at `.github/workflows/deploy.yml`

```
name: Deploy to S3

on:
  push:
    branches:
      - main

permissions:
  id-token: write
  contents: read

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Configure AWS credentials using OIDC
        uses: aws-actions/configure-aws-credentials@v2
        with:
          role-to-assume: arn:aws:iam::<ACCOUNT_ID>:role/<ROLE_NAME>
          aws-region: us-east-1

      - name: Sync static site to S3
        run: |
          aws s3 sync . s3://your-bucket-name --delete

```
- `Workflow name`: Deploy to S3
- `Trigger Conditions`:  this workflow is run only when a push is made to the main branch

| Step                        | Purpose                                  |
| --------------------------- | ---------------------------------------- |
| `on: push`                  | Triggers deploy on `main` branch push    |
| `permissions`               | Grants minimum necessary rights for OIDC |
| `checkout`                  | Pulls code from your repo                |
| `configure-aws-credentials` | Assumes IAM role securely via OIDC       |
| `aws s3 sync`               | Uploads your site to the S3 bucket       |

