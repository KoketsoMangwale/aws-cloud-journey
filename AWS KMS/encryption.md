# AWS KMS Key and Encrypted EBS Volume Setup

## Overview
This guide outlines the steps for creating and managing an AWS KMS (Key Management Service) key, encrypting an Amazon EBS (Elastic Block Store) volume, and observing key management activities via AWS CloudTrail.

---

## Task 2: Creating an AWS KMS Key

1. **Open AWS KMS Console:**
   - In AWS Management Console, search for "KMS" and open *Key Management Service*.
   
2. **Create Customer Managed Key:**
   - Navigate to **Customer managed keys**.
   - Click **Create key**.

3. **Key Configuration:**
   - Select **Symmetric** for Key type (256-bit secret key).
   - Click **Next**.

4. **Set Key Alias:**
   - Enter alias: `MyKMSKey`.
   - Click **Next**.

5. **Assign Key Permissions:**
   - Administrative permissions:
     - Search and select the `voclabs` role.
   - Usage permissions:
     - Search and select the `voclabs` role.
   
6. **Complete Key Creation:**
   - Review and click **Finish**.

---

## Task 3: Creating and Attaching Encrypted EBS Volume

1. **Open EC2 Console:**
   - Search for and open the *EC2* service.
   - Navigate to **Instances** and note the *Availability Zone* of `LabInstance`.

2. **Create EBS Volume:**
   - Navigate to **Volumes** and click **Create volume**.
   - Configuration:
     - Size: `1 GiB`
     - Availability Zone: Same as `LabInstance`
     - Encryption: **Encrypt this volume**
     - KMS Key: `MyKMSKey`
   - Click **Create volume**.

3. **Attach EBS Volume:**
   - Select the newly created volume.
   - Choose **Actions > Attach volume**.
   - Select the `LabInstance` and attach the volume.

4. **Verify Attachment:**
   - In the EC2 console, under `LabInstance` > **Storage**, confirm:
     - Root volume: Not encrypted.
     - Data volume: Encrypted with `MyKMSKey`.

---

## Task 4: Disabling Encryption Key and Observing Effects

1. **Disable KMS Key:**
   - Open *KMS console* and navigate to **Customer managed keys**.
   - Select `MyKMSKey`, choose **Key actions > Disable**, and confirm.

2. **Detach EBS Volume:**
   - Open *EC2 console* and navigate to **Volumes**.
   - Select the data volume, choose **Actions > Detach volume**.

3. **Attempt Re-attach:**
   - Try to attach the volume to `LabInstance` — the operation fails due to the disabled KMS key.

4. **Review CloudTrail Events:**
   - Open *CloudTrail console* and navigate to **Event history**.
   - Review `DisableKey` and `AttachVolume` events.

---

## Task 5: Analyzing AWS KMS Activity with CloudTrail

1. **Filter CloudTrail Events:**
   - Open *CloudTrail console* and choose **Event history**.
   - Filter by **Event source: kms.amazonaws.com**.

2. **Analyze Events:**
   - `CreateGrant`: EC2 instance requests permission to use the KMS key.
   - `Decrypt`: EC2 instance decrypts the encrypted data key.
   - `GenerateDataKeyWithoutPlaintext`: Generates a data key for EBS volume encryption.

---

## Task 6: Reviewing Key Rotation

1. **Enable Key Rotation:**
   - Open *KMS console* and navigate to **Customer managed keys**.
   - Select `MyKMSKey`, choose **Key rotation**, and enable **Automatically rotate this KMS key every year**.
   - Save changes.

---

## Conclusion
Congratulations! You have successfully:
- Created and managed an AWS KMS key.
- Encrypted and attached an EBS volume to an EC2 instance.
- Observed the impact of disabling a KMS key.
- Monitored encryption operations via AWS CloudTrail.
- Configured automatic key rotation for enhanced security.

