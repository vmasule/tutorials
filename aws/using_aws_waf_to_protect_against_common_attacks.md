## Using AWS WAF to Protect Against Common Attacks
### Introduction
This learning activity will teach how AWS Web Application Firewall (WAF) can be utilized to protect an environment from common types of attacks.

This learning activity uses several AWS services including CloudFront, Route 53, CloudFormation, and S3. WAF ties these services together to protect an envrionment. The learning activity provides a web-enabled S3 bucket to serve as an endpoint for a CloudFront Distribution. A provided CloudFormation template will be used to configure WAF. Once WAF is configured, it can be attached to the CloudFront Distribution to provide protection against common attacks.

### Solution
Please log into the AWS environment by using the cloud_user credentials provided. Once inside the AWS account, make sure you to use US-East-1 (N. Virginia) as the selected region.

The learning activity uses a CloudFormation template to create WAF protection from common attacks. This template can be found here:

https://s3.amazonaws.com/cloudformation-examples/community/common-attacks.json

When creating the CloudFormation Stack, select the radio button for Specify an Amazon S3 Template URL and enter the template URL in the associated field.

### Create a CloudFront Distribution
In the AWS Management Console, select Services from the top menu.

Select CloudFront.

Click Create Distribution.

Click Get Started under the web distribution option.

Configure the settings for the distribution:

Select the box next to Origin Domain Name.

Select the Amazon S3 bucket from the list. The entry will have static in the name. This is the S3 bucket provided to you as part of the exercise.

Scroll down to the Distribution Settings section.

Open a new tab and navigate to Route 53 Management Console.

Click on Hosted zones on the left-hand menu or on the main landing page.

Find the domain name for the domain created for this lab. Click on the link.

Select the name for the record set and copy it to your clipboard.

Navigate back to the tab in CloudFront from earlier. Paste the name from the previous step into the box next to Alternate Doman Names (CNAMEs). Delete the trailing period and add "cdn." to the start of it. For example, if you had "cmcloudlab203.info." copied, the final entry would be "cdn.cmcloudlab203.info".

Select the Custom SSL Certificate option.

Click Request or Import a Certificate with ACM.

In the new window that appears, enter the domain name from earlier into the Domain name box. Using our example, that would be "cdn.cmcloudlab203.info".

Click Next.

Click Review.

Click Confirm and request.

Expand the domain by clicking the arrow. Click Create record in Route 53.

Click Create.

Once the status has changed to "Issued", select the ARN and copy it to your clipboard.

Go back to your previous tab in CloudFront where you are creating the distribution. Paste the ARN into the box under the button for Custom SSL Certificate.

Click Create Distribution at the bottom of the form.

Wait for the status for the distribution to update to Deployed. This takes 10-15 minutes on average.

### Create a CloudFormation Stack for WAF
In the AWS Management Console, select Services from the top menu.

Select CloudFormation.

Click Create Stack.

Under Choose a template, select the radio button to specify an Amazon S3 URL.

Enter "https://s3.amazonaws.com/cloudformation-examples/community/common-attacks.json" into the box provided.

Click Next in the bottom right-hand corner.

Under Specify Details, click on the Stack name box and enter the name "WAFLearningActivity".

Click Next in the bottom right-hand corner.

On the Options screen, scroll to the bottom and click Next in the bottom right-hand corner.

Click Create in the bottom right-hand corner.

The browser should automatically navigate back to the CloudFormation screen. Refresh the page to see the newly created stack.

Select the WAFLearningActivity stack to verify the status in the bottom half of the screen.

### Configure WAF for Protection Against Attacks
NOTE: Switch to AWS WAF Classic

In the AWS Management Console, select Services from the top menu.

Select AWS WAF.

Click Web ACLs.

Click on the CommonAttackProtection Web ACL.

Associate the Web ACL with the CloudFront distribution.

Click on the Rules tab.

Next to AWS resources using this web ACL, click Add assocation.

On the window that pops up, click on the box next to Resource and select the distribution created in the previous task.

Click Add.

Add IP addresses to the IP Match condition.

Select IP addresses from the navigation pane on the left-hand side.

Click Manual IP Block Set.

Click Add IP addresses or ranges.

In the Address box, enter "192.0.2.0/24". This will block all IP addresses ranging from 192.0.2.0 to 192.0.2.255.

Click Add IP address or range.

Click Add.

configure the Web ACL to block large bodies.

Select Web ACLs from the navigation pane on the left-hand side.

Click on the CommonAttackProtection Web ACL.

Click on the Rules tab.

Click Edit web ACL.

Find the row associated with the CommonAttackProtectionLargeBodyMatchRule rule and select the Block option for that row.

Click Update.