# Configuration Management and Infrastructure as Code
## Table of Contents
- [Cloudformation](#Cloudformation)
  - [Key Terms](#Key-Terms)
  - [Anatomy of a Template](#Anatomy-of-a-Template)
  - [Intrinsic Functions](#Intrinsic-Functions)
    - [Fn::Base64](#FnBase64)
    - [Fn::Cidr](#FnCidr)
    - [Fn::FindInMap](#FnFindInMap)
    - [Fn::GetAtt](#FnGetAtt)
    - [Fn::GetAZs](#FnGetAZs)
    - [Fn::ImportValue](#FnImportValue)
    - [Fn::Join](#FnJoin)
    - [Fn::Select](#FnSelect)
    - [Fn::Split](#FnSplit)
    - [Fn::Sub](#FnSub)
    - [Fn::Transform](#FnTransform)
    - [Ref](#Ref)
    - [Condition Functions](#Condition-Functions)
      - [Condition Intrinsic Functions](#Condition-Intrinsic-Functions)
        - [Fn::And](#FnAnd)
        - [Fn::Equals](#FnEquals)
        - [Fn::If](#FnIf)
        - [Fn::Not](#FnNot)
        - [Fn::Or](#FnOr)
  - [Wait Conditions/Creation Policy](#Wait-Conditons/Creation-Policy)
  - [Nested Stack](#Nested-Stack)
  - [Resource Deletion Policies](#Resource-Deletion-Policies)
  - [Stack Updates](#Stack-Updates)
  - [Custom Resources](#Custom-Resources)
  - [CloudFormation Best Practices](#CloudFormation-Best-Practices)

## Cloudformation
### Key Terms
1. Stack - manage related resources as a single unit called a stack. Create, update, and delete a collection of resources by creating, updating, and deleting stacks. All the resources in a stack are defined by the stack's AWS CloudFormation template.
2. Template - JSON or YAML formatted text file. AWS CloudFormation uses these templates as blueprints for building your AWS resources.
3. Stack Policy - IAM style policy statement which governs what can be changed and who can change it.

### Anatomy of a Template
- Format Version (optional)
  - The AWS CloudFormation template version that the template conforms to. The template format version is not the same as the API or WSDL version.
- Description (optional)
  - A text string that describes the template. This section must always follow the template format version section.
- Metadata (optional)
  - Objects that provide additional information about the template.
- **Parameters** (optional)
  - Values to pass to your template at runtime (when you create or update a stack).
- **Mappings** (optional)
  - A mapping of keys and associated values that you can use to specify conditional parameter values, similar to a lookup table. You can match a key to a corresponding value by using the Fn::FindInMap intrinsic function in the Resources and Outputs sections.
- Conditions (optional)
  - Conditions that control whether certain resources are created or whether certain resource properties are assigned a value during stack creation or update. For example, you could conditionally create a resource that depends on whether the stack is for a production or test environment.
- Transform (optional)
  - For serverless applications (also referred to as Lambda-based applications), specifies the version of the AWS Serverless Application Model (AWS SAM) to use. When you specify a transform, you can use AWS SAM syntax to declare resources in your template. The model defines the syntax that you can use and how it is processed.
  - You can also use AWS::Include transforms to work with template snippets that are stored separately from the main AWS CloudFormation template. You can store your snippet files in an Amazon S3 bucket and then reuse the functions across multiple templates.
- **Resources** (required)
  - Specifies the stack resources and their properties, such as an Amazon Elastic Compute Cloud instance or an Amazon Simple Storage Service bucket. You can refer to resources in the Resources and Outputs sections of the template.
- **Outputs** (optional)
  - Describes the values that are returned whenever you view your stack's properties. For example, you can declare an output for an S3 bucket name and then call the aws cloudformation describe-stacks AWS CLI command to view the name.

## Intrinsic Functions
### Fn::Base64
- Returns the Base64 representation of the input string. This function is typically used to pass encoded data to Amazon EC2 instances by way of the UserData property.

JSON
```
{ "Fn::Base64" : valueToEncode }
```
YAML
```
!Base64 valueToEncode
```

### Fn::Cidr
- Returns an array of CIDR address blocks. The number of CIDR blocks returned is dependent on the count parameter.

JSON
```
{ "Fn::Cidr" : [ipBlock, count, cidrBits]}
```
YAML
```
!Cidr [ ipBlock, count, cidrBits ]
```
- **ipBlock**:
The user-specified CIDR address block to be split into smaller CIDR blocks.
- **count**:
The number of CIDRs to generate. Valid range is between 1 and 256.
- **cidrBits**:
The number of subnet bits for the CIDR. For example, specifying a value "8" for this parameter will create a CIDR with a mask of "/24".

### Fn::FindInMap
- Returns the value corresponding to keys in a two-level map that is declared in the Mappings section
JSON
```
{ "Fn::FindInMap" : [ "MapName", "TopLevelKey", "SecondLevelKey"] }
```
YAML
```
!FindInMap [ MapName, TopLevelKey, SecondLevelKey ]
```
- **MapName**:
- The logical name of a mapping declared in the Mappings section that contains the keys and values.
- **TopLevelKey**:
The top-level key name. Its value is a list of key-value pairs.
- **SecondLevelKey**:
The second-level key name, which is set to one of the keys from the list assigned to TopLevelKey.

### Fn::GetAtt
- Returns the value of an attribute from a resource in the template.
JSON
```
{ "Fn::GetAtt" : [ "logicalNameOfResource", "attributeName" ] }
```
YAML
```
!GetAtt logicalNameOfResource.attributeName
```

### Fn::GetAZs
- Returns an array that lists Availability Zones for a specified region. Prevents hard-coding a full list of Availability Zones for a specified region.
JSON
```
{ "Fn::GetAZs" : "region" }
```
YAML
```
!GetAZs region
```

### Fn::ImportValue
- Returns the value of an output exported by another stack. You typically use this function to create cross-stack references.
Note:
- For each AWS account, Export names must be unique within a region.
- You can't create cross-stack references across regions.
- For outputs, the value of the Name property of an Export can't use Ref or GetAtt functions that depend on a resource.
- Similarly, the ImportValue function can't include Ref or GetAtt functions that depend on a resource.
- You can't delete a stack if another stack references one of its outputs.
- You can't modify or remove an output value that is referenced by another stack.
JSON
```
{ "Fn::ImportValue" : sharedValueToImport }
```
YAML
```
!ImportValue sharedValueToImport
```
- Note:
You can't use the short form of !ImportValue when it contains a !Sub.

Example:
```
Fn::ImportValue:
  !Sub "${NetworkStack}-SubnetID"
```
### Fn::Join
- Appends a set of values into a single value, separated by the specified delimiter. If a delimiter is the empty string, the set of values are concatenated with no delimiter.
JSON
```
{ "Fn::Join" : [ "delimiter", [ comma-delimited list of values ] ] }
"Fn::Join" : [ ":", [ "a", "b", "c" ] ]
```
YAML
```
!Join [ delimiter, [ comma-delimited list of values ] ]
!Join [ ":", [ a, b, c ] ]
!Join
  - ''
  - - 'arn:'
    - !Ref Partition
    - ':s3:::elasticbeanstalk-*-'
    - !Ref 'AWS::AccountId'
```

### Fn::Select
- Returns a single object from a list of objects by index.

Note:
- Fn::Select does not check for null values or if the index is out of bounds of the array. Both conditions will result in a stack error, so you should be certain that the index you choose is valid, and that the list contains non-null values.

JSON
```
{ "Fn::Select" : [ index, listOfObjects ] }
{ "Fn::Select" : [ "1", [ "apples", "grapes", "oranges", "mangoes" ] ] }
```
YAML
```
!Select [ index, listOfObjects ]
!Select [ "1", [ "apples", "grapes", "oranges", "mangoes" ] ]
```

### Fn::Split
- Split a string into a list of string values so that you can select an element from the resulting string list. Specify the location of splits with a delimiter, such as , (a comma). After you split a string, use the Fn::Select function to pick a specific element.
JSON
```
{ "Fn::Split" : [ "|" , "a|b|c" ] }
{ "Fn::Select" : [ "2", { "Fn::Split": [",", {"Fn::ImportValue": "AccountSubnetIDs"}]}] }
```
YAML
```
!Split [ "|" , "a|b|c" ]
!Select [2, !Split [",", !ImportValue AccountSubnetIDs]]
```
### Fn::Sub
- Substitutes variables in an input string with values that you specify
JSON
```
{ "Fn::Sub" : [ String, { Var1Name: Var1Value, Var2Name: Var2Value } ] }
{ "Fn::Sub": [ "www.${Domain}", { "Domain": {"Ref" : "RootDomainName" }} ]}
```
YAML
```
!Sub
  - String
  - { Var1Name: Var1Value, Var2Name: Var2Value }
Name: !Sub
  - www.${Domain}
  - { Domain: !Ref RootDomainName }
```

### Fn::Transform
- Specifies a macro to perform custom processing on part of a stack template. Macros enable you to perform custom processing on templates, from simple actions like find-and-replace operations to extensive transformations of entire templates.
- You can also use Fn::Transform to call the AWS::Include Transform transform, which is a macro hosted by AWS CloudFormation.
JSON
```
{ "Fn::Transform" : { "Name" : macro name, "Parameters" : {key : value, ... } } }
```
YAML
```
!Transform { "Name" : macro name, "Parameters" : {key : value, ... } }
```
### Ref
- Returns the value of the specified parameter or resource.
JSON
```
{ "Ref" : "logicalName" }
```
YAML
```
!Ref logicalName
```

### Condition Functions
- Use intrinsic functions to conditionally create stack resources. Conditions are evaluated based on input parameters that you declare when you create or update a stack.
- Define all conditions in the Conditions section of a template except for Fn::If

#### Condition Intrinsic Functions
- ##### Fn::And
  - `Fn::And: [condition]`
  - `!And [condition]`
  - Examples:
  ```
  "MyAndCondition": {
      "Fn::And": [
          {"Fn::Equals": ["sg-mysggroup", {"Ref": "ASecurityGroup"}]},
          {"Condition": "SomeOtherCondition"}
      ]
    }
  ```
  ```
  MyAndCondition: !And
  - !Equals ["sg-mysggroup", !Ref ASecurityGroup]
  - !Condition SomeOtherCondition

  ```
- ##### Fn::Equals
  - `"Fn::Equals" : ["value_1", "value_2"]`
  - `!Equals [value_1, value_2]`
  - Examples:
  ```
  "UseProdCondition" : {
    "Fn::Equals": [
        {"Ref": "EnvironmentType"},
        "prod"
    ]
  }
  ```
  ```
  UseProdCondition:
    !Equals [!Ref EnvironmentType, prod]
  ```
- ##### Fn::If
  - `"Fn::If": [condition_name, value_if_true, value_if_false]`
  - `!If [condition_name, value_if_true, value_if_false]`
  - Examples:
  ```
  "SecurityGroups" : [{
    "Fn::If" : [
      "CreateNewSecurityGroup",
      {"Ref" : "NewSecurityGroup"},
      {"Ref" : "ExistingSecurityGroup"}
    ]
  }]
  ```
  ```
  SecurityGroups:
    - !If [CreateNewSecurityGroup, !Ref NewSecurityGroup, !Ref ExistingSecurityGroup]
  ```
- ##### Fn::Not
  - `"Fn::Not": [{condition}]`
  - `!Not [condition]`
  - Examples:
  ```
  "MyNotCondition" : {
    "Fn::Not" : [{
        "Fn::Equals" : [
          {"Ref" : "EnvironmentType"},
          "prod"
        ]
    }]
  }
  ```
  ```
  MyNotCondition:
    !Not [!Equals [!Ref EnvironmentType, prod]]
  ```
- ##### Fn::Or
  - `"Fn::Or": [{condition}, {...}]`
  - `!Or [condition, ...]`
  - Examples:
  ```
  "MyOrCondition" : {
    "Fn::Or" : [
        {"Fn::Equals" : ["sg-mysggroup", {"Ref" : "ASecurityGroup"}]},
        {"Condition" : "SomeOtherCondition"}
    ]
  }
  ```
  ```
  MyOrCondition:
    !Or [!Equals [sg-mysggroup, !Ref ASecurityGroup], Condition: SomeOtherCondition]
  ```
## Wait Conditons/Creation Policy

**CreationPolicy** attribute prevents resource's status from reaching create complete until AWS CloudFormation receives a specified number of success signals or the timeout period is exceeded.
- Use the cfn-signal helper script or SignalResource API to send signal
- Only available for following resources
  - AWS::AutoScaling::AutoScalingGroup
  - AWS::EC2::Instance
  - AWS::CloudFormation::WaitCondition

##### Example:
In template:
```
CreationPolicy:
  AutoScalingCreationPolicy:
    MinSuccessfulInstancesPercent: Integer
  ResourceSignal:
    Count: Integer
    Timeout: String
MyInstance:
  Type: AWS::EC2::Instance
  Properties:
    UserData: !Base64
      'Fn::Join':
        - ''
        - - |
            #!/bin/bash -x
          - |
            # Install the files and packages from the metadata
          - '/opt/aws/bin/cfn-init -v '
          - '         --stack '
          - !Ref 'AWS::StackName'
          - '         --resource MyInstance '
          - '         --region '
          - !Ref 'AWS::Region'
          - |+

          - |
            # Signal the status from cfn-init
          - '/opt/aws/bin/cfn-signal -e $? '
          - '         --stack '
          - !Ref 'AWS::StackName'
          - '         --resource MyInstance '
          - '         --region '
          - !Ref 'AWS::Region'
          - |+
```
In most conditions, CreationPolicy is preferable to **WaitConditon**. Use a wait condition for situations like the following:

- To coordinate stack resource creation with configuration actions that are external to the stack creation

- To track the status of a configuration process

##### Example
```
WebServerGroup:
  Type: AWS::AutoScaling::AutoScalingGroup
  Properties:
    AvailabilityZones:
      Fn::GetAZs: ""
    LaunchConfigurationName:
      Ref: "LaunchConfig"
    MinSize: "1"
    MaxSize: "5"
    DesiredCapacity:
      Ref: "WebServerCapacity"
    LoadBalancerNames:
      -
        Ref: "ElasticLoadBalancer"
WaitHandle:
  Type: AWS::CloudFormation::WaitConditionHandle
WaitCondition:
  Type: AWS::CloudFormation::WaitCondition
  DependsOn: "WebServerGroup"
  Properties:
    Handle:
      Ref: "WaitHandle"
    Timeout: "300"
    Count:
      Ref: "WebServerCapacity"
```

## Nested Stack
Stack is a resource which has following benefits
- Overcome limits of CloudFormation
- Split large number of resources over multiple templates
- Reuse common template patterns


## Resource Deletion Policies
Three types of DeletionPolicy for each resource
- Delete (default)
- Retain
- Snapshot (only on a few services)

## Stack Updates
Use Stack Policy to control actions
Example:
```
{
  "Statement" : [
    {
      "Effect" : "Allow",
      "Action" : "Update:*",
      "Principal": "*",
      "Resource" : "*"
    },
    {
      "Effect" : "Deny",
      "Action" : "Update:*",
      "Principal": "*",
      "Resource" : "LogicalResourceId/ProductionDatabase"
    }
  ]
}
```
- No stack policy = allow all updates
- Once a stack policy is applied, can't be deleted
- Once a policy is applied, all resources are protected by default
  - `Update:*` is denied

Updates can impact a resource in 4 ways, depending on resource
1. No interruption
2. Some interruption
3. Replacement
4. Deletion

## Custom Resources
Create resources outside of the available AWS resources.
Involves 3 parties:
1. Template developer
    - Creates a template that includes a custom resource type. The template developer specifies the service token and any input data in the template.
2. custom resource provider
    - Owns the custom resource and determines how to handle and respond to requests from AWS CloudFormation. The custom resource provider must provide a service token that the template developer uses.
3. AWS CloudFormation
    - During a stack operation, sends a request to a service token that is specified in the template, and then waits for a response before proceeding with the stack operation.

Steps of creating a custom resource
1. Template developer defines a custom resource in his or her template, which includes a service token and any input data parameters.
    - input data might be required; service token is always required.
    - service token specifies where AWS CloudFormation sends requests to, such as to an Amazon SNS topic ARN or to an AWS Lambda function ARN
2. During create, update, or delete a custom resource, AWS CloudFormation sends a request to the specified service token.
    - service token must be in the same region in which you are creating the stack.
    -  CloudFormation includes information such as the request type and a pre-signed Amazon Simple Storage Service URL, where the custom resource sends responses
3. Custom resource provider processes the AWS CloudFormation request and returns a response of SUCCESS or FAILED to the pre-signed URL.
    - Response is in a JSON-formatted file
    - Custom resource provider can also include name-value pairs that the template developer can access
4. After getting a SUCCESS response, AWS CloudFormation proceeds with the stack operation. If a FAILED response or no response is returned, the operation fails.


## CloudFormation Best Practices
- Organize Your Stacks By Lifecycle and Ownership
  - Two common frameworks:
    1. **multi-layered architecture**
      - Layered architecture organizes stacks into multiple horizontal layers that build on top of one another, where each layer has a dependency on the layer directly below it. Layers can have multiple stacks grouped by owner/lifecycle.
    2. **service-oriented architecture (SOA)**
      -  A service-oriented architecture organizes problems into manageable parts, each with a clearly defined purpose representing a self-contained unit of functionality. You can map these services to a stack, with its own lifecycle and owners.
- Use Cross-Stack References to Export Shared Resources
  - Use cross-stack references to export resources from a stack so that other stacks can use them. Stacks can use the exported resources by calling them using Fn::ImportValue
- Use IAM to Control Access
  - Manage users permissions for viewing stack templates, creating stacks, or deleting stacks
  - Separate permissions between a user and the AWS CloudFormation service using a service role. AWS CloudFormation uses the service role's policy to make calls instead of the user's policy.
- Verify Quotas for All Resource Types
  - for example: you can only launch 200 AWS CloudFormation stacks per region in your AWS account
- Reuse Templates to Replicate Stacks in Multiple Environments
- Use Nested Stacks to Reuse Common Template Patterns
- Do Not Embed Credentials in Your Templates
- Use AWS-Specific Parameter Types
  - For example, you can specify a parameter as type AWS::EC2::KeyPair::KeyName, which takes an existing key pair name that is in your AWS account and in the region where you are creating the stack
- Use Parameter Constraints
- Use AWS::CloudFormation::Init to Deploy Software Applications on Amazon EC2 Instances
  - Describe the configurations that you want rather than scripting procedural steps. Update configurations without recreating instances.
- Use the Latest Helper Scripts
  - Inlcude following command in userdata: `yum install -y aws-cfn-bootstrap`
- Validate Templates Before Using Them
  - check for valid json or yaml
- Manage All Stack Resources Through AWS CloudFormation
 - do not update resources outside of stack template
- Create Change Sets Before Updating Your Stacks
  - See how proposed changes to a stack might impact your running resources before you implement them.
- Use Stack Policies
  - prevent unintentional disruptions by protecting important resources
- Use Code Reviews and Revision Controls to Manage Your Templates
- Update Your Amazon EC2 Linux Instances Regularly
  - `yum update`
