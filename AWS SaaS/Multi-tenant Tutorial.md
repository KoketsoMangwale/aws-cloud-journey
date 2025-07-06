## 📘 Tutorial: Serverless SaaS Multi‑Tenant on AWS

This tutorial is based on the **AWS SaaS Factory Serverless Workshop**—a current, hands-on guide for migrating a monolithic app to a serverless, multi-tenant architecture ([github.com][1]).
It is **step-by-step beginner‑friendly** for building a multi-tenant serverless SaaS—without Cloud9 and with minimal storage usage. We'll use **AWS CloudShell**.

### 🧭 Prerequisites

* AWS account (free tier OK)
* AWS CLI configured
* \~1 GB free disk on CloudShell or local
* Tools: `git`, `sam`, `node`, `npm`, `kubectl` (only if Kubernetes is used)

---

### 🚀 Step 1: Clone the Repo

```bash
git clone https://github.com/aws-samples/aws-saas-factory-serverless-workshop.git
cd aws-saas-factory-serverless-workshop
```

This repo includes labs 1–4 that cover onboarding, multi-tenancy, pooled data models, and isolation ([docs.aws.amazon.com][2], [github.com][1]).

---

### 🔧 Step 2: Provision Infrastructure

Use CloudFormation to launch the stack:

```bash
export BUCKET=my-saas-workshop-bucket
aws s3 mb s3://$BUCKET

aws s3 cp https://s3.us-west-2.amazonaws.com/aws-saas-factory-serverless-saas-workshop-us-west-2/workshop.template .
aws s3 cp workshop.template s3://$BUCKET/

aws cloudformation create-stack \
  --stack-name saas-workshop \
  --template-url https://$BUCKET.s3.amazonaws.com/workshop.template \
  --parameters ParameterKey=EEAssetsBucket,ParameterValue=$BUCKET \
  --capabilities CAPABILITY_NAMED_IAM
```

This deploys the baseline serverless environment, including frontend, shared services, and backend microservices ([github.com][1], [aws.amazon.com][3]).

---

### 🧪 Step 3: Lab 1 – Monolith Review

Explore the single-tenant legacy:

* HTML/JS frontend served from S3/CloudFront
* API Gateway → Lambda → DynamoDB product/order services

Understand how current services work.

---

### 🛠️ Step 4: Lab 2 – Onboarding & Shared Services

* Deploy Lambdas and Cognito user pool for tenant registration
* Introduce shared services:

  * Registration service
  * Tenant management
  * User management via Cognito
* Capture tenant context (like `tenantId`) in JWT tokens ([github.com][1], [d1.awsstatic.com][4], [aws.amazon.com][3])

---

### 🔐 Step 5: Lab 3 – Multi‑Tenant Logic (Pooled Model)

* Modify backend Lambdas to include tenant scoping
* Use pooled DynamoDB table with `tenantId` as partition key
* Update API Gateway authorizers to pass tenant context from JWT to backend functions&#x20;

---

### 🔒 Step 6: Lab 4 – Data Isolation & Pool Model

* Enhance functions to securely isolate data by tenant
* Apply IAM conditions (e.g., `LeadingKeys`) to restrict access based on `tenantId` ([aws.amazon.com][3])

---

### 🧪 Step 7: Lab 5 & Beyond (Optional)

* Tier-based deployment (silo vs shared)
* API throttling via tenant usage plans
* Observability with CloudWatch
* CI/CD via CodePipeline ([d1.awsstatic.com][4], [aws.amazon.com][3])

---

## 🛠️ Use CloudShell Instead of Cloud9 or Local

* CloudShell gives you **preconfigured AWS CLI**, \~1 GB home dir, and persistent storage.
* Clone repo and follow steps above.
* Clean up with: `aws cloudformation delete-stack --stack-name saas-workshop`
* For large builds, run in `/tmp` or offload artifacts to S3.

---

## 🎥 Learn by Watching

Here’s a concise video walkthrough of the serverless SaaS reference:

[Building Serverless SaaS on AWS (re\:Invent 2023)](https://aws.amazon.com/awstv/watch/83138e49318/?utm_source=chatgpt.com)

---

### 💬 Why This Approach Works

* **Updated & maintained** by AWS SaaS Factory
* Covers onboarding, tenant isolation, polymorphic microservices
* Demonstrates both **pooled & siloed** models ([github.com][1], [aws.amazon.com][3])
* No need for Cloud9—works in CloudShell or local dev environment

---

## ✅ Quick Recap

1. Clone the **aws-saas-factory-serverless-workshop** repo
2. Provision baseline stack via CloudFormation
3. Progress through Labs 1–4 for multi-tenant functionality
4. Continue with Labs 5–6 for tiering, throttling, DevOps
5. Clean up when done

---

Need help with code snippets, environment variables, or step-by-step commands for CloudShell? Just let me know!

[1]: https://github.com/aws-samples/aws-saas-factory-serverless-workshop?utm_source=chatgpt.com "aws-samples/aws-saas-factory-serverless-workshop - GitHub"
[2]: https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/build-a-multi-tenant-serverless-architecture-in-amazon-opensearch-service.html?utm_source=chatgpt.com "Build a multi-tenant serverless architecture in Amazon OpenSearch ..."
[3]: https://aws.amazon.com/blogs/apn/building-a-multi-tenant-saas-solution-using-aws-serverless-services/?utm_source=chatgpt.com "Building a Multi-Tenant SaaS Solution Using AWS Serverless Services"
[4]: https://d1.awsstatic.com/events/reinvent/2021/Handson_serverless_SaaS_Building_a_serverless_SaaS_solution_on_AWS_REPEAT_ARC403-R2.pdf?utm_source=chatgpt.com "[PDF] Building a serverless SaaS solution on AWS - awsstatic.com"
