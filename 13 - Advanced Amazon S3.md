# 13 - Advanced Amazon S3

## Amazon S3 - Moving Between Storage Classes

- You can transition objects between storage classes.
- Moving objects can be automated using a `Lifecycle Rules`

---
## Amazon S3 - Lifecycle Rules

- Rules can be created for a certain specific prefix (example: s3://mybucket/mp3/*)
- Rules can be created for certain object tags (example: Department: Finance)

### Transition Action

- Configure objects to transition to another storage class.
    - For Ex. Move objects to Standard IA class 60 days after creation.
    - For Ex. Move to Glacier for archiving after 6 months.

### Expiration Action

- Configure objects to expire (delete) after some time.
    - For Ex. Access log files can be set to delete after a 365 days.
    - For Ex. Can be used to delete old versions of files (if versioning is enabled).
    - For Ex. Can be used to delete incomplete Multi-Part uploads.

---
## Amazon S3 - Lifecycle Rules (Scenario 1)

-  Your application on EC2 creates images thumbnails after profile photos are uploaded to Amazon S3.These thumbnails can be easily recreated, and only need to be kept for 60 days. The source images should be able to be immediately retrieved for these 60 days, and afterwards, the user can wait up to 6 hours. How would you design this?

- S3 source images can be on Standard, with a lifecycle configuration to transition them to Glacier after 60 days.
- S3 thumbnails can be on One-Zone IA, with a lifecycle configuration to expire them (delete them) after 60 days.

---
## Amazon S3 - Lifecycle Rules (Scenario 2)

- A rule in your company states that you should be able to recover your deleted S3 objects immediately for 30 days, although this may happen rarely. After this time, and for up to 365 days, deleted objects should be recoverable within 48 hours.

- `Enable S3 Versioning` in order to have object versions, so that "deleted objects" are in fact hidden by a "delete marker" and can be recovered
- Transition the "noncurrent versions" of the object to `Standard IA`
- Transition afterwards the "noncurrent versions" to `Glacier Deep Archive`

---
## Amazon S3 Analytics - Storage Class Analysis

- Help you decide when to transition objects to the right storage class.
- Recommendations for `Standard` and `Standard IA`
    - Does NOT work for One-Zone IA or Glacier
- Report is updated daily
- 24 to 48 hours to start seeing data analysis

---
## Amazon S3 - Requester Pays

- In general, bucket owners pay for all Amazon s3 storage and data transfer costs associated with their bucket.
- `With Requester Pays buckets`, the requester instead of bucket owner pays the cost of the request and data download from the bucket.
- Helpful when you want to share large datasets with other accounts
- The requester must be authenticated in AWS (cannot by anonymous)

---
## Amazon S3 - Event Notifications

### What are events?

- Events are things such as `S3:ObjectCreated`, `S3:ObjectRemoved`, `S3:ObjectRestore`, `S3:Replication...` etc...
- We can filter this events
    - Ex. I only want to consider objects that ends with `.jpg`.
- Can create as many `S3 events` as desired.
- We can send this events to agents such as `SNS, SQS and Lambda Function`
- S3 event notifications typically deliver events in seconds but can sometimes take minute or longer.
- We have to use `SNS resource (access) policy, SQS resource (access) policy, Lambda resource policy` instead of `IAM Role` to send data to this targets.

#### Use Case
- To automatically react to certain events happening in S3.
    - Ex. You want to generate thumbnails of images uploaded to S3.
- We will create event notification for this.

---
## Amazon S3 - Baseline Performance

- S3 automatically scales to high request rates, latency 100-200 ms
- Your application can achieve at least `3,500 PUT/COPY/POST/DELETE or 5,500 GET/HEAD requests per second per prefix in a bucket`
- There is no limit of prefixes in a bucket.

### S3 Transfer Acceleration

- Increase transfer speed by transferring file to an AWS edge location which will forward the data to the S3 bucket in the target region.
- Compatible with multi-part upload.

### S3 Byte Range Fetches

- Parallelize GETs by requesting specific byte ranges.
- Better resilience in case of failures.
- Can be used to speed up downloads.

---
## S3 Select & Glacier Effect

- Retrieve less data using SQL by performing `server-side filtering`.
- Can filter by rows & columns (simple SQL statements)

---
## S3 Batch Operation

- Perform bulk operations on existing S3 objects with a simple request, For Ex.
    - Modify Object Metadata
    - Copy objects between s3 buckets
    - `Encrypt un-encrypted objects`
    - Modify ACLs, tags
    - Restore Objects from S3 Glacier
    - Invoke Lambda function to perform custom action on each object
- A job consists of a list of objects, the action to perform, and optional parameters
- S3 Batch Operations manages retries, tracks progress, send completion notifications, generate reports ...
- `You can use S3 inventory to get object list and use S3 Select to filter your objects`