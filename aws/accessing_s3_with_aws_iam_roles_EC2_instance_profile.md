https://github.com/natonic/AWS_SA_Pro/tree/master/IAM_S3_Roles

Accessing S3 with AWS IAM Roles
This hands-on lab will focus on using EC2 roles to grant access to AWS resources. Specifically, an IAM role will be created and attached to an EC2 instance that will give the instance access to an S3 bucket. This process can be completed in the AWS Management Console. After a brief walkthrough of how that can be done in the Management Console, the lab will focus on using a CloudFormation Template to complete this task. Putting such tasks in a CloudFormation template promotes reuse, documentation, and efficiency of effort. After the CloudFormation Stack is created, which will attach the IAM role to the EC2 instance, the permissions will be verified.

Solution
Log in to the live AWS environment using the credentials provided. Make sure you're in the N. Virginia (us-east-1) region throughout the lab.

Before We Create the Stack
Note: The key pair creation and retrieval of VPC information is done during the CloudFormation stack creation in the solution video, but we've included it here first so you'll already have those values ready.

Create Key Pair
Navigate to EC2 > Key Pairs.
Click Create key pair.
Give it a Key pair name of "s3roleslearningactivity".
Make sure the .pem format is selected.
Click Create.
Get VPC Information
Navigate to VPC > Your VPCs.
Copy the VPC ID of the VPC in the list.
Paste this information into a text file, as we'll need it in a minute.
Click Subnets in the left-hand menu.
Copy the subnet ID of the first subnet in the list.
Paste this information into the same text file, as we'll also need it shortly.
Create CloudFormation Stack
Navigate to CloudFormation.
Click Create stack > With new resources (standard).
Click Create template in Designer.
Click Create template in designer.
Click the Template tab along the bottom.
Paste in the provided CloudFormation template.
Click the checkmark icon in the top left to validate the template.
Click the cloud icon at the top left to create the stack.
Click Next.
Set the following values:
Stack name: s3role
KeyName: s3roleslearningactivity
MySubnet: Paste the subnet ID you copied earlier
myVPC: Paste the VPC ID you copied earlier
Click Next.
Click Next.
Check the acknowledgement at the bottom of the page, and click Create stack.
Refresh the Events table to show the stack creation progress. It will take a few minutes.
Once it's created, click the Outputs tab.
Copy the ssh string in the Value column and paste it into a text file, since we're going to need it in a minute.
List and Create S3 Buckets from the CLI
Open a terminal session, and change to your downloads directory:

cd Downloads
Change permissions on the key pair file:

chmod 400 s3roleslearningactivity.pem
Enter the ssh string you copied a minute ago to connect to the EC2 instance.

Enter yes at the prompt.

Once you are logged in, update the host:

sudo yum update
Enter y at the prompt.

Verify that you can list buckets:

aws s3 ls
In the AWS console, navigate to S3 to make sure the bucket listed matches the output in the terminal.

Back in the terminal, verify that you can create a bucket:

aws s3 mb s3://mybucket
Note: Remember, bucket names have to be globally unique, so add a few numbers to the end of the bucket name.

Back in the AWS console, refresh the S3 page and ensure your bucket was created.