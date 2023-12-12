# 16 - AWS Storage Extras

## AWS Snow Family

- Highly-secure, portable devices to collect and process data at the edge, and migrate data into and out of AWS.
- offline devices to perform data migrations.
- If it takes more than a week to transfer over the network, use Snowball devices!
- `Data Migration:` Snowcone, Snowball Edge, Snowmobile
- `Data Computing:` Snowcone, Snowball Edge

---
## Snowcone & Snowcone SSD

- Small, portable computing, anywhere, rugged & secure, withstands harsh environments.
- Can be sent back to AWS offline, or connect it to internet and use AWS DataSync to send data.
- `Snowcone:` 8 TB of HDD Storage
- `Snowcone SSD:` 14 TB of SSD Storage

---
## Snowball Edge

- Alternative to moving data over the network.
- Provide block storage and Amazon S3 compatible object storage.


### Snowball Edge Storage Optimized

- 80 TB of HDD capacity

### Snowball Edge Compute Optimized

- 42 TB of HDD or 28TB NVMe capacity

### Use cases

- Large data cloud migrations
- DC Decommission
- Disaster recovery

---
## AWS Snowmobile

- Transfer exabytes of data (1 EB = 1,000 PB = 1,000,000 TBs).
- Each Snowmobile has 100 PB of capacity (use multiple in parallel)
- Better than Snowball if you transfer more than 10 PB

---
## Snow Family

--------------------------------------------------------------------------------------------------------
|                  | Snowcone & Snowcone SSD | Snowball Edge Storage Optimized |       Snowmobile      |
|                  | ----------------------- | ------------------------------- |       ----------      |
| Storage Capacity |       8 TB HDD          |       80 TB usable              |         < 100 PB      |
|                  |       14 TB SSD         |                                 |                       |
|                  | ----------------------- | ------------------------------- |   ------------------  |
| Migration Size   |       Up to 24 TB,      |      Up to petabytes, offline   |     Up to exabytes,   |
|                  |   online and offline    |                                 |         offline       |
|                  | ----------------------- | ------------------------------- |   ------------------  |
| DataSync agent   |   Pre Installed         |                                 |                       |
--------------------------------------------------------------------------------------------------------

---
## What is Edge Computing?

- Process data while it’s being created on an edge location
    - A truck on the road, a ship on the sea, a mining station underground.
- These locations may have
    - Limited / no internet access
    - Limited / no easy access to computing power
- We setup a `Snowball Edge / Snowcone` device to do edge computing.

### Use cases of Edge Computing:

- Preprocess data
- Machine learning at the edge
- Transcoding media streams

---
## AWS OpsHub

- AWS OpsHub is a software that you need to install on your computer to manage your `Snow Family Device`.
- It's GUI.
- Unlocking and configuring single or clustered devices
- Transferring files
- Launching and managing instances running on Snow Family Devices
- Monitor device metrics (storage capacity, active instances on your device)
- Launch compatible AWS services on your devices (ex: Amazon EC2 instances, AWS DataSync, Network File System (NFS))

---
## Snowball into Glacier

- **Snowball cannot import to Glacier directly.**
- You must use Amazon S3 first, in combination with an S3 lifecycle policy.

---
## Amazon FSx – Overview

- **Launch 3rd party high-performance file systems on AWS.**
- Fully managed service

### Amazon FSx for Windows (File Server)

- Supports SMB protocol & Windows NTFS.
- Microsoft Active Directory integration, ACLs, user quotas.
- **Can be mounted on Linux EC2 instances.**
- Supports Microsoft's Distributed File System (DFS) Namespaces.
- **Scale up to 10s of GB/s, millions of IOPS, 100s PB of data**
- Can be configured to be Multi-AZ (high availability)
- ata is backed-up daily to S3

### Amazon FSx for Lustre

- Lustre is a type of parallel distributed file system, for large-scale computing
- The name Lustre is derived from “Linux” and “cluster
- **Machine Learning, High Performance Computing (HPC)**
- Video Processing, Financial Modeling, Electronic Design Automation
- Scales up to 100s GB/s, millions of IOPS, sub-ms latencies
- Seamless integration with S3
    - Can “read S3” as a file system (through FSx)
    - Can write the output of the computations back to S3 (through FSx)

#### FSx Lustre - File System Deployment Options

#### Scratch File System
- Temporary storage
- Data is not replicated (doesn’t persist if file server fails)
- High burst (6x faster, 200MBps per TiB)
- Usage: short-term processing, optimize costs

#### Persistent File System
- Long-term storage
- Data is replicated within same AZ 
- Replace failed files within minutes
- Usage: long-term processing, sensitive data

### Amazon FSx for NetApp ONTAP

- **File System compatible with NFS, SMB, iSCSI protocol**
- Move workloads running on ONTAP or NAS to AWS
- Works with:
    - Linux
    - Windows
    - MacOS 
    - VMware Cloud on AWS
    - Amazon Workspaces & AppStream 2.0
    - Amazon EC2, ECS and EKS
- Storage shrinks or grows automatically
- Snapshots, replication, low-cost, compression and data de-duplication
- Point-in-time instantaneous cloning (helpful for testing new workloads)

### Amazon FSx for OpenZFS

- File System compatible with NFS (v3, v4, v4.1, v4.2)
- Move workloads running on ZFS to AWS
- Works with:
    - Linux
    - Windows
    - MacOS 
    - VMware Cloud on AWS
    - Amazon Workspaces & AppStream 2.0
    - Amazon EC2, ECS and EKS
- Up to 1,000,000 IOPS with < 0.5ms latency
- Snapshots, low-cost, compression
- Point-in-time instantaneous cloning (helpful for testing new workloads)

---
## AWS Storage Cloud Native Options

### Block

- Amazon EBS
- EC2 Instance Store

### File

- Amazon EFS
- Amazon FSx

### Object

- Amazon S3
- Amazon Glacier

---
## AWS Storage Gateway

- Bridge between on-premises data and cloud data 
- Use cases:
    - disaster recovery
    - backup & restore
    - tiered storage
    - on-premises cache & low-latency files access
- Types of Storage Gateway:
    - S3 File Gateway 
    - FSx File Gateway
    - Volume Gateway
    - Tape Gateway

### Amazon S3 File Gateway

- Configured S3 buckets are accessible using the NFS and SMB protocol
- Most recently used data is cached in the file gateway
- Supports S3 Standard, S3 Standard IA, S3 One Zone A, S3 Intelligent Tiering
- Transition to S3 Glacier using a Lifecycle Policy
- Bucket access using IAM roles for each File Gateway
- SMB Protocol has integration with Active Directory (AD) for user authentication

### Amazon FSx File Gateway

- Native access to Amazon FSx for Windows File Server
- Local cache for frequently accessed data
- Windows native compatibility (SMB, NTFS, Active Directory...) 
- Useful for group file shares and home directories

### Volume Gateway

- Block storage using iSCSI protocol backed by S3
- Backed by EBS snapshots which can help restore on-premises volumes!
- `Cached volumes:` low latency access to most recent data
- `Stored volumes:` entire dataset is on premise, scheduled backups to S3

### Tape Gateway

- Some companies have backup processes using physical tapes (!)
- With Tape Gateway, companies use the same processes but, in the cloud
- Virtual Tape Library (VTL) backed by Amazon S3 and Glacier
- Back up data using existing tape-based processes (and iSCSI interface)
- Works with leading backup software vendors

### Storage Gateway – Hardware appliance

- It's physical device you need to attach it to your computer
- Works with File Gateway, Volume Gateway, Tape Gateway
- Has the required CPU, memory, network, SSD cache resources
- Helpful for daily NFS backups in small data centers

---
## AWS Transfer Family

- A fully-managed service for file transfers into and out of Amazon S3 or Amazon EFS using the FTP protocol
- Supports protocol:
    - FTP (File Transfer Protocol)
    - FTPS (File Transfer Protocol over SSL)
    - SFTP (Secure File Transfer Protocol)
- Integrate with existing authentication systems
- Usages
    - Sharing Files
    - Public Datasets
    - CRM
    - ERP

---
## AWS DataSync

- Move large amount of data to and from
    - On-premises / other cloud to AWS (NFS, SMB, HDFS, S3 API…) – needs agent
    - AWS to AWS (different storage services) – no agent needed
- Can synchronize to:
    - Amazon S3 (any storage classes – including Glacier)
    - Amazon EFS
    - Amazon FSx (Windows, Lustre, NetApp, OpenZFS...)
- Replication tasks can be scheduled hourly, daily, weekly
- File permissions and metadata are preserved (NFS POSIX, SMB…)
- One agent task can use 10 Gbps, can setup a bandwidth limit

---
## Storage Comparison
-------------------------------------------------------------------------------------------------------------------|
| **S3:**                               | Object Storage                                                           |
| **S3 Glacier:**                       | Object Archival                                                          |
| **EBS volumes:**                      | Network storage for one EC2 instance at a time                           |
| **Instance Storage:**                 | Physical storage for your EC2 instance (high IOPS)                       |
| **EFS:**                              | Network File System for Linux instances, POSIX filesystem                |
| **FSx for Windows:**                  | Network File System for Windows servers                                  |
| **FSx for Lustre:**                   | High Performance Computing Linux file system                             |
| **FSx for NetApp ONTAP:**             | High OS Compatibility                                                    |
| **FSx for OpenZFS:**                  | Managed ZFS file system                                                  |
| **Storage Gateway:**                  | S3 & FSx File Gateway, Volume Gateway (cache & stored), Tape Gateway     |
| **Transfer Family:**                  | FTP, FTPS, SFTP interface on top of Amazon S3 or Amazon EFS              |
| **DataSync:**                         | Schedule data sync from on-premises to AWS, or AWS to AWS                |
| **Snowcone / Snowball / Snowmobile:** | to move large amount of data to the cloud, physically                    |
| **Database:**                         | for specific workloads, usually with indexing and querying               |
--------------------------------------------------------------------------------------------------------------------
