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

### Intrinsic Functions
#### Fn::Base64
- Returns the Base64 representation of the input string. This function is typically used to pass encoded data to Amazon EC2 instances by way of the UserData property.

JSON
```
{ "Fn::Base64" : valueToEncode }
```
YAML
```
!Base64 valueToEncode
```

#### Fn::Cidr
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

#### Fn::FindInMap
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

#### Fn::GetAtt
- Returns the value of an attribute from a resource in the template.
JSON
```
{ "Fn::GetAtt" : [ "logicalNameOfResource", "attributeName" ] }
```
YAML
```
!GetAtt logicalNameOfResource.attributeName
```

#### Fn::GetAZs
- Returns an array that lists Availability Zones for a specified region. Prevents hard-coding a full list of Availability Zones for a specified region.
JSON
```
{ "Fn::GetAZs" : "region" }
```
YAML
```
!GetAZs region
```

#### Fn::ImportValue
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
#### Fn::Join
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

#### Fn::Select
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

#### Fn::Split
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
#### Fn::Sub
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

#### Fn::Transform
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
#### Ref
- Returns the value of the specified parameter or resource.
JSON
```
{ "Ref" : "logicalName" }
```
YAML
```
!Ref logicalName
```
