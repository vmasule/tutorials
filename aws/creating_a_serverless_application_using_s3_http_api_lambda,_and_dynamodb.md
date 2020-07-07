## Creating a Serverless Application Using S3, HTTP API, Lambda, and DynamoDB
### Introduction
Serverless allows you to build and run applications and services without thinking about servers. You can build them for nearly any type of application or backend service, and everything required to run and scale your application with high availability is handled for you. AWS provides a set of fully managed services that you can use to build and run serverless applications.

In this hands-on lab, you will build a serverless web application that utilizes a number of AWS services, including S3, API Gateway, Lambda, and DynamoDB.

### Lab Prerequisites
Understand how to log in to and use the AWS Management Console.
Understand how to use the AWS Command Line Interface (CLI).
Understand how to use ssh from the command line.
The Github repository used in this lab is here: https://github.com/linuxacademy/content-aws-sam

### Logging In

Use the credentials provided on the hands-on lab page, and log in as cloud_user. Make sure you are operating the lab within the N. Virginia (us-east-1) region.

### Create DynamoDB Table

Navigate to DynamoDB in the AWS Management Console, and click on the Create table button.
Give it the name friends:
Set the partition key to id of type Number.
Check Add sort key.
Set the sort key value to name of type String.
Leave Use default settings checked.
Click Create.
Wait until the Table status reads Active.
Log into the EC2 instance with SSH, using the credentials provided. Remember, these credentials are not the same as the ones used for logging into the AWS console.
Clone the repository:
 git clone https://github.com/linuxacademy/content-aws-sam
Change to the directory for this lab:
 cd content-aws-sam/labs/Creating-Serverless-Application-S3-HTTP-API-Lambda-DynamoDB
Seed the DynamoDB table with data:
 ./dynamodb.py friends

### Create Lambda Function

Navigate to Lambda in the AWS Management Console.
Click Create function.
Leave Author from scratch selected.
Set Function name to GetFriends.
Set Runtime to Python 3.8.
Under Permissions, expand Choose or create an execution role.
Select Create a new role from AWS policy templates.
Set Role name to GetFriendsRole.
Click Create function.
In the Function code editor, replace the boilerplate code with the contents of https://github.com/linuxacademy/content-aws-sam/blob/master/labs/Creating-Serverless-Application-S3-HTTP-API-Lambda-DynamoDB/lambda_function.py.
Under Environment variables, click Manage environment variables.
Click Add environment variable.
Set Key to TABLE_NAME.
Set Value to friends.
Click Save.
Click Save (Lambda function).

### Create HTTP API

Navigate to API Gateway in the AWS Management Console, and click Get started, then click OK on the Create your first API window that pops up.
Select APIs up at the top of the next screen, then click Build under the HTTP API API type.
Click Add integration.
Under Integration type select Lambda.
Under Integration target select us-east-1.
Choose the Lambda function named GetFriends.
Under API name, enter GetFriends.
Click Review and Create.
Click Create.
In the left navigation menu, under Develop, click CORS.
Click Edit.
Under Access-Control-Allow-Origin enter *.
Click Add.
Click Save.
Now click on our API: GetFriends... link in the left-hand menu.
Under Stages for GetFriends, click on the Invoke URL for our $default API:
This will give us a Not Found message. We need to add /GetFriends to the end of the URL in our browser, after the ...amazon.com.

### Create S3 Bucket

Navigate to S3 in the AWS Management Console.
Select Create bucket.
Under Bucket name enter something unique. There can be no other S3 bucket in all of AWS with the same name.
Under Region select US East (N. Virginia) us-east-1.
Uncheck Block all public access.
Check the acknowledgement box.
Click Create bucket. There will be an error here if a bucket with the name we chose already exists, so it might take a couple of tries of renaming it before this works.
In the next screen, click the bucket name to get into it.
In the Properties tab, click Static website hosting.
Select Use this bucket to host a website.
Under index document enter index.html.
Click Save.
In the Permissions tab, under Bucket Policy, paste the contents of https://github.com/linuxacademy/content-aws-sam/blob/master/labs/Creating-Serverless-Application-S3-HTTP-API-Lambda-DynamoDB/bucket_policy.json.
Replace my-bucket-name with the actual name of the bucket. Make sure that /* is still there. The line should look something like this:
"arn:aws:s3:::friends12345678/*"
Click Save.

### Configure and Deploy Web Application

On the EC2 instance, change to the app directory:
 cd app
Edit app.js:
 vim app.js
Replace the XXXXXXXXXX part of the invokeUrl with the HTTP API Invoke URL, which we'll need to grab from the AWS console (AWS Console > API Gateway > GetFriends). We don't actually need the whole Invoke URL, just the API ID.
Save the file.
Copy the application to S3:
 aws s3 sync . s3://<bucket name>
Browse to the S3 website URL http://.s3-website-us-east-1.amazonaws.com.