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

```markdown

|               CloudFront                  |               S3 Cross Region Replication                     |
|-------------------------------------------|---------------------------------------------------------------|
| Global Ege Network                        | Must be setup for each region you want replication to happen  |
| Files are cached for a TTL (maybe a day)  | Files are updated in real time                                |
| Great for static content                  | Great for dynamic content                                     |

```