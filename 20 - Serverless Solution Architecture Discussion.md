# 20 - Serverless Solution Architecture Discussion

## Mobile application: MyTodoList

### Requirements

- We want to create a mobile application with the following requirements
- Expose as REST API with HTTPS
- Serverless architecture
- Users should be able to directly interact with their own folder in S3
- Users should authenticate through a managed serverless service
- The users can write and read to-dos, but they mostly read them
- The database should scale, and have some high read throughput

### Architecture

- Serverless REST API: HTTPS, API Gateway, Lambda, DynamoDB
- Using Cognito to generate temporary credentials with STS (Security Token Service) to access 
  S3 bucket with restricted policy. App users can directly access AWS resources this way. 
  Pattern can be applied to DynamoDB, Lambda…
- Caching the reads on DynamoDB using DAX
- Caching the REST requests at the API Gateway level
- Security for authentication and authorization with Cognito, STS

---
## Serverless hosted website: MyBlog.com

### Requirements

- This website should scale globally 
- Blogs are rarely written, but often read
- Some of the website is purely static files, the rest is a dynamic REST API
- Caching must be implement where possible
- Any new users that subscribes should receive a welcome email
- Any photo uploaded to the blog should have a thumbnail generated

### Architecture

- We’ve seen static content being distributed using CloudFront with S3
- The REST API was serverless, didn’t need Cognito because public
- We leveraged a Global DynamoDB table to serve the data globally
- (we could have used Aurora Global Database)
- We enabled DynamoDB streams to trigger a Lambda function
- The lambda function had an IAM role which could use SES
- SES (Simple Email Service) was used to send emails in a serverless way 
- S3 can trigger SQS / SNS / Lambda to notify of events

---
## Software updates offloading

### Requirements

- We have an application running on EC2, that distributes software updates once in a while.
- When a new software update is out, we get a lot of request and the content 
  is distributed in mass over the network. It’s very costly.
- We don’t want to change our application, but want to optimize our cost and CPU, how can we do it?

### Architecture

- Use CloudFront. Why Cloudfront?
- No changes to architecture
- Will cache software update files at the edge
- Software update files are not dynamic, they’re static (never changing)
- Our EC2 instances aren’t serverless
- But CloudFront is, and will scale for us
- Our ASG will not scale as much, and we’ll save tremendously in EC2
- We’ll also save in availability, network bandwidth cost, etc
- Easy way to make an existing application more scalable and cheaper!

