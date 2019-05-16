# High Availability, Fault Tolerance, & Disaster Recovery

## Table of Contents
- [Single Sign-On](#Single-Sign-On)
- [CloudFront](#CloudFront)
- [AutoScaling](#AutoScaling)
  - [AutoScaling lifecycle](#AutoScaling-Lifecycle)
- [Route 53](#Route-53)

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
