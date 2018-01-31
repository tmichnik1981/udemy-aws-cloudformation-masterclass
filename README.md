# Udemy AWS CloudFormation Masterclass

#### Links

- [author's github](https://github.com/simplesteph)
- [author's linkedin](https://www.linkedin.com/in/stephanemaarek/)
- [JSON To YAML converter](https://www.json2yaml.com/ )

#### First template

- Templates have to be uploaded in S3 and then referenced in CloudFormation
- To update a templte we can't edit previous ones. We have to re-upload a new version of the template to AWS
- Stacks are identified by a name
- Deleting a stack deletes every single artifact that was created by ClouFormation

1. Go to aws.amazon.com / services / CloudFormation
2. Click Create new stack
   - Select **Upload a template to Amazon S3** and file **0-just-ec2.yaml**
   - Next
   - Stack name: introduction
   - Next
   - Tags
     - Key: Course
     - Value: Udemy
   - Next
   - Create. Everything what was in yml file will be created.

##### Updating stack

1. Select your stack
2. Actions / update
3. Once again select **Upload a template to Amazon S3**, and this time choose file **1-ec2-with-sg-eip.yaml** 

##### Deleting stack and resources

1. Go to your stack
2. Select
3. Actions / delete stack and confirm. Stack and all resources created by cloudformation will be deleted 

#### YAML

```yaml
# YAML files should end in .yaml whenever possible in Grav.
# YAML is case sensitive.
# YAML does not allow the use of tabs. Spaces are used instead as tabs are not universally supported.
# comments '#'
#Hello there, how are you?

# scalars
integer: 25
string: "25"
float: 25.0
boolean: Yes

# maps
minecraft-tag: 'minecraft'
cancelled-tag: 'cancelled'
# in line map
{minecraft-tag: 'minecraft', cancelled-tag: 'cancelled'}

# lists
mylist:
- 'item 1'
- 'item 2'

mylist:
    - 100
    - 200
#in line list
mylist: [100, 200]

# maps inside lists
channels:
  - name: '#mychannel'
    password: ''
  - name: '#myprivatechannel'
    password: 'mypassword'
    
# multiline values
include_newlines: |
            exactly as you see
            will appear these three
            lines of poetry
# All values are in the same line. Line breaks are ignored
ignore_newlines: >
            this is really a
            single line of text
            despite appearances
```

#### Creating S3 Bucket

- [S3 CloudFormation docs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket.html)

##### Basic S3 Bucket

```yaml
---
# always start with 'Resources'
Resources:
# your resource name
  MyS3Bucket:
  # type in accordance with AWS docs 
    Type: "AWS::S3::Bucket"
    Properties: {}

```

##### Updating S3 bucket

- Updates with no interruption (AccessControl)
- Replacements updates, such as updating the name of the bucket

###### Adding access control

```yaml
Resources:
  MyS3Bucket:
    Type: "AWS::S3::Bucket"
    Properties:
     # key and value in accordance with AWS docs 
      AccessControl: PublicRead
```

1. Go to  aws.amazon.com / Services / CloudFormation
2. Select previously created **whatever-you-want-s3 stack**
3. Actions / Update Stack
4. Choose **Upload a template to Amazon S3** and select new yaml file
5. Next

###### Renaming S3 bucket - requires replacement

```yaml
Resources:
  MyS3Bucket:
    Type: "AWS::S3::Bucket"
    Properties:
      AccessControl: PublicRead
      # new name (Key as in AWS docs  )
      BucketName: "insert-some-random-string-here-123456"
```

#### CloudFormation template options

- Tags
- Permissions
- Notification Options
- Timeouts
- Rollback on Failure
- Stack Policy

#### CloudFormation Designer

- drag and drop elements
- validate
- edit template
- create stack

#### CloudFormation Building Blocks

##### Templates componens

1. Resources: your AWS resources declared in the template
2. Parameters: the dynamic inputs for your template
3. Mappings: the static variables for your template
4. Outputs: References to what has been created
5. Conditionals: List of conditions to perform resource creation
6. Metadata

##### Templates helpers

1. References
2. Functions

#### CloudFormation Parameters 

- The way to provide inputs to your AWS CF templates
- Important when
  - You want to reuse your templates across the company
  - Some inputs can not determined ahead of time
- Parameters are powerful, controlled and  can prevent errors from happening in your templates thanks to types 
- Parameters can be controlled by:
  - Type:
    - String
    - Number
    - CommaDelimitedList
    - List<Type>
    - AWS Parameter (catch invalid values - match against existing values in the AWS Account)
  - Description
  - Constraints
  - ConstraintsDescription
  - Min/MaxLength
  - Min/MaxValue
  - Defaults
  - AllowedValues (array)
  - AllowedPattern (regexp)
  - NoEcho (Boolean)

```yaml
# for example
Parameters:
  SecurityGroupDescription:
    Description: Security Group Description (Simple parameter)
    Type: String
  SecurityGroupPort:
    Description: Simple Description of a Number Parameter, with MinValue and MaxValue
    Type: Number
    MinValue: 1150
    MaxValue: 65535
  InstanceType:
    Description: WebServer EC2 instance type (has default, AllowedValues)
    Type: String
    Default: t2.small
    AllowedValues:
      - t1.micro
      - t2.nano
      - t2.micro
      - t2.small
    ConstraintDescription: must be a valid EC2 instance type.
    
# referencing by !Ref <param_name>
Resources:
  MyEC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      #we reference the InstanceType parameter
      InstanceType: !Ref InstanceType
      KeyName: !Ref KeyName
      ImageId: "ami-a4c7edb2"
      # here we reference an internal CloudFormation resource
      SubnetId: !Ref DbSubnet1
```

##### Referencing parameters

- **Fn::Ref** - function can be leveraged to reference parameters
- Parameters can be used anywhere in a template
- The shorthand for this in YAML is **!Ref**
- **!Ref** can be used to reference everything not only parameters

#### Resources

- [Resources docs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)
- [EC2 instance CF docs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-instance.html)
- [EC2 Elastic IP CF docs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-eip.html)
- [EC2 Security group CF docs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-security-group.html)
- Resources are the core of your CloudFormation template
- They represent th different AWS Components that will be created and configured
- Resources are declared and can reference each other
- AWS figures out creation, updates and deletes of resources for us
- There are over 224 types of resources 
- Resource types identifiers are of the form:
  - **AWS::aws-product-name::data-type-name**

##### Optional attributes for Resources

- DependsOn: very useful to draw a dependency between two resources. For example, only create an ECS cluster after creating an ASG
- DeletionPolicy: protect resources from being deleted even if the cloudformation is deleted
- CreationPolicy: more details on the section on CFN init
- Metadata: anything you want

#### CloudFormation Mappings

- Mappings are fixed variables within your CF Template

- They are very handy to differentiate between different environments (dev vs prod), regions (AWS regions), AMI types, etc

- All the values are hardcoded within the template

  ```yaml
  Mappings:
  	Mapping01:
  		Key01:
  			Name: Value01
  		Key02:
  			Name: Value02
  		Key03:
  			Name: Value03
  		
  ```

- We use Fn::FindInMap to return a named value from a specific key

- !FindInMap [MapName, TopLevelKey, SecondLevelKey]

  ```yaml
  Mappings:
    AWSRegionArch2AMI:
      us-east-1:
        HVM64: ami-6869aa05
      us-west-2:
        HVM64: ami-7172b611
      us-west-1:
        HVM64: ami-31490d51
      
  Resources:
    EC2Instance:
      Type: AWS::EC2::Instance
      Properties:
        InstanceType: !FindInMap [EnvironmentToInstanceType, !Ref 'EnvironmentName', instanceType]
        # Note we use the pseudo parameter AWS::Region
        ImageId: !FindInMap [AWSRegionArch2AMI, !Ref 'AWS::Region', HVM64]
  ```

#### Pseudo Parameters

- AWS offers us pseudo parameters in any CloudFormation templates

- These can be used at any time and are enabled by default

  | Reference Value       | Example Return Value                     |
  | --------------------- | ---------------------------------------- |
  | AWS::AccountId        | 1234567890                               |
  | AWS::NotificationARNs | [arn:aws:sns:us-east-1:123456789012:MyTopic] |
  | AWS::NoValue          | Does not return a value                  |
  | AWS::Region           | us-east-2                                |
  | AWS::StackId          | arn:aws:cloudformation:us-east-1:12345678912:stack/MyStack/1c2fa620-982a-11e3-aff7-50e2416294e0 |
  | AWS::StackName        | MyStack                                  |

#### CloudFormation Outputs

- The Outputs section declares optional outputs values that we can import into other stacks

- You can also view the outputs in the AWS Console or in using the AWS CLI

  ```yaml
  Outputs:
  	Logical ID:
  		Description: Information about the value
  		Value: Value to return
           Export:
           	Name: Value to export
  ```

- Outputs allow to expose some value form one stack to another

- The other stack will use `!ImportValue SSHSecurityGroup` to get access to exposed values

  ```yaml
  # I stack
  Resources:
    # here we define a SSH security group that will be used in the entire company
    MyCompanyWideSSHSecurityGroup:
      # http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-security-group.html
      Type: AWS::EC2::SecurityGroup
      Properties:
  #------------------#
  Outputs:
    StackSSHSecurityGroup:
      Description: The SSH Security Group for our Company
      Value: !Ref MyCompanyWideSSHSecurityGroup
      Export:
        Name: SSHSecurityGroup
  # II stack
  Resources:
    MySecureInstance:
      # http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-instance.html
      Type: AWS::EC2::Instance
      Properties:
        AvailabilityZone: us-east-1a
        ImageId: ami-a4c7edb2
        InstanceType: t2.micro
        SecurityGroups:
          # we reference the output here, using the Fn::ImportValue function
          - !ImportValue SSHSecurityGroup
  ```

#### Conditionals

- Conditionals are used to control the creation of resources of outputs based on a condition

- Conditions can be whatever you want them to be, but common ones are:

  - Environment (dev / test /prod)
  - AWS Region
  - Any parameter value

- Each condition can reference another condition, parameter value or mapping

- ​

  ```yaml
  Conditions:
  	# any ID to want 
  	Logical ID:
  	# Fn::And, Fn::Equals, Fn::If, Fn::Not, Fn::Or 
  		Intrinsic function
  ```

  ​