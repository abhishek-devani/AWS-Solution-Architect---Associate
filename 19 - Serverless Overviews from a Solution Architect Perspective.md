# 19 - Serverless Overviews from a Solution Architect Perspective

## What is Serverless?

- Serverless is a new paradigm in which the developers don’t have to manage servers anymore…
- They just deploy code
- Serverless was pioneered by AWS Lambda but now also includes anything that’s managed: “databases, messaging, storage, etc.
- **Serverless does not mean there are no servers…** it means you just don’t manage / provision / see them

---
## Serverless Services in AWS

- AWS Lambda
- DynamoDB
- AWS Cognito
- AWS API Gateway
- Amazon S3
- AWS SNS & SQS
- AWS Kinesis Data Firehouse
- Aurora Serverless
- Step Functions
- Fargate

---
## AWS Lambda

### Why AWS Lambda

- **Virtual functions** – no servers to manage!
- Limited by time - **short executions**
- Run **on-demand**
- Scaling is **automated!**

### Benefits of AWS Lambda

- Easy Pricing:
  - Pay per request and compute time
  - Free tier of 1,000,000 AWS Lambda requests and 400,000 GBs of compute time
- Integrated with the whole AWS suite of services
- Integrated with many programming languages
- Easy monitoring through AWS CloudWatch
- Easy to get more resources per functions (up to 10GB of RAM!)
- Increasing RAM will also improve CPU and network

### AWS Lambda Limits to Know - per region

#### Execution:
- **Memory allocation:** 128 MB – 10GB (1 MB increments)
- **Maximum execution time:** 900 seconds (15 minutes)
- **Environment variables:** (4 KB)
- **Disk capacity in the “function container” (in /tmp):** 512 MB to 10GB 
- **Concurrency executions:** 1000 (can be increased)

#### Deployment:
- **Lambda function deployment size (compressed .zip):** 50 MB
- **Size of uncompressed deployment (code + dependencies):** 250 MB
- Can use the /tmp directory to load other files at startup
- **Size of environment variables:** 4 KB

### Customization At The Edge

- Many modern applications execute some form of the logic at the edge
- **Edge Function:** 
    - A code that you write and attach to CloudFront distributions
    - Runs close to your users to minimize latency
- **Use Case:** customize the CDN content
- **CloudFront provides two types:** CloudFront Functions & Lambda@Edge

### CloudFront Functions vs Lambda@Edge

|        |    CloudFront Functions    |    Lambda@Edge    |
|--------|--------|--------|
|    Runtime Support    |    JavaScript    |    Node.js, Python    |
|    #  of Requests    |    Millions of requests per second    |    Thousands of requests per second    |
|    CloudFront Triggers    |    - Viewer Request/Response    |    - Viewer Request/Response<br> - Origin Request/Response    |
|    Max. Execution Time    |    < 1 ms    |    5 – 10 seconds    |
|    Max. Memory    |    2 MB    |    128 MB up to 10 GB    |
|    Total Package Size    |    10 KB    |    1 MB – 50 MB    |
|    Network Access, File System Access    |    No    |    Yes    |
|    Access to the Request Body    |    No    |    Yes    |

### CloudFront Functions vs Lambda@Edge  - Use Cases

|    CloudFront Functions    |    Lambda@Edge    |
|--------|--------|
|    Cache key normalization <br> - Transform request attributes (headers, cookies, query strings, URL) to create an optimal Cache Key    |    Longer execution time (several ms)    |
|    Header manipulation <br> - Insert/modify/delete HTTP headers in the request or response    |    Adjustable CPU or memory    |
|    URL rewrites or redirects    |    Your code depends on a 3rd libraries (e.g., AWS SDK to access other AWS services)    |
|    Request authentication & authorization <br> - Create and validate user-generated tokens (e.g., JWT) to allow/deny requests    | Network access to use external services for processing    |
|     |    File system access or access to the body of HTTP requests    |

### Lambda in VPC

- By default, your **Lambda** function is **launched outside your own VPC** (in an AWS-owned VPC)
- Therefore, it **cannot access resources in your VPC** (RDS, ElastiCache, internal ELB…)
- To Use lambda in VPC you must define the VPC ID, the Subnets and the Security Groups
- Lambda will create an ENI (Elastic Network Interface) in your subnets
- For RDS, The Lambda function must be deployed in your VPC, because **RDS Proxy is never publicly accessible**

---
## Amazon DynamoDB

### Amazon DynamoDB - Basics

- DynamoDB is made of **Tables**
- Each table can have an infinite number of items (= rows)
- Each item has attributes (can be added over time – can be null)
- **Maximum size of an item is 400KB**
- Data types supported are:
  - **Scalar Types –** String, Number, Binary, Boolean, Null
  - **Document Types –** List, Map
  - **Set Types –** String Set, Number Set, Binary Set
- **Therefore, in DynamoDB you can rapidly evolve schemas**

### DynamoDB – Read/Write Capacity Modes

- Control how you manage your table’s capacity (read/write throughput)

#### Provisioned Mode (default)
- You specify the number of reads/writes per second
- You need to plan capacity beforehand

#### On-Demand Mode
- Read/writes automatically scale up/down with your workloads
- No capacity planning needed
- Great for **unpredictable** workloads, **steep sudden spikes**

### DynamoDB Accelerator (DAX)

- Fully-managed, highly available, seamless in-memory cache for DynamoDB
- **Help solve read congestion by caching**
- **Microseconds latency for cached data**
- 5 minutes TTL for cache (default)

### DynamoDB –Time To Live (TTL)

- Automatically delete items after an expiry timestamp

#### Use Cases
- reduce stored data by keeping only current items
- adhere to regulatory obligations
- web session handling

---
## AWS API Gateway

### Overview

- AWS Lambda + API Gateway: No infrastructure to manage
- Support for the WebSocket Protocol 
- Handle API versioning (v1, v2…) 
- Handle different environments (dev, test, prod…) 
- Handle security (Authentication and Authorization) 
- Create API keys, handle request throttling 
- Swagger / Open API import to quickly define APIs 
- Transform and validate requests and responses
- Generate SDK and API specifications 
- Cache API responses

### API Gateway – Integrations High Level

#### Lambda Function
- Invoke Lambda function
- Easy way to expose REST API backed by AWS Lambda

#### HTTP
- Expose HTTP endpoints in the backend 
- **Example:** internal HTTP API on premise, Application Load Balancer…
- Why? Add rate limiting, caching, user authentications, API keys, etc… 

#### AWS Service
- Expose any AWS API through the API Gateway
- **Example:** start an AWS Step Function workflow, post a message to SQS
- Why? Add authentication, deploy publicly, rate control…

### API Gateway - Endpoint Types

#### Edge-Optimized (default): For global clients
- Requests are routed through the CloudFront Edge locations (improves latency)
- The API Gateway still lives in only one region

#### Regional: 
- For clients within the same region
- Could manually combine with CloudFront (more control over the caching strategies and the distribution) 

#### Private: 
- Can only be accessed from your VPC using an interface VPC endpoint (ENI)
- Use a resource policy to define access

---
## AWS Step Functions

- Build serverless visual workflow to orchestrate your Lambda functions
- **Features:** sequence, parallel, conditions, timeouts, error handling, …
- Can integrate with EC2, ECS, On-premises servers, API Gateway, SQS queues, etc…
- Possibility of implementing human approval feature 
- **Use cases:** order fulfillment, data processing, web applications, any workflow

---
## Amazon Cognito

- Give users an identity to interact with our web or mobile application
- **Cognito vs IAM:** “hundreds of users”, ”mobile users”, “authenticate with SAML”

### Cognito User Pools:

- Sign in functionality for app users
- Integrate with API Gateway & Application Load Balancer

### Cognito Identity Pools (Federated Identity):

- Provide AWS credentials to users so they can access AWS resources directly
- Integrate with Cognito User Pools as an identity provider

