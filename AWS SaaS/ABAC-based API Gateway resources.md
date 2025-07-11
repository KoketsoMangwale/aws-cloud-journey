# **step-by-step guide** to implement **Attribute-Based Access Control (ABAC)** in **Amazon API Gateway**.

This ensures each tenant can only access their **own data**, based on **attributes like `TENANT_ID`** passed in the identity token or IAM principal.

---

## 🧱 What is ABAC?

**ABAC** lets you use **tags or attributes** (e.g., `tenant=user123`) in IAM policies and **evaluate them at runtime** to grant/deny access.

For Maru, it helps isolate tenant actions like:

* `GET /tenants/{tenantId}`
* `GET /uploads/{tenantId}/latest`
* `POST /upload` (with tenant context)

---

## ✅ Step-by-Step Guide to Implement ABAC in API Gateway

---

### ✅ Step 1: Tag IAM users/roles with tenant IDs

This step links your IAM identities (users/roles/groups) with a specific tenant.

#### Example:

Tag the user or role with `TENANT_ID=tenant123`

```bash
aws iam tag-user \
  --user-name my-api-user \
  --tags Key=TENANT_ID,Value=tenant123
```

---

### ✅ Step 2: Use IAM Policy with ABAC Condition

Attach a policy like this to the user/role:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "execute-api:Invoke",
      "Resource": "arn:aws:execute-api:*:*:*/GET/tenants/*",
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/TENANT_ID": "${aws:PrincipalTag/TENANT_ID}"
        }
      }
    }
  ]
}
```

🧠 This means:

* User may invoke `GET /tenants/{id}`
* **Only if the tenant ID in the request matches their own IAM tag**

---

### ✅ Step 3: Add Resource Tags to API Gateway Stage or Resource

You must tag your **API Gateway methods** with matching tenant attributes.

#### Example:

Tag `GET /tenants/tenant123` with:

```bash
aws apigateway tag-resource \
  --resource-arn arn:aws:execute-api:region:account:api-id/stage-name/GET/tenants/tenant123 \
  --tags TENANT_ID=tenant123
```

You can also use CDK or SAM templates to automate this.

---

### ✅ Step 4: Require IAM Authorization in API Gateway

In your API Gateway settings:

* Choose **IAM authorization**
* This ensures `execute-api:Invoke` checks the user's policy

---

### ✅ Step 5: Test with Postman or Curl

Sign your request with AWS SigV4 (or use `aws-sigv4` plugin for Postman):

```bash
aws apigateway test-invoke-method \
  --rest-api-id <id> \
  --resource-id <res-id> \
  --http-method GET \
  --path-with-query-string "/tenants/tenant123"
```

✅ The request will succeed only if:

* The API method has `TENANT_ID=tenant123`
* The IAM user making the request also has `TENANT_ID=tenant123`

---

## 🧠 Example Use Case in Maru

### Scenario:

Tenant `tenantX` uploads their website via:

```
POST /uploads
Headers: x-tenant-id: tenantX
```

You restrict this so:

* Only users **tagged with tenantX** can upload to `maru-content/sites/tenantX/`
* And only access resources tagged with `TENANT_ID=tenantX`

---

## 🧾 Summary

| Step | Action                                                   |
| ---- | -------------------------------------------------------- |
| 1    | Tag IAM roles/users with `TENANT_ID`                     |
| 2    | Write IAM policies using `${aws:PrincipalTag/TENANT_ID}` |
| 3    | Tag API Gateway resources with `TENANT_ID`               |
| 4    | Use IAM auth for API methods                             |
| 5    | Enforce tenant match via condition in policy             |
