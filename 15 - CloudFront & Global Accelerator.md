# 15 - CloudFront & Global Accelerator

## AWS CloudFront

- Content Delivery Network (`CDN`)
- **Improves read performance, content is cached at the edge**
- `DDoS Protection`, Integration with Shield, AWS Web Application Firewall.

---
## CloudFront - Origins

### S3 Bucket

- For distributing files and caching them at the edge
- Enhanced security with CloudFront Origin Access Control (OAC)
- OAC is replacing Origin Access Identity (OAI)
- CloudFront can be used as an ingress (to upload files to S3)

### Custom Origin (HTTP)

- Application Load Balancer
- EC2 instance
- S3 website (must first enable the bucket as a static S3 website)
- Any HTTP backend you want

---
## CloudFront vs S3 Cross Region Replication

|               CloudFront                  |               S3 Cross Region Replication                     |
|-------------------------------------------|---------------------------------------------------------------|
| Global Ege Network                        | Must be setup for each region you want replication to happen  |
| Files are cached for a TTL (maybe a day)  | Files are updated in real time                                |
| Great for static content                  | Great for dynamic content                                     |

---
## CloudFront - Geo Restrictions

- You can restrict who can access your distribution
    - Allowlist: Allow your users to access your content only if they're in one of the countries on a list of approved countries.
    - Blocklist: Prevent your users from accessing your content if they're in one of the countries on a list of banned countries.

- The “country” is determined using a 3rd party Geo-IP database
- `Use case:` Copyright Laws to control access to content

---
## CloudFront - Pricing

- CloudFront Edge locations are all around the world 
- The cost of data out per edge location varies

### CloudFront - Price Classes

- You can reduce the number of edge locations for cost reduction
- Three price classes:
    1. **Price Class All:** all regions – best performance 
    2. **Price Class 200:** most regions, but excludes the most expensive regions
    3. **Price Class 100:** only the least expensive regions

---
## CloudFront – Cache Invalidations

• In case you update the back-end origin, CloudFront doesn’t know about it and will only get the refreshed content after the TTL has expired.
• However, you can force an entire or partial cache refresh (thus bypassing the TTL) by performing a CloudFront Invalidation.
• You can invalidate all files (*) or a special path (/images/*).

---
## Unicast IP vs Anycast IP

- `Unicast IP:` one server holds one IP address
- `Anycast IP:` all servers hold the same IP address and the client is routed to the nearest one.

---
## AWS Global Accelerator

- Leverage the AWS internal network to route to your application.
- 2 Anycast IP are created for your application.
- The Anycast IP send traffic directly to Edge Locations.
- The Edge locations send the traffic to your application.
- Works with **Elastic IP, EC2 instances, ALB, NLB, public or private.**

### Consistent Performance

- Intelligent routing to lowest latency and fast regional failover
- No issue with client cache (because the IP doesn’t change)
- Internal AWS network

### Health Checks

- Performs a health check of your applications
- Helps make your application global (failover less than 1 minute for unhealthy)
- Great for disaster recovery

### Security

- only 2 external IP need to be whitelisted.
- DDoS protection thanks to AWS Shield.

