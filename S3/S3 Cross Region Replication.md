# Configuring S3 Cross-Region Replication (CRR)

This guide outlines the steps to configure **Cross-Region Replication (CRR)** for an Amazon S3 bucket.

---

## Prerequisites
1. **Source Bucket and Destination Bucket**:
   - Create a source bucket (e.g., `source-bucket`) in Region A.
   - Create a destination bucket (e.g., `destination-bucket`) in Region B.

2. **Enable Versioning**:
   - Both the source and destination buckets must have **versioning enabled**.

3. **Permissions**:
   - Ensure the destination bucket's permissions allow the source bucket to replicate objects.

4. **IAM Role**:
   - Create or use an IAM role with the required S3 replication permissions.

---

## Step 1: Enable Versioning
1. Navigate to the **S3 console** in AWS.
2. Select the source bucket, then go to the **Properties** tab.
3. Under **Bucket Versioning**, click **Edit**, and enable versioning.
4. Repeat the process for the destination bucket.

---

## Step 2: Create an IAM Role
1. Open the **IAM console**.
2. Create a new role with the following:
   - **Trusted entity**: AWS service.
   - **Use case**: S3.
3. Attach the following policy to the role:
   ```json
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "s3:GetReplicationConfiguration",
                   "s3:ListBucket",
                   "s3:GetObjectVersion",
                   "s3:GetObjectVersionAcl"
               ],
               "Resource": [
                   "arn:aws:s3:::source-bucket",
                   "arn:aws:s3:::source-bucket/*"
               ]
           },
           {
               "Effect": "Allow",
               "Action": [
                   "s3:ReplicateObject",
                   "s3:ReplicateDelete",
                   "s3:ReplicateTags"
               ],
               "Resource": "arn:aws:s3:::destination-bucket/*"
           }
       ]
   }

4. Give the role a descriptive name (e.g., S3ReplicationRole) and create it.
---
## Step 3: Configure Replication Rules
1. Go to the S3 console and select the source bucket.
2. Navigate to the Management tab.
3. Under Replication rules, click Create replication rule.
4. Name the rule (e.g., CRR-to-destination-bucket).
5. Set the status to Enabled.

---
## Step 4: Choose the Destination
1. Select the destination bucket:
   - Choose bucket in another account if applicable.
2. Specify the destination bucket name (e.g., destination-bucket).
---
## Step 5: Set Permissions

- Choose the IAM role created earlier (e.g., S3ReplicationRole).
---
## Step 6: Configure Rule Filters (Optional)
You can replicate:
- All objects.
- Objects with specific prefixes (e.g., images/).
- Objects with specific tags.
---
## Step 7: Review and Save
1. Review the settings:
 - Source bucket.
 - Destination bucket.
 - IAM role.
 - Rule filters (if any).
2. Click Save to enable the replication rule.

---
## Step 8: Verify Configuration
1. Upload an object to the source bucket.
2. Check the destination bucket after some time to verify that the object has been replicated.
 - Ensure you have permissions to access both buckets.
---
## Notes
**Replication is asynchronous**: Objects may take a few minutes to replicate.
**Object Ownership**: By default, replicated objects are owned by the destination bucket owner.
**Cost**: CRR incurs additional storage and data transfer costs.
