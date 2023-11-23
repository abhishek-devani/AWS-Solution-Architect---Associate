# 11 - Classic Solutions Architecture Discussions

## Section Introduction

- Let's understand how all the technologies we've seen work together
- We'll see the progression of a Solution's architect mindset through many **sample case studies:**
    - WhatIsTheTime.Com
    - MyClothes.Com
    - MyWordPress.Com
    - Instantiating applications quickly
    - Beanstalk

---
## Stateless Web App - WhatIsTheTime.Com

- WhatIsTheTime.Com allows people to know what time it is.
- We don't need a database
- We want to start small and accept downtime
- We want to fully scale vertically and horizontally, no downtime

---
## Stateless Web App - What time is it?

### Scaling Vertically

- We have **one user** and one t2.micro **public EC2 instance.**
- When user send a request to ec2 than it will get current time.
- We setup a elastic Ip and attached it to ec2 so user can access application every time with same IP.
- User suggested his friends to use this app and his friends started using it.
- We realized that our application is getting more and more traffic and certainly t2.micro isn't enough.
- As a solution architect we say maybe we should use M5.large instead of t2.micro
- We stopped instance then we changed the instance type and started the instance again.
<!-- - We have **experienced downtime while scaling vertically.** -->
- **`Limit:` It worked but it isn't good because We have experienced downtime while scaling vertically.**

### Scaling Horizontally

- As a solution architect we started thinking on Scaling Horizontal.
- And for that we started adding more EC2 instances, they're all M5 large.
- And they all have elastic IP attached to it.
- So now on top of having 3 EC2 instance, we have 3 elastic IPS.
- And so our users, they need to be aware of the exact values of three elastic IP to talk to our instances.
- **`Limit:` It worked but user have to remember more IPs and we have to manage more infrastructure, and it's pretty tricky**

### Use Route 53

- Let's remove elastic IPs from all the instance because There's only 5 elastic IPs per region per accounts by default so it's not a lot.
- Instead of this we'll setup route 53 and set up website URL as api.whatisthetime.com.
- We've decided its going to be an `A record` with a TTl of a 1 hour.
- An `A record` means that DNS will going to give me a list of IP.
- So now users will query route 53 and they get IP addresses of EC2 Instances.
- **`Limit:` When we do remove an instance user will get the same response for an one hour because of TTL.**

### Use Load Balancer

- We have setup **private ec2 instance now** and in the **same AZ.**
- We'll use **ELB + Health Checks** and link to our instances.
- ELB will be public while EC2 instances is private so they restrict traffic between using security group.
- We will then connect route 53 to out ELB using alias record because IP of ELB has its IP changing all the time.
- This alias record will point from Route 53 --ELB.
- **`Limit:` Adding and removing instances manually is pretty hard to do.**

### Use Auto Scaling Group

- We will create ASG and this time our EC2 will be managed by ASG.
- And so this allows our ASG to basically scale based on the demand, scale in and scale out.
- Now we have a `stable application` with `no downtime,` `auto-scaling` and `load balanced`
- It seems like a really `stabled architecture`.
- **`Limit:` What if AZ goes down due to some natural calamities? Our app will be entire down**

### Implement Multi AZ Application

- We will implement multi Az to be highly available.
- We are gonna have to ELB, and on top of health checks, it's also going to be multi-AZ, and it's going to be launched on AZ 1 to 3.
- Our ASG as well is going to span across multiple AZ.
- So now if AZ-1 goes down, we'll still have AZ-2 and AZ-3 to serve our traffic to users.
- We have effectively made our app **multi AZ, and highly available and resilient to failure**.

### Minimum 2 AZ ==> Let's Reserve Capacity

- We have 2 AZ and we know at least one instance we'll be running in each AZ, so why don't we reserve capacity?
- Why don't we start basically the diminishing the cost of our application? because we know that for sure two instances must be running at all time during the year. 
- And so by reserving instance maybe for the capacity of our auto-scaling group, then we're going to save a lot of cost in the future.
- Whereas the new instances that get launched, maybe they're gonna be temporary, so on demand is fine.

> **`Note:` As a solution architect you have to understand what are the requirements, and what should we architect in return to these requirements**

---
## Stateful Web App - MyClothes.com

- It allows people to buy cloths online.
- There's shopping cart.
- Our website is having hundreds of users at the same time.
- We need to scale, maintain horizontal scalability and keep our web application as stateless as possible.
- Users should not lose their shopping cart.
- Users should have their details (address, etc) in a database.

### Basic Architecture

- We have user + Route 53 + Multi AZ ELB + ASG with 3 AZ.
- We users create shopping cart with 1st instance.
- **`Limit:` If next request goes to 2nd instance then user won't be able to view shopping cart, so shopping cart is lost for user.**

### Introduce Stickiness (Session Affinity)

- It's an ELB feature, so we enable ELB stickiness.
- So every request of particular user to the same instance because of stickiness.
- **`Limit: `What is an EC2 instance gets terminated for some reason, then we still lose our shopping cart**

### Introduce User Cookies

- So instead of having the EC2 instances store the content of the shopping cart, let's say that the user is the one storing the shopping cart content.
- So every time it connects to the load balancer, it basically is going to say, by the way in my shopping cart, I have all these things. 
- And that's done through web cookies.
- We **achieved statelessness** because now each EC2 instance doesn't need to know what happened before. The user will tell us what happened before.
- **`Limit 1:` HTTP request are heavier because we are sending cart content in web cookies we're sending more and more data every time we add something into our cart**
- **`Limit 2:` There is some level of security risk because the cookies, they can be altered by attackers maybe**
- **`Limit 3:` Cookies must be validated**
- **`Limit 4:` Cookies must be less than 4kb**

### Introduce Server Session

- Instead of sending whole shopping cart in we cookies, we're just going to send **Session ID**.
- In the background, we're gonna have maybe an **ElastiCache Cluster** to store/retrieve session data.
- When user tell EC2 that I'm going to add this content to cart then ec2 instance will add that cart content into the ElastiCache.
- And ID to retrieve this content is going to be Session ID.
- All this is a millisecond performance, So all these things happen really quickly.
- Alternatively we can use **DynamoDB** to store session data.
- It's secure now because no one can change what's in ElastiCache Cluster.

### Storing User Data in Database

- Now, we are gonna store user data (address) in a database.
- So again, we're gonna talk to ourEC2 instance, and this time it's going to talk to an RDS instance.
- RDS is going to be great because it's for long term storage. so we can store and retrieve data such as address, name, etc directly by taking to RDS.
- So our website is doing amazing. 

### Scaling Reads

- Now we have more and more users and we realize that one of the most thing they do is they navigate the website.
- They do reads, they get product information etc. 
- We can use **RDS Master**, which takes the writes but we can also have **RDS Read Replicas** with some replication happening.
- We can have up to 15 read replicas.

### Scaling Reads (Alternative) - Lazy Loading

- Our user talks to an EC2 instance. It looks in the ElastiCache for information. If it doesn't have then it's going to read from RDS and put in back into ElastiCache. SO just the information is cached.
- This pattern allows us to do less traffic on RDS, basically **decrease the CPU usage** and **improve performance** at the same time.
- **`Limit:` We need to do cache maintenance now and it's a bit more difficult**.

### Multi AZ - Survive Disasters

- Make ELB Multi AZ.
- Make ASG Multi AZ.
- Make RDS Multi AZ.
- Make ElastiCache Multi AZ.

### Security Groups

- Maybe We'll **open HTTP/HTTPS traffic from anywhere** on the **ALB (Application Load Balancer) side**.
- For an **EC2 instance side**, we just want to **restrict traffic coming from the load balancer**. 
- For an **ElastiCache**, we just want to **restrict traffic coming from the EC2 security group**.
- For an **RDS**, we just want to **restrict traffic coming from the EC2 security group**.

> **`Note:` This is a more complicated application. There's three tier such as Client Tier, Web Tier and the database tier**

---
## Stateful Web App - MyWordPress.com

- We are trying to create a fully scalable WordPress website.
- We want that website to access and correctly display picture uploads.
- Our user data, and the blog content should be stored in a MySQL database.

## Scaling with Aurora: Multi AZ and Read Replicas

- Consider previous architecture.
- We will replace RDS layer with Aurora MySQL and I can have Multi AZ, Read Replicas.
- We can setup even Global Databases using Aurora if we wanted to.
- Aurora scales better and it is easier to upwrite.

## Storing Images with EBS

- Let's go back to simple architecture where we have `1 EC2` and it has `1 EBS volume` attached to it. So it's in 1 AZ.
- We are connected to Load Balancer.
- So our user wants to send image to load balancer and that image makes it all the way through to EBS. So now image is stored in EBS.
- **`Limit:` EBS only works well for 1 instance but when we start scaling then is's started to become problematic**.

## Storing Images with EFS

- We use the same architecture but instead of EBS we will use EFS.
- EFS creates an ENI's (Elastic Network Interface) and it created these ENI's into each AZ.
- And these ENI's can be used for all our EC2 instances to access our EFS drive. 
- Storage is shared between all the instances.

---
## Instantiating  Applications Quickly

### Problems

- When launching a full stack application (EC2, EBS, RDS), it can take time to:
    - Install Application
    - Insert initial (or recovery) data
    - Configure everything
    - Launch an application

> **`Note:` We can take advantage of the cloud to speed that up**

### Solutions

#### EC2 Instances:

- **Use a Golden AMI:**
    - Install your applications, OS dependencies etc., beforehand and launch your EC2 instance from the `Golden AMI`.

- **Bootstrap using User Data:**
    - For dynamic configuration, use User Data scripts.

- **Hybrid:**
    - Mix Golden AMI and User Data (Elastic Beanstalk)

#### RDS Databases:

- **Restore from Snapshot**
    - The database will have schemas and data ready!

#### EBS Volumes:

- **Restore from Snapshot**
    - The disk will already be formatted and have data!

