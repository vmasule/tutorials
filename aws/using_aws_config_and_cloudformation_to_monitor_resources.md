# Using AWS Config and CloudFormation to Monitor Resources
Introduction
In this hands-on lab, we will use AWS Config to monitor the resources in our account. Specifically, we will configure a rule (via a reusable CloudFormation template) to check if our EC2 instances have detailed monitoring. We will then set up an SNS topic, which will be linked to our Config rule. After subscribing to the SNS topic, we will be alerted via email when there are any changes to our environment.

## Solution
Log in to the live AWS environment using the credentials provided. Make sure you're in the N. Virginia (us-east-1) region throughout the lab.

### Configuring AWS Config
#### Set Up Config
1. Navigate to EC2.
2. Click the running instances link.
3. Open a new browser tab, and navigate to Config.
4. Click Get started.
5. On the Settings page, set the following values:
* Resource types to record
    * All resources: Check Record all resources supported in this region
* Amazon S3 bucket
    * Select Create a bucket
    * Bucket name: Leave as-is
* Amazon SNS topic
    * Check Stream configuration changes and notifications to an Amazon SNS topic.
    * Select Create a topic
    * Topic name: Leave as-is
* AWS Config role
    * Select Choose a role from your account
    * Role name: Select the listed cfst- role
6. Click Next.
7. We'll set up our rules via a template, so click Next.
8. On the Review page, click Confirm.
9. Head to the Settings page to make sure recording is turned on.

NOTE: Sometimes Config gets "stuck" in the taking inventory phase. Later on, if your Config rule is not working properly, head back to its settings. If the recorder is still taking inventory, turn the recorder off and then back on. It should then complete taking inventory and recognize your in-scope resources, making your rule evaluate properly.

### Subscribe to SNS Topic
1. Open two new new browser tabs
In one, navigate to Simple Notification Service (SNS).
In the other, navigate to your inbox.
The SNS Dashboard has changed.
Click the 3 horizontal lines icon in the top right to cascade out a menu of navigation options.
Choose Topics. Click the config-topic topic.
Choose Create subscription.
Choose email as the Protocol, enter your email as the Endpoint and Create subscription.
Check your email.
8. In the subscription confirmation email, click Confirm subscription.
## Using CloudFormation to Create an AWS Config Rule
### Add CloudFormation Template
1. Open another browser tab, and navigate to CloudFormation.
Click Design template.
Click the Template tab at the bottom of the new template box.
Add the following template:
```
 {
   "Resources": {
     "AWSConfigRule": {
       "Type": "AWS::Config::ConfigRule",
       "Properties": {
         "ConfigRuleName": {
           "Ref": "ConfigRuleName"
         },
         "Description": "Checks whether detailed monitoring is enabled for EC2 instances.",
         "InputParameters": {},
         "Scope": {
           "ComplianceResourceTypes": [
             "AWS::EC2::Instance"
           ]
         },
         "Source": {
           "Owner": "AWS",
           "SourceIdentifier": "EC2_INSTANCE_DETAILED_MONITORING_ENABLED"
         }
       }
     }
   },
   "Parameters": {
     "ConfigRuleName": {
       "Type": "String",
       "Default": "ec2-instance-detailed-monitoring-enabled",
       "Description": "The name that you assign to the AWS Config rule.",
       "MinLength": "1",
       "ConstraintDescription": "This parameter is required."
     }
   },
   "Metadata": {
     "AWS::CloudFormation::Interface": {
       "ParameterGroups": [
         {
           "Label": {
             "default": "Required"
           },
           "Parameters": []
         },
         {
           "Label": {
             "default": "Optional"
           },
           "Parameters": []
         }
       ]
     }
   },
   "Conditions": {}
 }
```
Click the upload icon at the top to run the template.

Click Next.
Give it a Stack name of configrule.
Click Next.
We aren't changing the options, so click Next.
Click Create.
11. Refresh the stacks page to see the new stack.
### Verify the Config Rule Was Created
1. Back in the AWS Config browser tab, click Rules in the left-hand menu.
2. Once it's done evaluating, it may take up to 10 minutes for the rule to change from non-compliant to compliant.
On the EC2 instances page, in the Monitoring section, click the link to Enable Detailed Monitoring.
In the dialog, click Yes, Enable.
On the Config page, click the rule.
It's still non-compliant. Click Re-evaluate. (It could take up to five minutes to become compliant.)
Keep an eye on your inbox, as you should receive an email after a few minutes notifying you the instance is compliant.
8. In the Config console, refresh the page to see the rule is compliant.