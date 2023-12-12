# 17 Decoupling Applications

## Introduction

- When we start deploying multiple applications, they will inevitably need to communicate with one another
- There are two patterns of application communication
  - Synchronous communications (application to application)
  - Asynchronous / Event based (application to queue to application)
- Synchronous between applications can be problematic if there are sudden spikes of traffic
  - What if you need to suddenly encode 1000 videos but usually it’s 10?
- In that case, it’s better to `decouple` your applications,
    - using SQS: queue model
    - using SNS: pub/sub model
    - using Kinesis: real-time streaming model

---
# Amazon SQS (Simple Queue Service)

## Amazon SQS - Standard Queue

- Fully managed service, used to `decouple applications`
- Can have duplicate messages (at least once delivery, occasionally)
-  Can have out of order messages (best effort ordering)
   -  It ensures that messages are generally delivered in the same order as they're sent.

#### Attributes

- Unlimited throughput, unlimited number of messages in queue
- Default retention of messages: 4 days, maximum of 14 days
- Low latency (<10 ms on publish and receive)
- Limitation of 256KB per message sent

### SQS – Producing Messages

- Produced to SQS using the SDK (SendMessage API)
- The message is persisted in SQS until a consumer deletes it

#### Example: send an order to be processed 
- Order id
- Customer id
- Any attributes you want

### SQS – Consuming Messages

- Consumers (running on EC2 instances, servers, or AWS Lambda)…
- Poll SQS for messages (receive up to 10 messages at a time)
- Process the messages (example: insert the message into an RDS database)
- Delete the messages using the DeleteMessage API

### Amazon SQS - Security

#### Encryption:
- In-flight encryption using HTTPS API
- At-rest encryption using KMS keys
- Client-side encryption if the client wants to perform encryption/decryption itself

#### Access Controls: 
- IAM policies to regulate access to the SQS API

#### SQS Access Policies (similar to S3 bucket policies)
- Useful for cross-account access to SQS queues
- Useful for allowing other services (SNS, S3…) to write to an SQS queue

### SQS – Message Visibility Timeout

- After a message is polled by a consumer, it becomes invisible to other consumers
- By default, the “message visibility timeout” is 30 seconds
- That means the message has 30 seconds to be processed
- After the message visibility timeout is over, the message is “visible” in SQS
- If a message is not processed within the visibility timeout, it will be processed twice
- A consumer could call the ChangeMessageVisibility API to get more time
- If visibility timeout is high (hours), and consumer crashes, re-processing will take time
- If visibility timeout is too low (seconds), we may get duplicates

### Amazon SQS - Long Polling

- When a consumer requests messages from the queue, it can optionally “wait” for messages to arrive if there are none in the queue.
- **LongPolling decreases the number of API calls made to SQS while increasing the efficiency and reducing latency of your application.**
- The wait time can be between 1 sec to 20 sec (20 sec preferable).
- Long polling can be enabled at the queue level or at the API level using `WaitTimeSeconds`

---
## Amazon SQS - FIFO

- name should be end with `.fifo`.
- Limited throughput: 300 msg/s without batching, 3000 msg/s with batches
- Exactly-once send capability (by removing duplicates).
- Messages are processed in order by the consumer.

---
# Amazon SNS (Simple Notification Service)

- The “event producer” only sends message to one SNS topic
- As many “event receivers” (subscriptions) as we want to listen to the SNS topic notifications
- Each subscriber to the topic will get all the messages (note: new feature to filter messages)
- Many AWS services can send data directly to SNS for notifications

## Amazon SNS - How to publish

### Topic Publish (using the SDK)

- Create a topic
- Create a subscription (or many)
- Publish to the topic

### Direct Publish (for mobile apps SDK)

- Create a platform application
- Create a platform endpoint
- Publish to the platform endpoint
- Works with Google GCM, Apple APNS, Amazon ADM…

### Amazon SNS – Security

#### Encryption:
- In-flight encryption using HTTPS API
- At-rest encryption using KMS keys
- Client-side encryption if the client wants to perform encryption/decryption itself

#### Access Controls: 
- IAM policies to regulate access to the SNS API

#### SNS Access Policies (similar to S3 bucket policies)
- Useful for cross-account access to SNS queues
- Useful for allowing other services (S3…) to write to an SNS queue

### SNS + SQS: Fan Out

- Push once in SNS, receive in all SQS queues that are subscribers
- Fully decoupled, no data loss
- `SQS allows for:` data persistence, delayed processing and retries of work
- Ability to add more SQS subscribers over time
- Make sure your SQS queue access policy allows for SNS to write
- `Cross-Region Delivery:` works with SQS Queues in other regions.

### Amazon SNS – FIFO Topic

- Similar features as SQS FIFO:
- `Ordering` by Message Group ID (all messages in the same group are ordered)
- `Deduplication` using a Deduplication ID or Content Based Deduplication
- `Can have SQS Standard and FIFO queues as subscribers`
- Limited throughput (same throughput as SQS FIFO)

### SNS – Message Filtering

- JSON policy used to filter messages sent to SNS topic’s subscriptions
- If a subscription doesn’t have a filter policy, it receives every message

---
# Kinesis Overview

- Makes it easy to `collect, process`, and `analyze` streaming data in real-time
- Ingest real-time data such as: Application logs, Metrics, Website clickstreams, IoT telemetry data…

- **`Kinesis Data Streams:`** capture, process, and store data streams
- **`Kinesis Data Firehose:`** load data streams into AWS data stores
- **`Kinesis Data Analytics:`** analyze data streams with SQL or Apache Flink
- **`Kinesis Video Streams:`** capture, process, and store video streams

### Kinesis Data Firehouse

- It takes data from Kinesis Data Streams, Amazon CloudWatch & Amazon IOT and writes it to the destinations without writing the code.
- It usually takes data from Kinesis Data Streams.
- Three Types of AWS Destinations
  - Amazon S3
  - Amazon Redshift (copy through S3)
  - Amazon OpenSearch
- There are many 3rd party destinations as well as Custom Destination.

### Kinesis Data Streams vs Firehose

|           Kinesis Data Streams                |           Kinesis Data Firehose             |
| --------------------------------------------- | ------------------------------------------- |
| Streaming service for ingest at scale         | Load streaming data into S3 / Redshift / OpenSearch / 3rd party / custom HTTP |
| Write custom code (producer / consumer)       | Fully managed                               |
| Real-time (~200 ms)                           | **Near real-time** (buffer time min. 60 sec)|
| Manage scaling (shard splitting / merging)    | Automatic scaling                           |
| Data storage for 1 to 365 days                | No data storage                             |
| Supports replay capability                    | Doesn’t support replay capability           |

### Kinesis vs SQS ordering

- **Let’s assume 100 trucks, 5 kinesis shards, 1 SQS FIFO**

#### Kinesis Data Streams:
- On average you’ll have 20 trucks per shard
- Trucks will have their data ordered within each shard
- The maximum amount of consumers in parallel we can have is 5 
- Can receive up to 5 MB/s of data

#### SQS FIFO
- You only have one SQS FIFO queue
- You will have 100 Group ID 
- You can have up to 100 Consumers (due to the 100 Group ID)
- You have up to 300 messages per second (or 3000 if using batching)

### SQS vs SNS vs Kinesis

| **SQS** | **SNS** | **Kinesis** |
| ------- | ------- | ----------- |
| Consumer “pull data” | Push data to many subscribers | Standard: pull data (2 MB per shard) |
| Data is deleted after being consumed | Up to 12,500,000 subscribers | Enhanced-fan out: push data (2 MB per shard per consumer) |
| Can have as many workers (consumers) as we want | Data is not persisted (lost if not delivered) | Possibility to replay data |
| No need to provision throughput | Pub/Sub | Meant for real-time big data, analytics and ETL |
| Ordering guarantees only on FIFO queues | Up to 100,000 topics | Ordering at the shard level |
| Individual message delay capability | No need to provision throughput | Data expires after X days (x: 1 to 365)
| | Integrates with SQS for fan- out architecture pattern | Provisioned mode or on-demand capacity mode |
| | FIFO capability for SQS FIFO | |

