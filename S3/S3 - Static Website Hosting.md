# Static Website Hosting with Amazon S3

This guide outlines the steps to configure **Static Website Hosting** in an Amazon S3 bucket.
- Host a static website by using Amazon S3
- Implement a data lifecycle strategy in Amazon S3
  
---
# Hosting a Static Website on S3 and Creating a Lifecycle Policy

## Step 1: Create an S3 Bucket
1. Sign in to the [AWS Management Console](https://aws.amazon.com/console/).
2. Navigate to **S3** from the AWS services menu.
3. Click **Create bucket**.
4. Enter a **Bucket name** (e.g., `my-static-website`).
5. Choose a **region**.
6. Uncheck **Block all public access** (since a static website needs to be publicly accessible).
7. Acknowledge the warning and click **Create bucket**.

## Step 2: Enable Static Website Hosting
1. Open the **S3 bucket** you created.
2. Go to the **Properties** tab.
3. Scroll down to **Static website hosting** and click **Edit**.
4. Select **Enable**.
5. Choose **Host a static website**.
6. Enter the **Index document** (e.g., `index.html`).
7. (Optional) Enter an **Error document** (e.g., `error.html`).
8. Click **Save changes**.

## Optional Step: Enable Versioning
1. Navigate to the **S3 console** in AWS.
2. Select the source bucket, then go to the **Properties** tab.
3. Under **Bucket Versioning**, click **Edit**, and enable versioning.

## Step 3: Upload Website Files
1. In your S3 bucket, go to the **Objects** tab.
2. Click **Upload**.
3. Click **Add files** and select your `index.html` and other website files.
4. Click **Upload**.

## Step 4: Set Bucket Policy for Public Access
1. Go to the **Permissions** tab of your S3 bucket.
2. Scroll down to **Bucket policy** and click **Edit**.
3. Copy and paste the following policy (replace `my-static-website` with your actual bucket name):
    ```json
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Sid": "PublicReadGetObject",
          "Effect": "Allow",
          "Principal": "*",
          "Action": "s3:GetObject",
          "Resource": "arn:aws:s3:::my-static-website/*"
        }
      ]
    }
    ```
4. Click **Save changes**.

## Step 5: Get the Website URL
1. Go to the **Properties** tab.
2. Scroll down to **Static website hosting**.
3. Copy the **Bucket website endpoint**.
4. Open it in a browser to view your website.

---

# Creating an S3 Lifecycle Policy

## Step 1: Open Lifecycle Configuration
1. In your S3 bucket, go to the **Management** tab.
2. Click **Create lifecycle rule**.

## Step 2: Define Rule
1. Enter a **Rule name** (e.g., `ExpireOldFiles`).
2. Choose the **rule scope**:
   - Apply to the **entire bucket** or specific prefixes/tags.
3. Click **Next**.

## Step 3: Configure Lifecycle Actions
1. Choose **Action on current versions** (e.g., "Transition to Glacier after 30 days").
2. (Optional) Choose **Expiration**:
   - Delete files after a certain period (e.g., 90 days).
3. Click **Next**.

## Step 4: Review and Apply
1. Review your lifecycle rule settings.
2. Click **Create rule**.

---

Your static website is now hosted on S3, and an automated lifecycle policy is set up to manage object storage over time!

---
