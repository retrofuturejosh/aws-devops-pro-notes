# High Availability, Fault Tolerance, & Disaster Recovery

## Table of Contents
- [Single Sign-On](#Single-Sign-On)
- [CloudFront](#CloudFront)
- [AutoScaling](#AutoScaling)
  - [AutoScaling lifecycle](#AutoScaling-Lifecycle)
- [Route 53](#Route-53)
- [RDS](#RDS)
- [Aurora](#Aurora)
- [DynamoDB](#DynamoDB)

# Single Sign-On
Cloud service that makes it easy to centrally manage single sign on access to multiple AWS accounts and business applications

Features
- Integrates with AWS Organizations
- SSO access to cloud applications
- Create/Manage users and groups
- Use existing corporate indentities (Active Directory)

Relevance
- High availability
- Fault tolerant

# CloudFront
A fast content delivery network (CDN) that securely delivers data, video, applications, and APIs to customers globally

## Steps
1. Specify origin servers, like an Amazon S3 bucket or your own HTTP server

2. An origin server stores the original, definitive version of your objects, either in an Amazon S3 bucket or an HTTP server, such as a web server

3. Adobe Media Server RTMP protocol is always an Amazon S3 bucket (streaming video)

4. Upload files to your origin servers: web pages, images, and media files, or anything that can be served over HTTP or a supported version of Adobe RTMP (Adobe Flash Media Server)

5. With S3, you can make the objects in your bucket publicly readable or keep objects private

6. Create a CloudFront distribution, which tells CloudFront which origin servers to get your files from when users request the files through your web site or application. Specify details such as whether you want CloudFront to log all requests and whether you want the distribution to be enabled as soon as it's created.

7. CloudFront assigns a domain name to your new distribution that you can see in the CloudFront console or via API.

8. CloudFront sends your distribution's configuration (but not your content) to all of its edge locations

# AutoScaling
Scale EC2 instance capacity automatically according to defined conditions
- High availability
- Better fault tolerance
- Cost savings

### AutoScaling Lifecycle
- Starts when AutoScaling group launches an instance
- Ends when you terminate an instance
- Ends when AutoScaling group takes the instance out of service and terminates it

Overview
- Starts to scale out
- **Pending state**
    - Hook: EC2_INSTANCE_LAUNCHING
      - Pending:Wait
      - Pending:Proceed
- In **Service State**
- If instance fails or taken out of service, moves to **Terminating State**
    - Hook: EC2_INSTANCE_TERMINATING
      - Terminating:Wait
      - Terminating:Proceed
- Optional: **Standby state**
    - Managed by autoscaling group, but don't receive traffic
    - EnteringStandby
    - Standby
- When **Standby state** is put back into service, it enters **Pending State**
- Optional: Detach Instance
    - Detaching state
    - Detached state
    - Can be stand-alone EC2 or re-attached to another autoscaling group

How Lifecycles Work
1. Autoscaling responds to scale out event by launching an instance
2. Autoscaling puts the instance into **Pending:Wait** state
3. Autoscaling sends a message to notification target defined for the hook, along with information and token
4. Waits until you tell it or timeout ends
5. You can perform custom action, install software, etc.
6. By default, instance will wait 1 hour and change state to **Pending:Proceed**, then enter **InService** state

Notes on Lifecycle Hooks
- You can change heartbeat timeout or define it when you create the hook in the CLI with `heartbeat-timeout` parameter
- You can call the `complete-lifecycle-action` to tell autoscaling group to proceed
- You can call the `record-lifecycle-action-heartbeat` command to add more time to timeout
- 48 hours is the maximum amount you can keep server in wait state

Cooldowns
- Cooldowns ensure that autoscaling group does not launch or terminate more instances than needed
- Cooldowns start when an instance enter InService state, so if instance is left in Pending:Wait, autoscaling will wait before adding any other servers

Abandon or Continue
- At conclusion of lifecycle hook, an instance can result in two states: `abandon` or `continue`
- Abandon will cause autoscaling group to terminate instance and, if necessary, launch a new one
- Continue will put instance into service

Spot Instances
- can use lifecycle hooks with spot instances
- does NOT stop an instance from terminating due to change in Spot Price
- When spot instance terminates, you must complete lifecycle action

# Route 53
Highly available and scalable cloud Domain Name System (DNS) service

Features
- Highly Available
- Interface directly with EC2 and S3
- Fault Tolerant
  - multiple routing types (e.g. latency based routing, weighted round robin)

### Routing Policies
- Failover routing policy
  - for active-passive failure
- Geolocation routing policy
  - route based on location of users
- Geoproximity routing policy
  - route traffic based on location of resources and users
- Latency routing policy
  - route traffic to region with best latency
- Multivalue answer routing policy
  - route randomly to up to 8 destinations
- Weighted routing policy
  - route to different resources using a percentage split
  - good for **A/B Testing**


#RDS
Amazon Relational Database Service - create, run, scale relational DBs in the cloud

Benefits
- Fast
- Cost effecient
- Resizable
- Secure
- Highly Available
- Minimal administration

Supported DB engines
- MySQL
- MariaDB
- Microsoft SQL Server
- PostgreSQL
- Oracle
- Amazon Aurora

Managemed Administration
    - AWS
        - Provision infrastructure
        - install DB software
        - automatic backups
        - automatic patching
        - synchronous data replication
        - automatic failover
    - You
        - Settings
        - Schema
        - Performance tuning

Scaling
    - Vertical:
        - Change instance type
        - discounts for reserved instances
        - storage can be scaled live except MSSQL
    - Horizontal
        - Read replicas
        - Read-only copies synched with master db
        - can be in different regions
        - can be promoted for disaster recovery
        - can create a replica of a replica

# Aurora
MySql and Postgres compatible DB built for the cloud

Benefits
- fast and reliable
- simple
- cost effective
- 5x throughput of mysql on same hardware
- compatibly with mysql 5.7
- fault tolerant and self healing
- disk failures repaired in background
- detects crashes and restarts
- no crash recovery or cache rebuilding required
- automatic failover to one of up to 15 read replicas
- storage autoscaling

Backups
- Automatic, continuous, or incremental
- point-in-time restore within second
- up to 35 day retention period
- no impact on db performance

Snapshots
- user-initiated snapshots stored in s3
- kept until explicitly deleted
- incremental

Failure and Fault Tolerance
- maintains 6 copies of data across 3 AZs
- point in time/snapshot restore
- data divided into 10 gb segments across many disks
- transparently handles loss
- can lose 2 copies without affecting ability to write
- can lose 3 copies without affecting ability to read
- self-healing

Replicas
- Amazon Aurora replicas
    - share underlying volume with primary instance
    - updates made by primary are visible to all replicas
    - up to 15
    - low performance impact on memory
    - replica can be failover target
- MySQL read replicas
    - Primary instance data is replayed on replica as transactions
    - up to 5
    - high performance impact on primary
    - replica can be target, but may have minutes of data loss

Security
- Must be in VPC
- SSL secures data in transit
- can encrypt data with KMS
- encrypted storage at storage/backup/snapshots/replicas
- NOTE: you can't encrypt an unencrypted database

# DynamoDB
Fully managed NoSQL database that supports key-value and document data structures

Details
- no servers
- no storage limitations
- fully resilient, highly available
- performance scales in a linear way
- fully intregrated with IAM

Concepts
- Collection of tables, highest level of structure
- specify performance requirements on table
- WCUs - write capacity units - number of 1 kb writes per second
- RCUs - read capacity units - number of 4 kb reads per second

DynamoDB Tables
- contain items
- items contain attributes (including partition key, which must be unique)
- attributes can also include sort key

Attribute Types
- string
- number
- binary
- boolean
- null
- Document (lists, maps)
- Set (array)

Streams
- Optional feature capturing data modification events
- Data events appear in the stream in near real time and in order
- Each event is represented by a stream record when
    - new item is added
    - item is update
    - item is deleted
- Can trigger lambda when a certain event appears in the stream
