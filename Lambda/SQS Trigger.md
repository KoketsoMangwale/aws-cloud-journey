Here's a **beginner-friendly step-by-step guide** to set up a **CI/CD pipeline for deploying an AWS Lambda function triggered by SQS**, using **AWS CodePipeline + CodeBuild**, starting with the sample Python code you provided.

---

## ✅ **Overview of What We'll Set Up**

* **Lambda Function** (Python backend)
* **SQS Queue** (Triggers Lambda)
* **GitHub Repo** (Code source)
* **AWS CodePipeline** (CI/CD pipeline)
* **AWS CodeBuild** (Build & deploy Lambda zip)

---

## 📁 **1. Prepare Your Project Structure**

**Create the following files locally or in your GitHub repo:**

```
lambda-sqs-project/
│
├── lambda_function.py       # Your Python Lambda code
├── buildspec.yml            # CodeBuild instructions
└── requirements.txt         # (Optional) dependencies, leave empty if none
```

### ✅ `lambda_function.py`

```python
import json

def lambda_handler(event, context):
    print("Received event from SQS:")
    print(json.dumps(event, separators=(',', ':')))
    
    for record in event['Records']:
        body = record['body']
        print(f"Processing message: {body}")

    return {
        'statusCode': 200,
        'body': json.dumps('Messages processed successfully!')
    }
```

### ✅ `buildspec.yml`

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      python: 3.11
    commands:
      - echo "Installing dependencies..."
      - pip install -r requirements.txt -t .
  build:
    commands:
      - echo "Zipping deployment package..."
      - zip -r function.zip .
artifacts:
  files:
    - function.zip
```

---

## 🏗️ **2. Create SQS Queue**

In the AWS Console:

* Go to **SQS** → **Create queue**
* Choose **Standard Queue**, name it e.g. `MyQueue`
* Leave the rest as default for now

---

## ⚙️ **3. Create Lambda Function**

1. Go to **Lambda → Create function**
2. Name: `MyLambdaFunction`
3. Runtime: Python 3.11
4. Execution Role:

   * Choose or create a role with basic Lambda permissions **plus SQS permissions**
5. Once created:

   * Add the **SQS trigger**

     * Under *Triggers*, click **Add Trigger**
     * Choose **SQS**, and select your `MyQueue`

---

## 🔐 **4. Set Up IAM Role for CodeBuild**

Create a role for CodeBuild with permissions to:

* Access S3 (for artifacts)
* Deploy Lambda

**Policy Example:**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "lambda:UpdateFunctionCode",
        "s3:GetObject",
        "s3:PutObject",
        "logs:*"
      ],
      "Resource": "*"
    }
  ]
}
```

---

## 🏗️ **5. Create CodeBuild Project**

1. Go to **CodeBuild → Create Build Project**
2. Name: `BuildLambdaSQS`
3. Source: GitHub (connect repo)
4. Environment:

   * Managed image: Ubuntu, Standard
   * Runtime: Python 3.11
   * Service Role: Use the IAM role you created earlier
5. Buildspec: Use the `buildspec.yml` in the repo
6. Artifacts: Store the `function.zip` in an S3 bucket

---

## 🔄 **6. Create CodePipeline**

1. Go to **CodePipeline → Create Pipeline**
2. Source: GitHub (connect to repo)
3. Build: Use the CodeBuild project created above
4. Deploy:

   * Use **AWS Lambda** deploy action
   * Select the Lambda function `MyLambdaFunction`
   * For input artifact, select `function.zip` from the build stage

---

## 🚀 **7. Deploy Workflow**

### 🔁 CI/CD Flow:

* Push code to GitHub → Triggers CodePipeline
* CodeBuild installs deps, zips code
* Artifact is deployed to Lambda
* Lambda updates with latest code

---

## 🧪 **8. Test**

1. Go to SQS → Send a test message to `MyQueue`
2. Check CloudWatch Logs under your Lambda function
3. You should see output like:

   ```
   Received event from SQS:
   {"Records":[...]}
   Processing message: Hello from SQS
   ```

---

## 📘 Future Enhancements

* Add tests and update `buildspec.yml` to include testing phases.
* Use GitHub Actions with OIDC as an alternative for tighter GitHub integration.
* For production, add stages like **staging**, **approval gates**, or **notifications (SNS/Slack)** in CodePipeline.

