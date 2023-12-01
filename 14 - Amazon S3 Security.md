# 14 - Amazon S3 Security

## Amazon S3 - Object Encryption

- You can encrypt objects in S3 buckets using one of 4 methods.

### Server Side Encryption (SSE)

#### Server Side Encryption with Amazon-S3 managed keys (SSE-S3) - Enabled by Default
- Encrypts S3 objects using keys handled, managed, and owned by AWS.
- Encryption type is `AES-256`
- Must set header "x-amz-server-side-encryption": "AES256"

#### Server Side Encryption with KMS Keys stored in AWS KMS (SSE-KMS)
- Leverage AWS Key Management Service (AWS KMS) to manage encryption keys.
- KMS Advantages: user control + audit key using CloudTrail
- Must set header "x-amz-server-side-encryption": "aws:kms"

#### Server Side Encryption with Customer Provided Keys (SSE-C)
- When you want to manage your own encryption keys.
- S3 does not store the encryption key you provide.
- `HTTPS must be used`
- Encryption key must provided in HTTP headers, for every HTTP request made.

### Client Side Encryption

- Use client libraries such as `Amazon S3 Client-Side Encryption Library`.
- Clients must encrypt data themselves before sending to S3.
- Clients must encrypt data themselves when retrieving from S3.
- Customer fully manages the keys and encryption cycle.

---
## Amazon S3 - Encryption in Transit (SSL/TLS)

- Amazon exposes two endpoints
    - `HTTP Endpoint` - non encrypted
    - `HTTPS Endpoint` - encryption in flight
- HTTPS is recommended
- HTTPS is mandatory for SSE-C

---
## Amazon S3 - Force Encryption in Transit aws:SecureTransport

- You can force the encryption in transit by using bucket policy.
- We attach bucket policy to S3 bucket with this statement.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Principal": "*",
            "Action": [
                "s3:GetObject",
            ],
            "Resource": [
                "arn:aws:s3:::examplebucket/*",
            ],
            "Condition": {
                "Bool": {
                    "aws:SecureTransport": false
                }
            }
        }
    ]
}
```

---
## What is CORS?

- Cross Origin Resource Sharing (CORS)
- Origin = scheme (protocol) + host (domain) + port
    - Ex. https://www.example.com (implied port is 443 for HTTPS, 80 for HTTP)
    - `scheme (protocol):` http
    - `host (domain):` www.example.com
    - `port:` 443
- CORS is `Web Browser` based mechanism to allow requests to other origin while visiting the main origin.
- `Same Origins:` http://example.com/app1 & http://example.com/app2
- `Different Origins:` http://www.example.com & http://other.example.com
- The requests won't be fulfilled unless the other origin allows for the requests, using `CORS Headers` (Ex. `Access-Control-Allow-Origin`).

---
## Amazon S3 - MFA Delete

- `MFA (Multi Factor Authentication)` - Force user to generate a code on a device (usually a mobile phone or hardware) before doing important operations on S3.
- To use MFA Delete, `Versioning must be enabled` on the bucket.
- **Only the bucket owner (root account) can enable/disable MFA Delete.**
- We can't enable it from console we have to use AWS CLI.

### MFA will be required to:

- Permanently delete an object version
- Suspend versioning on the bucket

### MFA won't be required to:

- Enable versioning 
- List deleted versions

```bash
# generate root access keys
aws configure --profile root-mfa-delete-demo

# enable mfa delete
aws s3api put-bucket-versioning --bucket mfa-demo-stephane --versioning-configuration Status=Enabled,MFADelete=Enabled --mfa "arn-of-mfa-device mfa-code" --profile root-mfa-delete-demo

# disable mfa delete
aws s3api put-bucket-versioning --bucket mfa-demo-stephane --versioning-configuration Status=Enabled,MFADelete=Disabled --mfa "arn-of-mfa-device mfa-code" --profile root-mfa-delete-demo

# delete the root credentials in the IAM console!!!
```

---
## S3 Access Logs

- For audit purpose, you may want to log all access to S3 buckets
- Any request made to S3, from any account, authorized or denied, will be logged into another S3 bucket
- That data can be analyzed using data analysis tools such as Amazon Athena
- The target logging bucket must be in the same AWS region
- The log format is at: 
    - https://docs.aws.amazon.com/AmazonS3/latest/dev/LogFormat.htm

### S3 Access Logs : Warning

- Do not set your logging bucket to be the monitored bucket (Bucket in which you are storing actual files).
- It will create a logging loop, and your bucket will grow exponentially.

---
## Amazon S3 - Pre Signed URLs

- Generate pre-signed URLs using the S3 Console, AWS CLI or SDK
- URL Expiration
    - S3 Console – up to 12 hours
    - AWS CLI – uo tp ~168 hours
- Users given a pre-signed URL inherit the permissions of the user that generated the URL for GET / PUT

### Examples: 
- Allow only logged-in users to download a premium video from your S3 bucket
- Allow an ever-changing list of users to download files by generating URLs dynamically
- Allow temporarily a user to upload a file to a precise location in your S3 bucket

---
## S3 Glacier Vault Lock

- Adopt a WORM (Write Once Read Many) model
- It's lock policy at the whole bucket level.
- Create a Vault Lock Policy • Lock the policy for future edits (can no longer be changed or deleted)
- Helpful for compliance and data retention.

---
## S3 Object Lock (versioning must be enabled)

- Adopt a WORM (Write Once Read Many) model
- Block an object version deletion for a specified amount of time

### Retention mode - Compliance:

- Object versions can't be overwritten or deleted by any user, including the root user
- Objects retention modes can't be changed, and retention periods can't be shortened

### Retention mode - Governance:
- Most users can't overwrite or delete an object version or alter its lock settings 
- Some users have special permissions to change the retention or delete the object

### Retention Period: 

- protect the object for a fixed period, it can be extended 

### Legal Hold: 

- protect the object indefinitely, independent from retention period
- can be freely placed and removed using the s3:PutObjectLegalHold IAM permission
