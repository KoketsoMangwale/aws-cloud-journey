## 📘 Tutorial: Serverless SaaS Multi‑Tenant on AWS

This tutorial is based on the **AWS SaaS Serverless Workshop**—a current, hands-on guide for migrating a monolithic app to a serverless, multi-tenant architecture ([github.com][1]).
It is **step-by-step beginner‑friendly** for building a multi-tenant serverless SaaS—without Cloud9 and with minimal storage usage. We'll use **AWS CloudShell**.

### 🧭 Prerequisites

* AWS account (free tier OK)
* AWS CLI configured
* \~16 GB free disk on CloudShell overlay directory
* Tools: `git`, `sam`, `node`, `npm`, `kubectl` (only if Kubernetes is used)

---

### 🚀 Lab 1: Basic Serverless Web Application

### ✅ Step 1: Clone the Repository
```bash
git clone https://github.com/aws-samples/aws-serverless-saas-workshop.git
cd aws-serverless-saas-workshop/Lab1/server
```

### ✅ Step 3: Initialize and Build the SAM App
We'll use `sam build` instead of relying on Cloud9 scripts.


📁 This will build functions from:

`server/ProductService`

`server/OrderService`

And package the layers folder
```
sam build
```

### ✅ Step 4: Set Up a Bucket (once-off per region)

```bash
export BUCKET_NAME=my-saas-workshop-bucket
aws s3 mb s3://$BUCKET_NAME
```

### ✅ Step 5: Deploy the Stack
```
sam deploy \
  --guided \
  --stack-name serverless-saas-lab1 \
  --s3-bucket $BUCKET_NAME \
  --capabilities CAPABILITY_NAMED_IAM \
  --region us-east-1
```

During --guided:

Accept the default values (or modify them).

Choose prod for the StageName parameter.

Save the config file when prompted (this will create/update samconfig.toml).

### ✅ Step 6: Test 

```
curl -X POST https://<api-id>.execute-api.<region>.amazonaws.com/prod/product -H 'Content-Type: application/json'  -d '{"category": "category1", "name": "Alexa", "price": "25", "sku": "XYZ"}'
```

```
{
    "requestId": "86cdac4d-f83d-43b5-960f-80897642d6eb",
    "ip": "98.83.43.71",
    "caller": "-",
    "user": "-",
    "requestTime": "06/Jul/2025:14:36:19 +0000",
    "httpMethod": "POST",
    "resourcePath": "/product",
    "status": "200",
    "protocol": "HTTP/1.1",
    "responseLength": "124"
}

```
[1]: https://github.com/aws-samples/aws-saas-factory-serverless-workshop?utm_source=chatgpt.com "aws-samples/aws-saas-factory-serverless-workshop - GitHub"
[2]: https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/build-a-multi-tenant-serverless-architecture-in-amazon-opensearch-service.html?utm_source=chatgpt.com "Build a multi-tenant serverless architecture in Amazon OpenSearch ..."
[3]: https://aws.amazon.com/blogs/apn/building-a-multi-tenant-saas-solution-using-aws-serverless-services/?utm_source=chatgpt.com "Building a Multi-Tenant SaaS Solution Using AWS Serverless Services"
[4]: https://d1.awsstatic.com/events/reinvent/2021/Handson_serverless_SaaS_Building_a_serverless_SaaS_solution_on_AWS_REPEAT_ARC403-R2.pdf?utm_source=chatgpt.com "[PDF] Building a serverless SaaS solution on AWS - awsstatic.com"
