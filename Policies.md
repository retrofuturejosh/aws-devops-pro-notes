# Policies and Standards Automation

## Table of Contents
- [Service Catalog](#Service-Catalog)
- [Trusted Advisor](#Trusted-Advisor)
- [Systems Manager](#Systems-Manager)

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
