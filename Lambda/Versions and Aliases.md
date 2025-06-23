 **step-by-step guide** on how to create and manage **versions**, **aliases**, and implement **weighted traffic routing** (canary deployments) in **AWS Lambda** — using both the **AWS Console** and **AWS CLI**.

---

# 🛠️ Step-by-Step Guide: Creating & Managing Versions, Aliases, and Weighted Traffic Routing in AWS Lambda

---

## 🧩 **What You’ll Learn**

* How to **publish Lambda versions**
* How to **create and update aliases**
* How to **implement weighted (canary) traffic routing**
* How to **roll back** to previous versions

---

## ✅ **1. Publish a New Version of Your Lambda Function**

> 📌 Versions are **immutable** snapshots of your function's code and configuration.

### 📍 Using AWS Console:

1. Go to the **Lambda Console**
2. Select your function
3. Click **Actions** → **Publish new version**
4. (Optional) Add a description
5. Click **Publish**

🔁 This creates a version number (e.g., `1`, `2`, `3`) — you can now reference this directly or via aliases.

---

### 📍 Using AWS CLI:

```bash
aws lambda publish-version --function-name MyLambdaFunction
```

✅ The response will include the version number:

```json
{
  "FunctionArn": "arn:aws:lambda:region:123456789012:function:MyLambdaFunction:3",
  "Version": "3"
}
```

---

## ✅ **2. Create or Update an Alias**

> 📌 An alias points to a **specific version** and allows you to manage traffic and environments.

### 📍 Create a New Alias (first time)

```bash
aws lambda create-alias \
  --function-name MyLambdaFunction \
  --name prod \
  --function-version 3
```

### 📍 Update an Existing Alias (e.g., for a new deployment)

```bash
aws lambda update-alias \
  --function-name MyLambdaFunction \
  --name prod \
  --function-version 4
```

> You now have a `prod` alias pointing to a specific version of your Lambda.

---

## ✅ **3. Implement Weighted Traffic Routing (Canary Deployment)**

> 📌 Use this to **gradually shift traffic** between two versions (e.g., new version gets 10% of traffic).

### 🧪 Example: 90% to version 3, 10% to version 4

```bash
aws lambda update-alias \
  --function-name MyLambdaFunction \
  --name prod \
  --function-version 3 \
  --routing-config '{"AdditionalVersionWeights": {"4": 0.1}}'
```

✅ Now:

* 90% of users hit version 3
* 10% are routed to version 4

---

## ✅ **4. Monitor the Deployment**

> 🧩 Use **Amazon CloudWatch Logs** and **Lambda metrics** to track:

* Errors
* Duration
* Throttles
* Invocations by version

🔎 This helps you decide whether to:

* ✅ Increase traffic to the new version
* ❌ Roll back to the previous version

---

## ✅ **5. Promote or Roll Back**

### Promote (shift 100% traffic to new version):

```bash
aws lambda update-alias \
  --function-name MyLambdaFunction \
  --name prod \
  --function-version 4
```

### Rollback (return all traffic to previous version):

```bash
aws lambda update-alias \
  --function-name MyLambdaFunction \
  --name prod \
  --function-version 3
```

---

## 🧠 Bonus: Use Aliases for Environments

| Alias     | Points To | Purpose          |
| --------- | --------- | ---------------- |
| `dev`     | Version 1 | Internal testing |
| `staging` | Version 2 | Pre-production   |
| `prod`    | Version 3 | Live traffic     |

This allows you to test safely in isolated stages before releasing.

---

## ✅ Summary

| Feature            | Benefit                                             |
| ------------------ | --------------------------------------------------- |
| **Versions**       | Immutable snapshots of function code                |
| **Aliases**        | Named pointers to versions (like `prod`, `staging`) |
| **Routing config** | Control traffic % between two versions              |
| **Rollback**       | Instantly revert if issues are found                |

---
