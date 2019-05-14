1. Know Cloudwatch like the back of your hand - logs, events, metrics, triggers. what services integrate with it.

2. CodePipeline, CodeBuild, CodeDeploy - know the lifecycle well. Testing, rollbacks. Would at least test out building a pipeline to deploy a lambda function, also an AMI. Just so you understand the process.

3. Different deployment types across all services - CodePipeline, EC2, EB, CFN, Lambda, API Gateway. Know how to do a canary, blue-green, and rolling in each.

4. Was a few questions about DynamoDB - one in particular where I had to design a sort/primary key and a LSI/GSI. Was only one question but still..

5. AutoScaling/Launch Configs/ELBs. Know them like the back of your hand. Lifecycle hooks will come up for sure.

6. Amazon Macie, Quick Sight, Personal Health Dashboard came up a few times as well. Just be aware of they do.

7. RDS - DR scenarios, CFN scenarios, cross-region read replicas (which engines support it).

8. Elastic Beanstalk - understand ebextensions as well as the deployment types.

9. Understand container deployment lifecycle - ECS, ECR, and maybe EB.

10. Route 53 comes up a lot. Know how it plays into different scenarios.. DR, B/G deployment, Canary, etc.


ALB's vs ELB's

Git branching

CloudFormation change sets

Amazon EC2 dedicated hosts

AWS Server Migration Service

VPC flow logs

Quicksight

Triggering lambda to make route53 changes

IPV6 (listed as an assumed knowledge topic)

Classic loadbalancer

Tags and cost reports

Matthew's tips from https://acloud.guru/forums/aws-certified-devops-engineer-professional-2019/discussion/-LaO1SD8JROLZSxJ36U8/newvsolddevopspro_exam

CloudFormation StackSets

Beanstalk deployment strategies

Cloudwatch events from things like CodeDeploy/CodePipeline and automation based on that

Some things about working with multiple accounts (literally DR accounts of the type where the original account won't be accessible anymore and how to prepare for that)

Hybrid environment CodeDeploy (how to enable that and how it shows up)

Various multi-region/DR setup questions

S3 permissions

Lambda@Edge
