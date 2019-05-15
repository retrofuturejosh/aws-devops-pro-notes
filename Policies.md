# Policies and Standards Automation

## Table of Contents
- [Service Catalog](#Service-Catalog)
- [Trusted Advisor](#Trusted-Advisor)
- [Systems Manager](#Systems-Manager)
- [Organizations](#Organizations)
- [Secrets Manager](#Secrets-Manager)
- [Macie](#Macie)
- [Certificate Manager](#Certificate-Manager)

# Service Catalog
Allows IT administrators to create, manage, and distribute catalogs of approved products to end users, who can then access the products they need in a personalized portal
- Has an API that provides programmatic contol over all end-user actions as an alternative to AWS Console
- Allows you to create custom interfaces and apps
- Allows administrators to create and distribute application stacks called **products**
- Products can be grouped into folders called **portfolios**
- Users can then launch and manage products without requiring access to AWS services/console
- Users can only see products they are supposed to see

# Trusted Advisor
Service that provides real time guidance to ensure resources are provisioned and managed correctly, following AWS best practices

Categories:
- Cost optimization
- Performance
  - e.g. under-utilizing resources
- Security
- Fault tolerance
- Service Limits

# Systems Manager
- Collecting software inventory
- Applying OS patches
- Creating system images
- Configuring operation systems
- Manage hybrid cloud systems from a single interface (AWS, on-prem)
- Reducing costs

Features
- Run command
  - remote management of servers
- State manager
  - manage all your servers' configuration
  - e.g. firewall settings
  - e.g. anti-virus definitions
- Inventory
  - info on installed applications
- Maintentance Window
- Patch Manager
  - e.g. select and deploy software across all EC2 instances
- Automation
  - automate repetitive IT tasks
- Parameter Store


# Organizations
Policy-based management for multiple AWS accounts.

Accounts can be split several ways within business: Environment (dev, qa, prod), Projects, or Business Units (Sales, support, dev, etc)

AWS Organizations allows you to:
- Programmatically create new accounts
- Create and maintain a group of accounts
- Set policies on those groups
- Set single payer, cost tracking

Overrides IAM policies with an organization policy


# Secrets Manager
Service to help protect secrets needed to access applications, service, and IT resources

- Encrypts secrets at rest using your own encrpytion keys stored in KMS
- Secrets can be database credentials, passwords, 3rd part API keys, any text, etc.
- Store and control access to them with Secrets Manager Console/CLI/API/SDK
- Hardcoded credentials in code is replaced with API call
- Secrets can be rotated automatically according to your own schedule

# Macie
Security service that uses machine learning to automatically discover, classify, and protect sensitive data in AWS

Managed service:
- Can recognize any Personally Identifiable Information (PII)
- Provides dashboard showing how data is accessed/moved
- Monitors data access for anomalies
- Generates detailed alerts when it detects risk of unauthorized access or accidental data leaks
- Currently only protects S3, but more is planned

# Certificate Manager
Easily provision, manage, and deploy SSL/TLS certificates

- Centrally manage certificates in AWS
- Audit the use of each certificate in CloudTrail logs
- Private Certificate Authority
- AWS integration
- Import 3rd party certificates from other CAs
