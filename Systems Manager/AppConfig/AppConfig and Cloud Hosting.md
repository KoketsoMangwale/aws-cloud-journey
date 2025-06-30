# Step-by-step guide to configure **AWS AppConfig** for **Maru Cloud Hosting** platform. 
AppConfig is part of AWS Systems Manager and is great for **managing dynamic app settings**, **feature flags**, and **environment-specific config** without redeploying your app.

---

## 🧱 What We’ll Configure for Maru

Let’s say you want to centrally manage:

* Feature flags (e.g., enable custom domains, backups)
* Limits (e.g., max upload size)
* Environment-specific values (e.g., content bucket names)

---

## ✅ Step-by-Step: Configure AWS AppConfig for Maru Cloud Hosting

---

### ✅ 1. **Open AppConfig Console**

* Go to: [https://console.aws.amazon.com/systems-manager/appconfig](https://console.aws.amazon.com/systems-manager/appconfig)
* Or: **AWS Systems Manager → AppConfig**

---

### ✅ 2. **Create an Application**

This is your umbrella config for Maru.

* Click **Create application**
* Name: `maru-cloud-hosting`
* Description: "App settings for Maru Cloud Hosting backend"

---

### ✅ 3. **Create an Environment**

Environments represent **stages** like dev, test, or prod.

* Click your application → **Create environment**
* Name: `dev`, `prod`, etc.
* Optionally link a monitoring tool (e.g., CloudWatch alarms)

---

### ✅ 4. **Create a Configuration Profile**

This is the actual config data your app will load.

* Click **Create configuration profile**
* Name: `maru-default-settings`
* Configuration source: **AWS Systems Manager (SSM) Parameter Store** or **hosted**
* Format: **JSON**

🧠 If using *hosted configuration*, you can create the JSON right in the console.

---

### ✅ 5. **Enter Your Configuration JSON**

Example:

```json
{
  "upload": {
    "maxSizeMB": 10,
    "allowedTypes": ["zip"]
  },
  "features": {
    "customDomains": true,
    "backupsEnabled": true
  },
  "buckets": {
    "uploads": "maru-uploads",
    "content": "maru-content",
    "backup": "maru-backup"
  }
}
```

Click **Create hosted configuration version**

---

### ✅ 6. **Deploy the Configuration**

* Go to your environment (e.g., `dev`)
* Click **Start deployment**
* Deployment strategy: choose **AllAtOnce** for MVP or **Linear10Percent** for gradual rollout
* Click **Start**

✅ This makes the config **live and versioned**.

---

### ✅ 7. **Access the Config from Lambda (Optional)**

If you want your Lambda functions to read AppConfig dynamically:

#### A. Grant IAM permission:

Add this to your Lambda role:

```json
{
  "Effect": "Allow",
  "Action": [
    "appconfig:GetConfiguration"
  ],
  "Resource": "*"
}
```

#### B. Use Boto3 in Lambda:

```python
import boto3

client = boto3.client('appconfigdata')

# Start a config session
response = client.start_configuration_session(
    ApplicationIdentifier='maru-cloud-hosting',
    EnvironmentIdentifier='dev',
    ConfigurationProfileIdentifier='maru-default-settings'
)

token = response['InitialConfigurationToken']

# Get the latest config
config = client.get_latest_configuration(
    ConfigurationToken=token
)

config_data = config['Configuration'].read()
settings = json.loads(config_data)

print(settings['upload']['maxSizeMB'])
```

---

### ✅ 8. **Optional Enhancements**

| Use Case              | Tool or Setting                                        |
| --------------------- | ------------------------------------------------------ |
| Validate JSON         | Add a **schema** during profile creation               |
| Feature flag toggling | Use **AppConfig Extensions**                           |
| Real-time updates     | Store config in **Parameter Store or S3**, use polling |
| Notify on deployment  | Use **SNS or CloudWatch Alarms**                       |

---

## 🚀 Summary

| Step | Description                                                          |
| ---- | -------------------------------------------------------------------- |
| 1–2  | Create App and Environment                                           |
| 3–5  | Create a hosted config profile                                       |
| 6    | Deploy the config                                                    |
| 7    | Read config in your app/Lambda                                       |
| 8    | Use for dynamic settings like limits, feature flags, S3 bucket names |
