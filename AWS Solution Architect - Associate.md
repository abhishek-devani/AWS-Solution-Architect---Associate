# AWS Solution Architect - Associate

## AWS Regions
- AWS has Regions all around the world.
- A Region is a cluster of data centers.
- Most AWS services are region-scoped.

---
## How to choose an AWS Region?
- `Compliance with data governance and legal requirements:` data never leaves a region without explicit permission.
- `Proximity to customers:` reduce latency
- `Availability Services within a region:` new services and new features aren’t available in every region
- `Pricing:` pricing varies region to region and is transparent in the service pricing page.

---
## AWS Availability Zones
- Each Region has many availability zones (Usually 3, min is 3, max is 6)
- Each availability zone has one or more discrete data centers with redundant power, networking and connectivity.
- They are separate from each other so that they are isolated from disasters.

---
## MFA device options in AWS
- Virtual MFA device
- Universal 2nd Factor (U2F) Security Key: Physical Device

---
## How can users access AWS?
- AWS Management Console (Protected by password)
- AWS Command Line Interface (AWS CLI): Protected by access keys
- AWS Software Development Kit (SDK) for code: protected by access keys

---
## What’s the AWS SDK?
- Language specific APIs (set of libraries)
- Enables you to access and manage AWS Services programmatically.
- Embedded within your application.
- Ex. AWS CLI is built on AWS SDK for python named boto.

---
## IAM Security Tools
- IAM Credentials Report (Account Level)
    - A report that lists all your account’s users and their status of the credentials.
- IAM Access Adviser (User Level)
    - Access Adviser shows the service permissions granted to a user and when those services were last accessed.
    - You can use this information to revise your policies.

---
## Elastic Compute Cloud (EC2) - Fundamentals

- It mainly consist in the capabilities of
    - Renting Virtual Machines (EC2)
    - Storing data on virtual drives (EBS)
    - Distributing load across machines (ELB)
    - Scaling the services using an auto-scaling groups (ASG)
- EC2 sizing and configuration options
    - `OS:` Linux, Windows or Mac OS
    - `CPU`
    - `RAM`
    - `Storage Space:`
        - Network - Attached (EBS & EFS)
        - Hardware (EC2 Instance Store)
    - `Network Card:` speed of the card, Public IP Address
    - `Firewall Rules:` Security Group
    - `Bootstrap Script:` EC2 User Data

> ### `Instance Types`

#### `General Purpose`
- Great for diversity of workloads such as web servers or code repositories.

#### `Compute Optimized`
- For high performance tasks such as
    - Batch processing workloads
    - Media transcoding
    - High performance web servers
    - High performance computing
    - Scientific modeling & machine learning
    - Dedicated gaming servers
- C names instances. Ex. c5, c6, c7 etc.

#### `Memory Optimized`
- Fast performance for workloads that process large data sets in memory
- Use cases:
    - High performance
    - Relations & Non-relational Databases
    - Web scale cache stores
    - In-memory database optimized for business intelligence (BI)
    - application is performing real time processing of big unstructured data.

#### `Storage Optimized`
- It’s for tasks that requires high, sequential read and write access to large data sets on local storage
- Use cases:
    - OLTP (Online transaction processing) systems
    - Relations & NoSQL Databases
    - Cache for in memory-databases (Ex. Redis)
    - Data warehousing applications
    - Distributed file systems

> ### `Security Groups`

- It is acting as a firewall on EC2 Instance.
- They Regulate
    - Access to the ports
    - Control of inbound and outbound network
- Ports to know
    - 22 = SSH 
    - 21 - FTP
    - 22 - SFTP
    - 80 - HTTP
    - 443 - HTTPS
    - RDP - (Remote Desktop Protocol)

> ### `EC2 Instance Types`

#### `On-Demand Instances` 
- short workload, predictable pricing, pay by second

#### `Reserved (1 & 3 Years)`
- `Reserved Instances:` long workloads
- `Convertible Reserved Instances:` long workloads with flexible instance (you can change the type of the instance)

#### `Savings Plan (1 & 3 Years)`
- commitment to an amount of usage, long workload

#### `Spot Instances` 
- short workloads, cheap, can lose instances (less reliable)
- Use Cases
    - Batch Jobs
    - Data analysis
    - Image processing
    - Any distributed workloads
    - Workloads with a flexible start and end time

#### `Dedicated Host` 
- book an entire physical server, control instance placement
Software Licenses

#### `Dedicated Instances` 
- no other customer will share your hardware but may share hardware with other instances in same account

#### `Capacity Reservation` 
- reserve capacity in a specific AZ for any duration

#### `Spot Fleets` 
- Set of Spot Instances + (Optional) On-Demand Instances

---
## EC2 - Solutions Architect Associate Level

> ### `Elastic IPs`

- When you start and stop instances, It can change its public IP.
- If you need to have a fixed public IP for your instance, you need an elastic IP.
- An Elastic IP is a public IPv4 IP you own as long as you don’t delete it.
- You can attach it to one instance at a time.
- You can have only 5 IP in your account (You can ask AWS to increase that)
- Overall, Try to avoid using Elastic IP:
    - They often reflect poor architectural decisions.
    - Instead, use a random public IP and register a DNS name to it.

> ### `Placement Groups`

#### `Cluster` 
- Same Hardware, Same AZ, Low latency, great network

#### `Spread` 
- Different Hardware, limited to 7 instances per AZ per placement group

#### `Partition`
- Instances distributed across many hardware racks, up to 7 partitions per AZ, same region.

> ### `Elastic Network Interface (ENI)`
- Logical component in a VPC that represents a Virtual Network Card.
- The ENI can have the following attributes
    - Primary private IPv4, one or more secondary IPv4
    - One elastic IP per private IPv4
    - One public IPv4
    - One or more security groups
    - A MAC address

> ### `EC2 Hibernate`
- In normal EC2 your application starts, caches get warmed up, and that can take time.
- In EC2 Hibernate
    - The in-memory (RAM) state is preserved.
    - The Instance boot is much faster. (The OS is not stopped / restarted).
    - Under the Hood: the RAM state is written to a file on the root EBS volume.
- An Instance CAN NOT be hibernated more than 60 days.

> ### `Use Cases`

- Long Running Processing
- Saving the RAM State
- Services that take time to initialize

---
## EC2 Instance Storage

> ### `EBS (Elastic Block Storage)`

- EBS is a `network drive` you can attach to your instance while they run.
- It allows your instance to `persist your data even after termination`.
- It has a `multi-attach` feature for some EBS. you can attach two EBS to one instance.
- `Bound to specific AZ.`
- Think of them as a `network USB stick`.
- `To move` an EBS volume `to another AZs`, you first need to `snapshot it`.

> ### `EBS Snapshot`

- `Make a backup` of your EBS volume at a point in time.
- `Can copy snapshots across AZ or Region.`
 
#### `EBS Snapshot Features`

- EBS Snapshot Archive
    - Move the snapshot to `achieve tier` which is `75% cheaper`.
    - Takes within `24 to 72 hrs` for `restoring the archive`.
- Recycle Bin for EBS Snapshots
    - Setup rules to retain deleted snapshots so you can recover them after an accidental deletion.
    - Specify retention (from 1 day to 1 year).
- Fast Snapshot Retention (FSR)
    - Force full initialization of snapshot to have no latency on the 1st use.

> ### `AMI (Amazon Machine Image)`
- AMI is a `customization of an EC2 instance`.
    - We can add our own software, configuration, OS, monitoring…
    - Faster boot/configuration time because all your software is pre-packaged.
- AMI is `built for specific regions` (and `can be copied across regions`).

> ### `EC2 Instance Storage`
- Used to have better I/O performance.
- It lose their storage if they’re stopped
- `Use Case`
    - Good for buffer / cache / scratch data / temporary content.

> ### EBS Volume Types

#### gp2 / gp3 (SSD)
- general purpose that balances price and performance.

#### io 1 / io 2 (SSD)
- Highest-performance for mission critical, low latency or high-throughput workloads.

#### st 1 (HDD)
- low cost volume for frequently accessed, throughput-intensive workloads.

#### sc 1 (HDD)
- Lowest cost volume designed for less frequently accessed workloads.

> Note: Only gp2 / gp3 and io1 / io2 can be used as boot volumes.

> ### `EBS Multi Attach - io 1 / io 2 family`

- Attach the same EBS volume to multiple EC2 instances in the same AZ.
- `Max 16 instances can attach` to the `same EBS`.
- `Use Cases:`
    - Higher application availability in clustered Linux applications (Ex. Teradata)
    - Applications must manage concurrent write operations.

> ### `EBS Encryption`

- Encryption has a minimal impact on latency
- **to Encrypt an unencrypted EBS Volume**
     - Create an EBS snapshot of the volume.
     - Encrypt the EBS snapshot (using copy).

> ### `EFS (Elastic File System)`

- Managed NFS (network file system) that can be mounted on many EC2.
- EFS works with EC2 instances in multi-AZ.
- Compatible with linux based AMI (not windows).
- **Storage Tiers:**
    - **Standard:** for frequently accessed files.
    - **Infrequent Access (EFS-IA):** cost to retrieve files, lower price to store.

---
## High Availability and Scalability: ELB & ASG

> ### `High Availability and Scalability`

- Scalability means that an application / system can handle greater loads by adapting.

> ### `Vertical Scalability`

- It means **increasing the size** of the instance.
- For Ex. application runs on a **t2.micro** and scaling that application vertically means running it on **t2.large**.
- It is **very common** for non distributed systems, such as a **database**.
- **RDS** and Elasticache are services that can scale vertically.

> ### `Horizontal Scalability`

- It means **increasing the number of instances** / systems for your application.
- It implies **distributed systems**.
- This is **very common for web applications** / modern applications.

> ### `High Availability`

- It usually goes **hand in hand with horizontal scaling**.
- It means running your **application** / system in **at least 2 data centers** (== AZ)
- The **goal** for High Availability is to **survive data center loss**.

---
## Load Balancing

> ### `What is load balancing?`

- Load Balances are servers that forward traffic to multiple servers (For ex. EC2 instance) downstream.

> ### `ELB (Elastic Load Balancer)`

Spread load across multiple downstream instances.
Expose a single point of access (DNS) to your application.
Seamlessly handle failures of downstream instances.
Health checks are crucial for Load Balancers.
They enable the load balancer to know if instances it forward traffic to are available to reply on request.
To get the client's IP address, ALB adds an additional header called "X-Forwarded-For" containing the client's IP address.

> ### `Types of Load Balancer on AWS`

Classic Load Balancer: (v1 - old generation)
Application Load Balancer: (v2 - new generation) - 2016 - ALB
HTTP, HTTPS, WebSocket	
Network  Load Balancer: (v2 - new generation) - 2017 - NLB
TCP, TLS (secure TCP), UDP
Gateway Load Balancer - 2020 - GWLB
Operates at layer 3 (network layer) - IP Protocol

> ### `Application Load Balancer (ALB)`

ALB is layer 7 (HTTP)
Supports redirects (Ex. from HTTP to HTTPS)
Routing tables to different target groups:
Routing based on path in URL (abc.com/users & abc.com/posts)
Routing based on hostname in URL (one.abc.com & two.abc.com)
Routing based on Query String, Headers (abc.com/users?id=123&order=false)
ALB are a great fit for micro services & container-based application such as Docker and Amazon ECS
Latency: 400 ms

> ### `Network Load Balancer (NLB)`

It (Layer 4) allow to:
Forward TCP & UDP traffic to your instance.
Handle millions of requests per seconds.
Latency: 100 ms
NLB has one static IP per AZ, and supports assigning Elastic IP.

> ### `Gateway Load Balancer (GWLB)`

Deploy, scale and manage a fleet of 3rd party network virtual appliances in AWS.
It analyzes the network traffic before sending it to the application.
Operates at Layer - 3 (Network Layer) - IP Packets
Uses the GENEVE protocol on 6081.

> ### `Sticky Sessions (Session Affinity)`

It is possible to implement stickiness so that the same client is always redirected to the same instance behind a load balancer.
Works for Classic Load Balancer, ALB and NLB (works without cookies).
The cookie used for stickiness has an expiration date you control.
Use Case: make sure the user doesn’t lose his session data.

> ### `Cookie Names`

Application-based Cookies
Custom Cookie
Generated by the target.
Can include any custom attributes required by the application.
Don’t use AWSALB, AWSALBAPP or AWSALBTG (reserved for use by the ELB).
Application Cookie
Generated by the load balancer.
The cookie name is AWSALBAPP.
Duration-based Cookies
Generated by the load balancer.
The Names are AWSALB for ALB and AWSELB for CLB.

> ### `Cross-Zone Load Balancing`

Each load balancer instance distributes evenly across all registered instances in all AZ.
In ALB it is enabled by default (can be disabled at the Target Group Level).
No charges for inter AZ data.
In GWLB & NLB disabled by default.
Pay charges for inter AZ data if enabled.

> ### `SSL (Secure Socket Layer) / TLS (Transport Layer Security) - Basics`

An SSL certificate allows traffic between your clients and your load balancer to be encrypted in transit (in-flight transaction).
HTTPs listeners:
You must specify a default certificate
You can add an optional list of certs to support multiple domains
Clients can use SNI (Server Name Indication) to specify the hostname they reach.

> ### `SNI (Server Name Indication)`

SNI solves the problem of loading multiple SSL certificates on one web server (to serve multiple websites).
It’s a “newer” protocol, and requires the client to indicate the hostname of the target server in the initial SSL handshake.
The server will then find the correct certificate, or return the default one.
Only works for ALB & NLB (newer generation) and CloudFront.

> ### `Connection Draining (CLB) / Deregistration Delay (ALB & NLB)`

It will give time to complete “in-flight request” while the instance is deregistering or unhealthy.
Stops sending new req to the EC2 which is de-registering.
Between 1 to 3600 secs (default 300 sec).

---
## Auto Scaling Groups

Scale out (add EC2 Instance) to match an increased load.
Scale in (remove EC2 Instance) to match an decreased load.
We can scale in and scale out based on CloudWatch alarms.

> ### `ASG - Dynamic Scaling Policy`

Target Tracking Scaling
Most simple and easy to set up
Ex. I want the average ASG CPU to stay at around 40%.
Simple / Step Calling
When a CloudWatch alarm is triggered (Ex. CPU > 70%), then add 2 units.
When a CloudWatch alarm is triggered (Ex. CPU < 30%), then remove 1 unit.
Scheduled Action
Anticipate a scaling based on known usage pattern
Ex. Increase the min capacity to 10 at 5 pm on Fridays.

> ### `Predictive Scaling`

- Continuously forecast load and schedule scaling ahead.

> ### `Good metrics to scale on`

CPUUtilization
RequestCountPerTarget
Average Network In / Out
Any custom metric (that you push using CloudWatch)

> ### `Scaling Cooldowns`

After a scaling activity happens, you are in the cooldown period (default 300 sec)
During the cooldown period, the ASG will not launch or terminate additional instances (to allow for metrics to stabilize).
Advice: Use ready-to-use AMI to reduce configuration time in order to be serving request fasters and reduce the cooldown period.

---
## AWS Fundamentals: RDS + Aurora + Elasticache

> ### `RDS (Relational Database)`

It's a managed DB service for DB which uses SQL as a query language.
It allows you to create databases in the cloud that are managed by AWS.
Postgres
MySQL
MariaDB
Oracle
Microsoft SQL Server
Arora

Advantages Over Using RDS vs deploying DB on EC2
RDS is a managed service:
Automated provisioning, OS patching
Continuous backups and restore to specific timestamp (Point in Time Restore)
Monitoring dashboard
Read replicas for improved read performance
Multi AZ setup for DR (Disaster Recovery)
Maintenance windows for upgrades
Scaling capability (vertical and horizontal)
Storage backed by EBS
You can’t SSH into your instance

RDS - Storage Auto Scaling
Help you increase storage on your RDS DB instance dynamically automatically.
You have to set Maximum Storage Threshold (maximum storage for DB storage).
Automatically modify storage if:
Free storage < 10% of allocated storage.
Low-storage lasts at least 5 minutes.
6 hours have passed since last modification

RDS Read Replicas for Read Scalability
Up to 15 Read Replicas
Within AZ, Cross Az, or Cross region
Replicas can be promoted to their own DB
No fees for the same region but diff AZ.

RDS - From Single AZ to Multi AZ
Zero downtime operation (no need to stop the db)
Just click on “Modify” for the database.

RDS - Custom
Only for Oracle and Microsoft SQl Server. 
Access to the underlying database and OS so you can
Configure settings
Install patches
Enable native features
Access the underlying EC2 using SSH or SSM session manager
RDS vs RDS Custom
RDS: entire db and OS to be managed by AWS
RDS Custom: full admin access to the underlying OS and DB.

Amazon RDS Proxy
Reduced RDS & Aurora fail over time by 66%.
RDS proxy is never publicly available (must be accessed from VPC).

> ### `Aurora`

Proprietary technology from AWS.
Claims 5x performance improvement over MySQL.
Storage automatically grows in increments of 10GB, up to 128 TB.
Can have up to 15 replicas.
Creates 6 copies of your data across 3 AZ
4/6 needed for writes
3/6 needed for reads
Automated failover for master in less than 30 secs.
Master + up to 15 read replicas

Writer Endpoint
It's DNS. Always pointing to the master.

Reader Endpoint
It helps with connection load balancing of read replicas.

Features of Aurora
Automatic fail-over
Backup and Recovery
Isolation and Recovery
Industry compliance
Push-button scaling
Automated patching with Zero Downtime
Advanced Monitoring
Routine Maintenance
Backtrack: restore data at any point of time without using backups.

Aurora Custom Endpoint
When you have different size of instance in read replicas you can create Custom Endpoint
The reader endpoint is generally not used after defining Custom Endpoints.

Aurora Serverless
Automated database instantiation and auto-scaling based on actual usage.
Good for unpredictable workload.
No capacity planning needed

Aurora Multi-Master
In case you want continuous write availability for the writer nodes.

Global Aurora
1 primary region (read / write)
Up to 5 secondary region (read-only)
Replication leg is less than 1 second.
Promoting another region for read/write has an <1 mnt of replication time.

> ### `RDS vs Aurora Backup`

We can disable backup in RDS but can’t disable in Aurora.
In RDS it has ability to restore to any point in time (from an oldest backup to to 5 minutes ago)

> ### `Amazon ElastiCache`

It is to get managed Redis or Memcached
Caches are in-memory dbs.
Helps make your application stateless.
Using elasticache involves heavy application code changes.
Redis Sorted Set guarantee both uniqueness and element ordering (most used in games for ranks)

---
## Route 53

Route 53 is a Domain Registrar of AWS
http://api.www.example.com
.com: Top Level Domain
example.com: Second Level Domain
www.example.com: Sub Domain
api.www.example.com: Fully Qualified Domain Name
http://api.www.example.com: URL
http: Protocol
53 is a port used by DNS service hence service name is Route 53.

> ### `Route 53 - Record Types`

A - maps a hostname to IPv4.
AAAA - maps a hostname to IPv6.
CNAME - maps a host name to another hostname.
Can’t create for example.com but you can create for www.example.com
NS - Name Servers for the Hosted Zone.
Control how traffic is routed for a domain.

> ### `Route 53 - Hosted Zones`

A container for records that define how to route traffic to a domain and its subdomains.

Public Hosted Zone: contain records that specify how to route traffic on the internet (public domain names)

Private Hosted Zone: contain records that specify how to route traffic within one or more VPCs (private domain names)

> ### `Route 53 - TTL (Time to Live)`

Cached data for a specific time period.
High TTL - e.g., 24 hr
Less traffic on Route 53
Possibly outdated record
Low TTL - e.g., 60 sec
More traffic on Route 53
Records are outdated for less time
Easy to change records
Except for alias records, TTL is mandatory for each DNS record.

> ### `CNAME vs Alias`

CNAME: Points a hostname to any other hostname (app.mydomain.com → blabla.anything.com)
Only for Non Root Domain (Ex. something.mydomain.com)
Alias: Points a hostname to an AWS Resource (app.mydomain.com → blabla.amazonaws.com)
Works for Root and Non Root Domain (Ex. mydomain.com)
Free of charge
Native health check capability
We can not set an ALIAS record for an EC2 DNS name.

> ### `Route 53 - Routing Policies`

It is used to manage client’s request.
Types:
Simple
Typically, route traffic to a single resource
Can specify multiple values in the same record
If multiple values are returned, a random one is chosen by the client.
Can’t be associated with health checks
Weighted
Control the % of the requests that go to each specific resource.
DNS records must have the same name and type.
Can be associated with health checks.
Total weight can be less/more than 100%.
Failover
It checks the health check and based on that it will send req to the resources.
If the primary resource fails with health checks then the request will be forwarded to the secondary resource.
Latency Based
Redirected to the resource that has the least latency close to us.
Super helpful when latency for users is a priority.
Users will be redirected to the nearest possible resource.
Geolocation
Different from Latency-based!
This routing is based on user location.
We have to specify location by continent or country (if there is overlapping, the most precise location selected).
Should create a default record 
Use cases: website localization, restrict content distribution, load balancing etc.
Multi-Value
Use when we want to routing traffic to multiple resources
Can be associated with health checks
Up to 8 healthy records are returned for each Multi-Value query
Geo Proximity (using Route 53 Traffic Flow feature)
Route traffic to your resource based on geographic location of users and resources
Ability to shift more traffic to resources based on defined bias.
To change the size of the geographic region, specify bias values:
To expand (1 to 99) - more traffic to resource
To shrink (-1 to -99) - less traffic to resource
Geo Proximity Routing is really helpful when you need to shift traffic from one region to another, by increasing the bias.
IP-based Routing
You provide a list of CIDRs (set of IP) for your clients and the corresponding endpoint/locations (user-IP-to-endpoint mappings)
Use Cases: Optimize performance, reduce network costs

> ### `Route 53 - Health Checks`

Monitor an Endpoint
About 15 health checker will send the request to our public resource to check health
Healthy/Unhealthy Threshold - 3 (Default)
Interval 30 sec (can set to 10 - higher cost)
If > 18% health checker says its healthy then route 53 will consider it as healthy
Calculated Health Checks
Combine the result of multiple health checks into a single.
We can health check the status of other health checks combined.
You can use OR, AND or NOT.
Can monitor up to 256 health checks
Usage: perform maintenance to your website without causing all health checks to fail.
Private Hosted Zones
Route 53 health checkers are outside the VPC
They can’t access private endpoints (private VPC or on-premise resources)
You can create a CloudWatch Metric and associate a CloudWatch Alarm then create a health check that checks the alarm itself.

> ### `3rd Party Registrar with Amazon Route 53`

We can purchase our domain from any 3rd party registrar like goDaddy and still we can use Route 53 to manage it.
We just have to create a public hosted zone in route 53
Update NS (name server) Records on 3rd party website to use Route 53 Name Servers
Domain Registrar != DNS Service
But every domain registrar usually comes with some DNS features


