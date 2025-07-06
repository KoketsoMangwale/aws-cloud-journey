## 📘 Tutorial: Serverless SaaS Multi‑Tenant on AWS

This tutorial is based on the **AWS SaaS Factory Serverless Workshop**—a current, hands-on guide for migrating a monolithic app to a serverless, multi-tenant architecture ([github.com][1]).
It is **step-by-step beginner‑friendly** for building a multi-tenant serverless SaaS—without Cloud9 and with minimal storage usage. We'll use **AWS CloudShell**.

### 🧭 Prerequisites

* AWS account (free tier OK)
* AWS CLI configured
* \~16 GB free disk on CloudShell overlay directory
* Tools: `git`, `sam`, `node`, `npm`, `kubectl` (only if Kubernetes is used)

---

### 🚀 Step 1: Clone the Repo

```bash
git clone https://github.com/aws-samples/aws-saas-factory-serverless-workshop.git
cd aws-saas-factory-serverless-workshop
```

I had the issue with limited disk size so I switched to this directory before cloning the repository.
```
cd /tmp
```

This repo includes labs 1–4 that cover onboarding, multi-tenancy, pooled data models, and isolation ([docs.aws.amazon.com][2], [github.com][1]).

---

### 🔧 Step 2: Provision Infrastructure


### 🔹 2.1 Set Up a Bucket (once per region)

```bash
export BUCKET=my-saas-workshop-bucket
aws s3 mb s3://$BUCKET
curl -O https://s3.us-west-2.amazonaws.com/aws-saas-factory-serverless-saas-workshop-us-west-2/workshop.template
aws s3 cp workshop.template s3://$BUCKET/
```

## 🧠 Understanding the Linux & AWS CLI Commands

You are creating an S3 bucket, uploading a CloudFormation template, and then using that template to create AWS resources.

##### `export BUCKET=my-saas-workshop-bucket`

### 🧾 What it does:

* This creates a **temporary environment variable** called `BUCKET` and assigns it the value `my-saas-workshop-bucket`. (Make sure the bucket name is unique)

### 🧠 Why it's useful:

* It allows you to use `$BUCKET` as a shortcut instead of typing the full bucket name again and again in future commands.

### 💡 Example:

```bash
echo $BUCKET
```

Will return:

```
my-saas-workshop-bucket
```

---

#### `aws s3 mb s3://$BUCKET`

### 🧾 What it does:

* `aws s3 mb` = **Make Bucket** — this command **creates a new S3 bucket**.
* `s3://$BUCKET` = this uses the value of the `BUCKET` variable you just set.

### 💡 Example:

If `$BUCKET` is `my-saas-workshop-bucket`, then the command becomes:

```bash
aws s3 mb s3://my-saas-workshop-bucket
```

✅ After running, you should see:

```
make_bucket: my-saas-workshop-bucket
```

🟡 **Note:** Bucket names must be globally unique. If someone else already used that name, pick a unique one like:

```bash
export BUCKET=my-saas-workshop-bucket-2025-koketso
```

---

#### `curl -O https://.../workshop.template .`

### 🧾 What it does:

* This will download the file `workshop.template` to your current directory (e.g., /tmp).
* `https://...` = this is the URL of the workshop template stored publicly in S3

### ✅ This downloads the `workshop.template` file into your current directory.

---

#### `aws s3 cp workshop.template s3://$BUCKET/`

### 🧾 What it does:

* This **uploads** the `workshop.template` you just downloaded to your own S3 bucket.

### 💡 Example:

```bash
aws s3 cp workshop.template s3://my-saas-workshop-bucket/
```

✅ You’ll see something like:

```
upload: ./workshop.template to s3://my-saas-workshop-bucket/workshop.template
```

---
### 🔹 2.2 Create and Deploy CludFormation Stack

###### `aws cloudformation create-stack \ ...`

This command **deploys the stack** using CloudFormation.

### 💥 Let's break it down:

```bash
aws cloudformation create-stack \
  --stack-name saas-workshop \
  --template-url https://$BUCKET.s3.amazonaws.com/workshop.template \
  --parameters ParameterKey=EEAssetsBucket,ParameterValue=$BUCKET \
  --capabilities CAPABILITY_NAMED_IAM
```

### ✅ What each line means:

| Part                                                                | Meaning                                                             |
| ------------------------------------------------------------------- | ------------------------------------------------------------------- |
| `aws cloudformation create-stack`                                   | Start creating a CloudFormation stack                               |
| `--stack-name saas-workshop`                                        | Give your stack a name (so you can manage/delete it later)          |
| `--template-url https://$BUCKET.s3.amazonaws.com/workshop.template` | Tells CloudFormation where to find your template                    |
| `--parameters ParameterKey=EEAssetsBucket,ParameterValue=$BUCKET`   | Passes input parameters (the bucket name) to the template           |
| `--capabilities CAPABILITY_NAMED_IAM`                               | Allows CloudFormation to create IAM roles (needed for Lambda, etc.) |

✅ If it runs successfully, you'll see output like:

```
{
    "StackId": "arn:aws:cloudformation:us-east-1:123456789012:stack/saas-workshop/..."
}
```

🟡 **Note:** If it fails, it might be because:

* The bucket name is already taken (use a unique one).
* You didn’t upload the template.
* You don’t have IAM permissions.

---

## 🧠 Final Notes for Beginners

| Command                           | What it does                                        |
| --------------------------------- | --------------------------------------------------- |
| `export`                          | Defines a variable for use in your terminal session |
| `$BUCKET`                         | References the value of a variable                  |
| `aws s3 mb`                       | Makes a bucket                                      |
| `aws s3 cp`                       | Copies files to/from S3                             |
| `aws cloudformation create-stack` | Deploys AWS resources using a template              |


### 🔹 2.3. Upload CloudFormation Template

```bash
aws s3 cp workshop.template s3://$BUCKET_NAME/
```

> If `workshop.template` is not already in the repo, you can download it:

```bash
curl -O https://s3.us-west-2.amazonaws.com/aws-saas-factory-serverless-saas-workshop-us-west-2/workshop.template
aws s3 cp workshop.template s3://$BUCKET_NAME/
```

---

### 🔹 2.4. Deploy the Stack via CloudFormation

```bash
aws cloudformation create-stack \
  --stack-name saas-workshop \
  --template-url https://$BUCKET_NAME.s3.amazonaws.com/workshop.template \
  --capabilities CAPABILITY_NAMED_IAM
```

---

### 🔹


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
