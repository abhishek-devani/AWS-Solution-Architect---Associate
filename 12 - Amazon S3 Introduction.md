# 12 - Amazon S3 Introduction

## S3 Overview

### Section Introduction

- Amazon S3 is one of the main building blocks of AWS.
- It's advertised as `Infinitely Scaling` storage.

### Amazon S3 Use Cases

- Backup and Storage
- Disaster Recovery
- Archive
- Hybrid Cloud Storage
- Application Hosting
- Media Hosting
- Data lakes & big data analytics
- Delivering Software Updates
- Hosting Static Website

### Amazon S3 - Buckets

- It allows people to store objects (files) in `buckets` (directories).
- Buckets must have globally unique name.
- Buckets are defined at region level.

#### Naming Convention
- No Uppercase, No Underscore
- 3-63 character long
- Not an IP
- Must start with lowercase letter or number
- Must not start with xn--
- Must not end with -s3alias

### Amazon S3 - Objects

- Objects (files) have a key
- The key is the full path:
    - s3://my-bucket/**my_file.txt**
    - s3://my-bucket/**my_folder1/another_folder/my_file.txt**
- The key is composed of `prefix` + `object name`
    - s3://my-bucket/`my_folder1/another_folder/` `my_file.txt`
- There's no concept of `directories` within buckets.

#### Object values are the content of the body
- Max. Object size is 5TB (5000GB).
- If uploading more than 5GB, must use **multi-part upload**.
 
---
## Amazon S3 - Security

### User-Based

#### IAM Policies
- Which API calls should be allowed for a specific user from IAM.

### Resource-Based

#### Bucket Policies
- Bucket wide rules from the S3 console - allows cross account
- Most common

#### Object Access Control List (ACL)
- finer grain (can be disabled)

#### Bucket Access Control List (ACL)
- less common (can be disabled)

> **`Notes`**
> - **An IAM principal can access an S3 if**
> - **The user IAM permissions ALLOW it OR the resource policy ALLOWS it**
> - **AND there's no explicit DENY**

### Encryption

- Encrypt objects in S3 using encryption keys

---
## S3 Bucket Policies

```json
{
    "Version": "2012-10-17",
    "Id": "PutObjPolicy",
    "Statement": [
        {
            "Sid": "Public Read",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:GetObject",
            ],
            "Resource": [
                "arn:aws:s3:::examplebucket/*",
            ]
        }
    ]
}
```

### JSON Based Policy

#### `Resources:` buckets & objects
- It tells policy what `buckets` and `objects` this policy applies on.
- Ex. "arn:aws:s3:::examplebucket/*"
- This ex. applies to every object within the examplebucket.

#### `Effect:` Allow / Deny
- We allow or deny actions

#### `Actions:` Set of API to Allow / Deny
- Define API which we want to Allow or Deny

#### `Principal:` The account or user to apply the policy to
- we can use * to allow/deny anyone

### Use S3 bucket for policy to

- Grant public access to the bucket
- Force objects to be encrypted at upload
- Grant access to another account (Cross Account)

---
## Amazon S3 - Static Website Hosting

- S3 can host static websites and have them accessible on the internet
- The website URL will be depending on the region
- Ex. http://**<bucket-name>**.s3-website`-`**<aws-region-name>**.amazonaws.com
- Ex. http://**<bucket-name>**.s3-website`.`**<aws-region-name>**.amazonaws.com

---
## Amazon S3 - Versioning

- You can version your file into S3
- It is enabled at the bucket level
- It will protect against unintended deletes (ability to restore version)
- Easy roll back to previous version

> **`Notes`**
> - **Any file that is not versioned prior to enabling versioning will have the version `null`**
> - **Suspending versioning does not delete the previous versioning**

---
## Amazon S3 - Replication (CRR & SRR)

- **`CRR:` Cross-Region Replication**
- **`SRR:` Same-Region Replication**
- If we have two different S3 Bucket in 2 diff region and we want to setup asynchronous replication between them
- To do so we first must enable versioning in source and destination buckets.
- Buckets can be in different AWS accounts.
- Copying is asynchronous
- Must give proper IAM permission to S3.
- Only delete markers are replicated not delete (permanently delete).

### Use Cases

#### CRR
- Compliance
- Lower latency access
- replication across accounts

#### SRR
- Log aggregation
- Live replication between production and test accounts

> **`Notes`**
> - **After you enable Replication, only new objects are replicated.**
> - **Optionally, you can replicate existing objects using S3 Batch Replication**
> - **There is no chaining of replication**
>   - **If bucket 1 has replication in bucket 2, which has replication on bucket 3**
>   - **Then objects created in bucket 1 are not replicated to bucket 3.**

---
## S3 Storage Classes

### Amazon S3 Standard - General Purpose

- Used for frequently accessed data
- Low latency and high throughput
- Sustain 2 concurrent facility failures

#### Use Cases
- Big Data Analytics
- Mobile & Gaming Applications
- Content Distribution

### Amazon S3 Standard - Infrequent Access (IA)

- For data is less frequently accessed, but require rapid access when needed
- Lower Cost than S3 standard but you will have a cost on retrieval 

#### Use Cases
- Disaster Recovery and backups

### Amazon S3 One Zone - Infrequent Access

- High durability (99.9999999999%) in a single AZ
- Data lost when AZ is destroyed

#### Use Cases
- Storing secondary backup copies of on-premise data, or data you can recreate

### Amazon S3 Glacier Storage Class

- Low-cost Object Storage meant for archiving / backup
- `Pricing:` price for storage + object retrieval cost

#### Amazon S3 Glacier Instant Retrieval
- Millisecond retrieval, great for data accessed once a quarter
- Minimum storage duration of 90 days.

#### Amazon S3 Glacier Flexible Retrieval
- **Expedited (1 to 5 minutes)**
- **Standard (3 to 5 hours)**
- **Bulk (5 to 12 hours) - free**
- Minimum storage duration of 90 days.

#### Amazon S3 Glacier Deep Archive - for long term storage
- **Standard (12 hours)**
- **Bulk (48 hours)**
- Minimum storage duration of 180 days.

### Amazon S3 Intelligent Tiering

- Small monthly monitoring and auto-tiering free
- Moves objects automatically between Access Tiers based on usage
- Small monthly monitoring fee and auto tiering fee
- No Retrieval charges 