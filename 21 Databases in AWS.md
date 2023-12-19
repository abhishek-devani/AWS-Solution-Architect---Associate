# Databases in AWS

## Amazon RDS – Summary

- Managed **PostgreSQL / MySQL / Oracle / SQL Server / MariaDB / Custom**
- Support for **Read Replicas**
- **Automated Backup** with **Point in time restore** feature (**up to 35 days**)
- **Manual DB Snapshot** for longer-term recovery
- RDS Custom for access to and **customize** the underlying **instance** **(Oracle & SQL Server)**

### Use Cases

- Store relational datasets (RDBMS / **OLTP**)
- perform SQL queries
- transactions

---
## Amazon Aurora – Summary

- Compatible API for **PostgreSQL / MySQL**, **separation of storage and compute**
<!-- - Storage: data is stored in 6 replicas, across 3 AZ – highly available, self-healing, auto-scaling  -->
- **Custom endpoints** for writer and reader DB instances
- **Aurora Serverless** – for **unpredictable / intermittent workloads**, no capacity planning
- **Aurora Multi-Master** – for continuous writes failover (**high write availability**)
- **Aurora Global:** up to 16 DB Read Instances in each region, **< 1 second storage replication** 
- **Aurora Machine Learning:** perform **ML** using **SageMaker** & Comprehend on Aurora
- **Aurora Database Cloning:** new cluster from existing one, **faster than restoring a snapshot** 

### Use Case

- same as RDS, but with less maintenance / more flexibility / more performance / more features

---
## Amazon ElastiCache – Summary

- Managed **Redis / Memcached** (similar offering as RDS, but **for caches**)
- **In-memory data store**, sub-millisecond latency
- Support for **Clustering (Redis)**
- Requires **some application code changes** to be leveraged
  
### Use Cases

- Key/Value store
- Frequent reads, less writes
- cache results for DB

---
## Amazon DynamoDB – Summary

- **Serverless NoSQL database**
- **provisioned capacity** or **on-demand capacity**
- **Can replace ElastiCache** as a key/value store (**storing session data** for example, using TTL feature)
- **DAX cluster for read cache** 
- DynamoDB Streams to **integrate** with **AWS Lambda**, or **Kinesis Data Streams** 
- **Automated backups up to 35 days** with PITR (restore to new table), or **on-demand backups**
- **Export to S3 without using RCU (Read Capacity Unit)** within the PITR window, **import from S3 without using WCU (Write Capacity Unit)**
- Great to rapidly evolve schemas
  
### Use Case

- Serverless applications development (small documents 100s KB)
- distributed serverless

---
## Amazon S3 – Summary

- S3 is a **key / value store** for objects (where the key is the full path of the object in the bucket)
- Great for bigger objects, **not so great for many small objects**
- Serverless, scales infinitely, max object size is 5 TB, versioning capability
- **Batch operations** on objects using S3 Batch, **listing files using S3 Inventory**
- Multi-part upload, S3 Transfer Acceleration, S3 Select
  
### Use Cases

- static files
- key value store for big files
- website hosting

---
## DocumentDB

- DocumentDB is an **AWS-implementation** of **MongoDB**
- MongoDB is **used to store**, query, and index **JSON data**
- DocumentDB storage **automatically grows** in **increments of 10GB, up to 64 TB**. 

---
## Amazon Neptune

- Fully managed **graph** database
- A popular graph dataset would be a **social network (Social Networking Sites)**
  - Users have friends
  - Posts have comments
  - Comments have likes from users
  - Users share and like posts… 
- Can **store** up to **billions of relations** and query the graph with milliseconds latency

### Use Cases
- Great for knowledge graphs (Wikipedia)
- fraud detection
- recommendation engines
- social networking

---
## Amazon Keyspaces (for Apache Cassandra)

- **For Apache Cassandra**
- **Apache Cassandra** is an **open-source NoSQL distributed database**
- Using the **Cassandra Query Language (CQL)**

### Use Cases

- Store IoT devices info
- Time-series data

---
## Amazon QLDB

- QLDB stands for **Quantum Ledger Database**
- A ledger is a book recording **financial transactions**
- Used to **review history of all the changes made to your application data** over time 
- **Immutable** system: no entry can be removed or modified, cryptographically verifiable
- 2-3x better performance than common ledger blockchain frameworks, manipulate data using SQL
- Difference with Amazon Managed Blockchain: **no decentralization component**, in accordance with financial regulation rules

---
## Amazon Timestream

- serverless **time series database**
- Store and **analyze trillions of events per day**
- 1000s times faster & 1/10th the cost of relational databases
- Scheduled queries, multi-measure records, SQL compatibility
- Built-in time series analytics functions (helps you **identify patterns in your data in near real-time**)
  
### Use Cases

- IoT apps
- Operational applications
- Real-time analytics
