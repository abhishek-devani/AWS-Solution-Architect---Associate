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
**Limit: It worked but it isn't good because We have experienced downtime while scaling vertically.**

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

### Minimum 2 AZ ==Let's Reserve Capacity

- We have 2 AZ and we know at least one instance we'll be running in each AZ, so why don't we reserve capacity?
- Why don't we start basically the diminishing the cost of our application? because we know that for sure two instances must be running at all time during the year. 
- And so by reserving instance maybe for the capacity of our auto-scaling group, then we're going to save a lot of cost in the future.
- Whereas the new instances that get launched, maybe they're gonna be temporary, so on demand is fine.

> **`Note:` As a solution architect you have to understand what are the requirements, and what should we architect in return to these requirements**