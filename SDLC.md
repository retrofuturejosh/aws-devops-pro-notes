# SDLC
## Table of Contents
- [Deployment Types](#Deployment-Types)
  - [Single Target](#Single-Target)
  - [All-at-once](#All-at-once)
  - [Minimum In-service](#Minimum-In-service)
  - [Rolling Deployment](#Rolling-Deployment)
  - [Blue Green Deployment](#Blue-Green-Deployment)
- [CodeCommit](#CodeCommit)
  - [Creating Web Hooks with CodeCommit](#Creating-Web-Hooks-with-CodeCommit)
- [CodeBuild](#CodeBuild)
  - [How it Works](#How-it-Works)
    - [Steps](#Steps)
  - [Sources](#Sources)
  - [Programming Frameworks](#Programming-Frameworks)
  - [Jenkins Integration](#Jenkins-Integration)
  - [Security](#Security)
  - [Buildspec.yml](#Buildspec.yml)
  - [Build Environment Compute Types](#Build-Environment-Compute-Types)
  - [Docker Images](#Docker-Images-Provided-by-CodeBuild)
- [CodeDeploy](#CodeDeploy)
  - [Supported Platforms/Deployment Types](#Supported-Platforms/Deployment-Types)
  - [CodeDeploy Components](#CodeDeploy-Components-per-Compute-Platform)
  - [App Spec File](#App-Spec-File)
    - [ECS](#AppSpec-Files-on-ECS)
    - [Lambda](#AppSpec-Files-on-Lambda)
    - [EC2/On-Premises](#AppSpec-Files-on-EC2/On-Premises)
[](#)

# Deployment Types:
### Single Target
- New app version is installed on target server
- Outage occurs during installation
- No secondary servers, testing is limited
- Rollback requires removing new version and installing previous version
### All-at-once
- Deployment happens in one step
- Destination is multiple targets
- Requires orchestration tooling
- Shares negatives of Single Target
  - No ability to test
  - Outage
  - Less than ideal rollback
### Minimum In-service
- Happens in multiple stages
- Deployment happens to as many targets as possible while maintaining minimum in-servive targets
- Allows automated testing
  - Deployment targets are assessed and tested prior to continuing
- No downtime
### Rolling Deployment
- Deployment happens in multiple stages, number of targets per stage is user-defined
- Moving parts, orchestration, health checks required
- Overall applicable health is not necessarily maintained
- Can be least effecient deployment type based on time-taken
- Allows automated testing
  - Deployment targets are assessed and tested prior to continuing
- Generally no downtime, assuming number of targets will not impact application
- Can be paused, allowing limited multi-version testing
### Blue Green Deployment
- Requires advanced orchestration tooling
- Costs more: maintain two environments at once
- Deployment is rapid, entire environment is deployed at once
- Cutover and migration is clean and controlled (DNS change)
- Rollback is equally clean (DNS regression)
- Health and performance of entire green environment can be tested prior to cutover
- Using advanced template systems (CloudFormation), entire process can be automated
### Canary Deployment
- Similar to Blue Green, but blue stays incrementally active
- Can be done with Route53 - weighted Round Robin
- Easily allows for a/b testing

# CodeCommit
Version control service to privately store and manage assets (such as documents, source code, and binary files) in the cloud
- Fully Managed –AWS CodeCommit eliminates the need to host, maintain, backup, and scale your own source control servers.
- Secure –AWS CodeCommit automatically encrypts your files in transit and at rest. AWS CodeCommit is integrated with AWS Identity and Access Management (IAM), allowing you to assign user-specific permissions to your repositories.
- Highly Available – AWS CodeCommit is built on highly scalable, redundant, and durable AWS services such as Amazon S3 and Amazon DynamoDB.
- Scalable - AWS CodeCommit allows you store any number of files and there are no repository size limits.
- Faster Development Lifecycle - AWS CodeCommit keeps your repositories close to your build, staging, and production environments in the AWS cloud. This allows you to increase the speed and frequency of your development lifecycle.
### Creating Web Hooks with CodeCommit
In the Amazon Simple Notification Service (SNS) console, you can create a SNS topic with an HTTP endpoint and the desired URL for the webhook. From the AWS CodeCommit console, you can then configure that SNS topic to a repository event using triggers.

# CodeBuild
Fully managed continuous integration service in the cloud. CodeBuild compiles source code, runs tests, and produces packages that are ready to deploy.
- Eliminates the need to provision, manage, and scale your own build servers
- Automatically scales up and down and processes multiple builds concurrently, so builds don’t have to wait in a queue.
- Use prepackaged build environments or custom build environments to use your own build tools
- Pay by the minute
### How it Works
As input, you must provide CodeBuild with a build project. A build project defines how CodeBuild runs a build. It includes information such as where to get the source code, the build environment to use, the build commands to run, and where to store the build output. A build environment represents a combination of operating system, programming language runtime, and tools that CodeBuild uses to run a build.

#### Steps:

1. CodeBuild uses the build project to create the build environment.

2. CodeBuild downloads the source code into the build environment and then uses the build specification (build spec), as defined in the build project or included directly in the source code. A build spec is a collection of build commands and related settings, in YAML format, that CodeBuild uses to run a build.

3. If there is any build output, the build environment uploads its output to an Amazon S3 bucket. The build environment can also perform tasks that you specify in the build spec (for example, sending build notifications to an Amazon SNS topic).

4. While the build is running, the build environment sends information to CodeBuild and Amazon CloudWatch Logs.

5. While the build is running, you can use the CodeBuild console, AWS CLI, or AWS SDKs, to get summarized build information from CodeBuild and detailed build information from Amazon CloudWatch Logs. If you use AWS CodePipeline to run builds, you can get limited build information from CodePipeline.
### Sources
CodeBuild can connect to AWS CodeCommit, S3, GitHub, and GitHub Enterprise and Bitbucket to pull source code
### Programming Frameworks
Java, Ruby, Python, Go, Node.js, Android, .NET Core, PHP, and Docker. Customize an environment by creating a Docker image and uploading it to the Amazon EC2 Container Registry or the Docker Hub registry
### Jenkins Integration
CodeBuild Plugin for Jenkins can be used to integrate CodeBuild into Jenkins jobs
### Security
- Specify a key stored in the AWS Key Management Service (AWS KMS) to encrypt your artifacts
-  Runs  build in fresh environments isolated from other users and discards each build environment upon completion. CodeBuild provides security and separation at the infrastructure and execution levels.
### Buildspec.yml
 A build spec is a collection of build commands and related settings, in YAML format, that CodeBuild uses to run a build. You can include a build spec as part of the source code or you can define a build spec when you create a build project.

 By default, the build spec file must be named buildspec.yml and placed in the root of your source directory. You can override the default build spec file name and location

 The build spec has the following syntax:
```
version: 0.2

run-as: Linux-user-name

env:
  variables:
    key: "value"
    key: "value"
  parameter-store:
    key: "value"
    key: "value"

phases:
  install:
    run-as: Linux-user-name
    commands:
      - command
      - command
    finally:
      - command
      - command
  pre_build:
    run-as: Linux-user-name
    commands:
      - command
      - command
    finally:
      - command
      - command
  build:
    run-as: Linux-user-name
    commands:
      - command
      - command
    finally:
      - command
      - command
  post_build:
    run-as: Linux-user-name
    commands:
      - command
      - command
    finally:
      - command
      - command
artifacts:
  files:
    - location
    - location
  name: artifact-name
  discard-paths: yes
  base-directory: location
  secondary-artifacts:
    artifactIdentifier:
      files:
        - location
        - location
      name: secondary-artifact-name
      discard-paths: yes
      base-directory: location
    artifactIdentifier:
      files:
        - location
        - location
      discard-paths: yes
      base-directory: location
cache:
  paths:
    - path
    - path
```
### Build Environment Compute Types
Compute type | computeType | value	Memory | vCPUs | Disk space | Operating system
| --- | --- | --- | --- | --- | --- |
build.general1.small | BUILD_GENERAL1_SMALL | 3 GB | 2 | 64 GB	| Linux |
build.general1.medium | BUILD_GENERAL1_MEDIUM | 7 GB | 4	|128 GB	|Linux, Windows
build.general1.large	|BUILD_GENERAL1_LARGE	|15 GB	|8	|128 GB	|Linux, Windows
### Docker Images Provided by CodeBuild
|Platform	|Programming language or framework	|Image identifier|	Definition|
---|---|---|---|
Ubuntu 18.04	|(Standard image)|aws/codebuild/standard:1.0	|ubuntu/standard/1.0|
Windows Server Core 2016	|(Base Image)	|aws/codebuild|windows-base:1.0|	N/A|
Standard image of the Ubuntu 18.04 platform contains the following programming languages:
- Ruby	2.x
- Python	3.x
- PHP	7.x
- Node	10.x
- Java	8
- Golang	1.x
- .NET Core	2.x
- Docker	18.x
- Android	28.x

# CodeDeploy
Service that automates code deployments to any instance, including Amazon EC2 instances and instances running on-premises.


### Supported Platforms/Deployment Types:

- EC2/On-Premises: In-Place or Blue/Green Deployments
  - Describes instances of physical servers that can be Amazon EC2 cloud instances, on-premises servers, or both. Applications created using the EC2/On-Premises compute platform can be composed of executable files, configuration files, images, and more.
  - Deployments that use the EC2/On-Premises compute platform manage the way in which traffic is directed to instances by using an in-place or blue/green deployment type.

- AWS Lambda: Canary, Linear, All-At-Once Deployments
  - Applications created using the AWS Lambda compute platform can manage the way in which traffic is directed to the updated Lambda function versions during a deployment by choosing a canary, linear, or all-at-once configuration.

- Amazon ECS: Blue/Green Deployment
  - Used to deploy an Amazon ECS containerized application as a task set.
  - CodeDeploy performs a blue/green deployment by installing an updated version of the containerized application as a new replacement task set. CodeDeploy reroutes production traffic from the original application, or task set, to the replacement task set. The original task set is terminated after a successful deployment.

### CodeDeploy Components per Compute Platform
|CodeDeploy Component	|EC2/On-Premises	|AWS Lambda	|Amazon ECS|
---|---|---|---|
Deployment group	|Deploys a revision to a set of instances.	| Deploys a new version of a serverless Lambda function on a high-availability compute infrastructure.	|Specifies the Amazon ECS service with the containerized application to deploy as a task set, a production and optional test listener used to serve traffic to the deployed application, when to reroute traffic and terminate the deployed application's original task set, and optional trigger, alarm, and rollback settings.|
Deployment|	Deploys a new revision that consists of an application and AppSpec file. The AppSpec specifies how to deploy the application to the instances in a deployment group.	|Shifts production traffic from one version of a Lambda function to a new version of the same function. The AppSpec file specifies which Lambda function version to deploy.	|Deploys an updated version of an Amazon ECS containerized application as a new, replacement task set. CodeDeploy reroutes production traffic from the task set with the original version to the new replacement task set with the updated version. When the deployment completes, the original task set is terminated.|
Deployment configuration	|Settings that determine the deployment speed and the minimum number of instances that must be healthy at any point during a deployment.	|Settings that determine how traffic is shifted to the updated Lambda function versions.	|Traffic is always shifted all at once. Custom deployment configuration settings cannot be specified for an Amazon ECS deployment.|
Revision	|A combination of an AppSpec file and application files, such as executables, configuration files, and so on.	|An AppSpec file that specifies which Lambda function to deploy and Lambda fuctions that can run validation tests during deployment lifecycle event hooks.	| An AppSpec file that specifies:<ul><li>The Amazon ECS task definition for the Amazon ECS service with the containerized application to deploy.</li><li>The container where your updated application is deployed.</li><li>A port for the container where production traffic is rerouted.</li><li>Optional network configuration settings and Lambda fuctions that can run validation tests during deployment lifecycle event hooks.</li></ul>|
Application|	A collection of deployment groups and revisions. An EC2/On-Premises application uses the EC2/On-Premises compute platform.	|A collection of deployment groups and revisions. An application used for an AWS Lambda deployment uses the Amazon ECS compute platform.	|A collection of deployment groups and revisions. An application used for an Amazon ECS deployment uses the Amazon ECS compute platform.

### App Spec File
The application specification file (AppSpec file) is a YAML-formatted or JSON-formatted file used by CodeDeploy to manage a deployment.

Note: the name of the AppSpec file for an EC2/On-Premises deployment must be appspec.yml. The name of the AppSpec file for an Amazon ECS or AWS Lambda deployment must be appspec.yaml.

#### AppSpec Files on ECS
Determine:
- Amazon ECS task definition file. This is specified with its ARN in the TaskDefinition instruction in the AppSpec file.
- The container and port in replacement task set where your Application Load Balancer or Network Load Balancer reroutes traffic during a deployment. This is specified with the LoadBalancerInfo instruction in the AppSpec file.
- Optional information about your Amazon ECS service, such the platform version on which it runs, its subnets, and its security groups.
- Optional Lambda functions to run during hooks that correspond with lifecycle events during an Amazon ECS deployment. For more information, see AppSpec 'hooks' Section for an Amazon ECS Deployment.

Example appspec.yaml (ECS)
```
version: 0.0
Resources:
  - TargetService:
      Type: AWS::ECS::Service
      Properties:
        TaskDefinition: "arn:aws:ecs:us-east-1:111222333444:task-definition/my-task-definition-family-name:1"
        LoadBalancerInfo:
          ContainerName: "SampleApplicationName"
          ContainerPort: 80
# Optional properties
        PlatformVersion: "LATEST"
        NetworkConfiguration:
          AwsvpcConfiguration:
            Subnets: ["subnet-1234abcd","subnet-5678abcd"]
            SecurityGroups: ["sg-12345678"]
            AssignPublicIp: "ENABLED"
Hooks:
  - BeforeInstall: "LambdaFunctionToValidateBeforeInstall"
  - AfterInstall: "LambdaFunctionToValidateAfterTraffic"
  - AfterAllowTestTraffic: "LambdaFunctionToValidateAfterTestTrafficStarts"
  - BeforeAllowTraffic: "LambdaFunctionToValidateBeforeAllowingProductionTraffic"
  - AfterAllowTraffic: "LambdaFunctionToValidateAfterAllowingProductionTraffic"
```

#### AppSpec Files on Lambda
Determine:

- Which Lambda function version to deploy.
- Which Lambda functions to use as validation tests.

An AppSpec file can be YAML-formatted or JSON-formatted. You can also enter the contents of an AppSpec file directly into CodeDeploy console when you create a deployment.

Example appspec.yaml (Lambda)
```
version: 0.0
Resources:
  - myLambdaFunction:
      Type: AWS::Lambda::Function
      Properties:
        Name: "myLambdaFunction"
        Alias: "myLambdaFunctionAlias"
        CurrentVersion: "1"
        TargetVersion: "2"
Hooks:
  - BeforeAllowTraffic: "LambdaFunctionToValidateBeforeTrafficShift"
  - AfterAllowTraffic: "LambdaFunctionToValidateAfterTrafficShift"
```

#### AppSpec Files on EC2/On-Premises
Determine:
- What it should install onto your instances from your application revision in Amazon S3 or GitHub.
- Which lifecycle event hooks to run in response to deployment lifecycle events.

Note: An AppSpec file must be a YAML-formatted file named appspec.yml and it must be placed in the root of the directory structure of an application's source code. Otherwise, deployments fail.

Steps:
- Complete AppSpec file, bundle it, along with the content to deploy, into an archive file (zip, tar, or compressed tar)
- After you have a bundled archive file (known in CodeDeploy as a revision), you upload it to an Amazon S3 bucket or Git repository.
- Use CodeDeploy to deploy the revision.
- The appspec.yml for an EC2/On-Premises compute platform deployment is saved in the root directory of your revision.

Example appspec.yml ( EC2/On-Premises)
```
version: 0.0
os: linux
files:
  - source: Config/config.txt
    destination: /webapps/Config
  - source: source
    destination: /webapps/myApp
hooks:
  BeforeInstall:
    - location: Scripts/UnzipResourceBundle.sh
    - location: Scripts/UnzipDataBundle.sh
  AfterInstall:
    - location: Scripts/RunResourceTests.sh
      timeout: 180
  ApplicationStart:
    - location: Scripts/RunFunctionalTests.sh
      timeout: 3600
  ValidateService:
    - location: Scripts/MonitorService.sh
      timeout: 3600
      runas: codedeployuser
```
