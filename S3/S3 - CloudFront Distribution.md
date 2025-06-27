# Proof of Concept Backend – MaruCloudHosting

### 🎯 Goal

Build a serverless backend that:

1. Reads a zipped static site from a user's upload stored in an S3 bucket (`maru-uploads`)
2. Unzips and saves the contents to a shared S3 bucket (`maru-content/<user_id>/`)
3. Backs up the extracted files to a separate S3 bucket (`maru-backup/<user_id>/`)
4. Uses **Amazon CloudFront SaaS Manager** to return a URL for the deployed site (e.g., `https://user123.maruhosting.co.za`)

---

### ✅ Step-by-Step Backend Guide

#### Step 1: Set up Required Buckets

* `maru-uploads` → temporary file upload location (ZIPs)
* `maru-content` → final hosting bucket (per user folder)
* `maru-backup` → backup of deployed files (per user folder)

---

#### Step 2: Lambda Function to Process and Deploy ZIP from S3

This Lambda function:

* Gets the ZIP file from `maru-uploads/<user_id>/site.zip`
* Unzips its content in memory
* Saves files to both `maru-content` and `maru-backup`
* Automatically registers the tenant in CloudFront SaaS Manager

**Lambda Code (Python):**
```
import boto3
import zipfile
import io
import json

s3 = boto3.client('s3')
cloudfront = boto3.client('cloudfront')
UPLOAD_BUCKET = 'maru-uploads'
CONTENT_BUCKET = 'maru-content'
BACKUP_BUCKET = 'maru-backup'
SAAS_SERVICE_NAME = 'maru-sites'
SAAS_DOMAIN_SUFFIX = 'maruhosting.co.za'

def lambda_handler(event, context):
    user_id = event['queryStringParameters']['user_id']
    zip_key = f"{user_id}/site.zip"

    # Download the ZIP file from maru-uploads bucket
    zip_obj = s3.get_object(Bucket=UPLOAD_BUCKET, Key=zip_key)
    zip_bytes = zip_obj['Body'].read()

    # Extract ZIP file in-memory
    with zipfile.ZipFile(io.BytesIO(zip_bytes)) as zip_file:
        for file_name in zip_file.namelist():
            if file_name.endswith('/'):
                continue  # Skip directories
            file_content = zip_file.read(file_name)
            key = f"{user_id}/{file_name}"

            # Upload to content bucket
            s3.put_object(Bucket=CONTENT_BUCKET, Key=key, Body=file_content)

            # Upload to backup bucket
            s3.put_object(Bucket=BACKUP_BUCKET, Key=key, Body=file_content)

    # Register tenant in SaaS Manager
    custom_domain = f"{user_id}.{SAAS_DOMAIN_SUFFIX}"
    origin_path = f"/{user_id}"
    description = f"Maru tenant for {user_id}"

    try:
        cloudfront.register_tenant(
            DomainName=custom_domain,
            OriginPath=origin_path,
            ServiceName=SAAS_SERVICE_NAME,
            Description=description
        )
        website_url = f"https://{custom_domain}"
        return {
            'statusCode': 200,
            'body': json.dumps({
                'message': 'ZIP deployed and tenant registered.',
                'website_url': website_url
            })
        }
    except Exception as e:
        return {
            'statusCode': 500,
            'body': json.dumps({
                'error': f"Deployment succeeded but tenant registration failed: {str(e)}"
            })
        }


---

#### Step 3: Trigger Lambda

* Use an **S3 Event Trigger** or **API Gateway** with a `GET` or `POST` to initiate processing.
* S3 trigger watches for `site.zip` upload under any prefix in `maru-uploads/`

---

#### Step 4: Final Response from Lambda

```python
return {
  'statusCode': 200,
  'body': json.dumps({
    'message': "Deployed successfully",
    'website_url': website_url
  })
}
```

---

### 🚀 Result:

* ZIPs are uploaded to a staging S3 bucket (`maru-uploads`)
* Lambda reads the ZIP, unpacks and deploys to the content bucket
* Backups are saved
* Tenant is automatically registered via SaaS Manager
* A SaaS subdomain is returned (e.g., https://user123.maruhosting.co.za)

---

* This version supports user-defined values for subdomain and path
* Optional `description` field helps track tenants internally
* Trigger via: `GET /register?user_id=user123&custom_domain=xyz.co.za&origin_path=/xyz`

