# Udemy AWS CloudFormation Masterclass

#### Links

- [authors github](https://github.com/simplesteph)
- [linked](https://www.linkedin.com/in/stephanemaarek/)

#### First template

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