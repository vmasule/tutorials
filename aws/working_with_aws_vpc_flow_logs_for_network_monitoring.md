Learning Objectives
check_circle
### Create S3 Bucket for VPC Flow Logs and VPC Flow Log to S3
Create S3 Bucket for VPC Flow Logs
Navigate to S3.
Note the account ID shown after the login name of cloud_user in the menu bar at the top right of the screen.
Copy the {account_id} without dashes to your favorite text editor.
Click Create bucket.
Enter a unique bucket name (e.g., vpc-flow-logs-{account_id}-{your-initials}). If the name you entered exists, try a different name.
Click Next.
Check the box for Default encryption.
Click Next.
Click Next.
Click Create bucket.
Select the checkbox next to the name of bucket you just created.
Click Copy Bucket ARN from the popup window. Paste the ARN in your favorite text editor. We will use the S3 ARN in an upcoming task.
Create VPC Flow Log to S3
In a new browser tab, navigate to VPC.
Click Your VPCs in the left-hand menu.
Select the LinuxAcademy VPC.
Select the Flow Logs tab for the VPC.
Click Create flow log.
In the Filter dropdown, select All.
Select Send to an S3 bucket for the Destination.
Paste the S3 bucket ARN you copied earlier.
Click Create.
Click Close.
Verify the flow log shows as Active.
Select the hyperlink in the Destination name field.
Select the Permissions tab.
Click Bucket Policy.
AWS automatically created a bucket policy when the VPC flow log was created. Notice the bucket path in the policy includes AWSLogs.
Please note, it can take 5-15 minutes before logs start to show up. You can proceed to the next step while waiting.
check_circle
### Create CloudWatch Log Group and VPC Flow Log to CloudWatch
Create CloudWatch Log Group
Navigate to CloudWatch.
Click Logs in the left-hand menu.
Click Actions > Create log group.
Enter "VPCFlowLogs" for the name.
Click Create log group.
Create VPC Flow Log to CloudWatch
Navigate to VPC.
Click Your VPCs in the left-hand menu.
Select the LinuxAcademy VPC.
Select the Flow Logs tab for the VPC.
Click Create flow log.
In the Filter dropdown, select All.
Select Send to CloudWatch Logs for the Destination.
Select VPCFlowLogs for the Destination log group.
Select the IAM role with DeliverVPCFlowLogsRole in the name.
Click Create.
Click Close.
Verify the flow log shows as Active.
Select the hyperlink in the Destination name field.
Make sure the N. Virginia region (us-east-1) is selected.
Please note, it can take 5-15 minutes before logs start to show up. You can proceed to the next step while waiting.
check_circle
### Generate Traffic
Log in to the EC2 instance via SSH using the credentials provided.
Exit the terminal.
Navigate to EC2 in a new browser tab.
Select the instance with the name Web Server.
Select Security Groups view inbound rules link.
Observe that SSH is enabled for all source addresses.
Click Actions > Networking > Change Security Groups.
Uncheck the HTTP and SSH Access security group.
Select the HTTP Access security group, and verify no other security groups are selected.
Click Assign Security Groups.
Select Security Groups view inbound rules link.
Observe that SSH is not enabled.
Attempt to log in to the EC2 instance via SSH using the credentials provided — we expect this to timeout since we just selected a security group with no SSH access.
Cancel the SSH command after 15 seconds.
Return to EC2 in the AWS console.
Select the instance with the name Web Server.
Click Actions > Networking > Change Security Groups.
Uncheck the HTTP Access security group.
Select the HTTP and SSH Access security group, and verify no other security groups are selected.
Click Assign Security Groups.
check_circle
### Create CloudWatch Filters and Alerts
Create CloudWatch Log Metric Filter
Navigate to CloudWatch.
Click Logs in the left-hand menu.
In the VPCFlowLogs row, click on the 0 filters link.
Click Add Metric Filter.
Enter the following Filter Pattern to track failed SSH attempts on port 22:
```
[version, account, eni, source, destination, srcport, destport="22", protocol="6", packets, bytes, windowstart, windowend, action="REJECT", flowlogstatus]
In the Select Log Data to Test dropdown, select Custom Log Data.
```
Enter the following in the text box:

```
2 086112738802 eni-0d5d75b41f9befe9e 61.177.172.128 172.31.83.158 39611 22 6 1 40 1563108188 1563108227 REJECT OK
2 086112738802 eni-0d5d75b41f9befe9e 182.68.238.8 172.31.83.158 42227 22 6 1 44 1563109030 1563109067 REJECT OK
2 086112738802 eni-0d5d75b41f9befe9e 42.171.23.181 172.31.83.158 52417 22 6 24 4065 1563191069 1563191121 ACCEPT OK
2 086112738802 eni-0d5d75b41f9befe9e 61.177.172.128 172.31.83.158 39611 80 6 1 40 1563108188 1563108227 REJECT OK
Click Test Pattern.
```

Click on the Show test results link. You should see 2 of the 4 records matching.
Click Assign Metric.
Enter destination-port-22-rejects as the Filter Name.
Enter SSH failures as the Metric Name.
Click Create Filter.
Create Alarm Based on the Metric Filter
Click on the Create Alarm link in the new destination-port-22-rejects metric filter box.
Select the Greater/Equal radio button.
Enter 1 in the Define the threshold value edit box.
Click Next.
Select the Create new topic button.
Enter PROD-ALERT-{your-initials} in the Create a new topic... edit box.
Enter your email address in the Email endpoints that will receive the notification edit box.
Click Create topic.
Click Next.
Enter SSH REJECT as the Alarm name.
Click Next.
Click Create alarm.
You will receive an email notification asking you to confirm your subscription. Click the Confirm Subscription link in the email.
check_circle
### Generate Traffic for Alerts
Log in to the EC2 instance via SSH using the credentials provided.
Exit the terminal.
Navigate to EC2 in a new browser tab.
Select the instance with the name Web Server.
Select Security Groups view inbound rules link.
Observe that SSH is enabled for all source addresses.
Click Actions > Networking > Change Security Groups.
Uncheck the HTTP and SSH Access security group.
Select the HTTP Access security group, and verify no other security groups are selected.
Click Assign Security Groups.
Select Security Groups view inbound rules link.
Observe that SSH is not enabled.
Attempt to log in to the EC2 instance via SSH using the credentials provided — we expect this to timeout since we just selected a security group with no SSH access.
Cancel the SSH command after 15 seconds.
Return to EC2 in the AWS console.
Select the instance with the name Web Server.
Click Actions > Networking > Change Security Groups.
Uncheck the HTTP Access security group.
Select the HTTP and SSH Access security group, and verify no other security groups are selected.
Click Assign Security Groups.
Observe you receive an email notification once there is a failed SSH login approximately 5-15 min. You can move to the next task while waiting.
check_circle
### Use CloudWatch Insights
Navigate to CloudWatch.
Click Insights in the left-hand menu.
Select VPCFlowLogs in the Select a log group search window.
Click Sample queries > VPC flow log queries > Top 20 source IP addresses with highest number of rejected requests.
Observe the query has changed.
Click Run query.
check_circle
### Configure VPC Flow Logs Table and Partition in Athena
Record Reference Information to Be Used in Athena Queries
Navigate to S3 in a new browser tab.
Open {your_log_bucket} created earlier in this hands-on lab.
Copy {your_log_bucket} name to a text editor for later use.
Open the AWSLogs folder.
Open the {account_id} folder.
Copy the {account_id} to a text editor for later use if you didn’t save it earlier.
Open the vpcflowlogs folder.
Open the us-east-1 folder.
Open the {Year} folder.
Open the {Month} folder.
Write down the {Year}/{Month}/{Day} using the latest {Day} shown.
Create the Athena Table
Navigate to Athena.
If you come to a page with a Get Started button, click on it. Otherwise skip this step.
If a Tutorial popup window shows up, then click on the X in the upper right of the screen to close it. You can return to the tutorial later by selecting it in the upper right-hand menu.
Paste the following DDL code in the new query window:
```
CREATE EXTERNAL TABLE IF NOT EXISTS default.vpc_flow_logs (
  version int,
  account string,
  interfaceid string,
  sourceaddress string,
  destinationaddress string,
  sourceport int,
  destinationport int,
  protocol int,
  numpackets int,
  numbytes bigint,
  starttime int,
  endtime int,
  action string,
  logstatus string
)  
PARTITIONED BY (dt string)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ' '
LOCATION 's3://{your_log_bucket}/AWSLogs/{account_id}/vpcflowlogs/us-east-1/'
TBLPROPERTIES ("skip.header.line.count"="1");
```
Update {your_log_bucket} and {account_id} in the query window with the values from this hands-on lab.

Click Run query.
You should see a Query successful. message once this has finished executing.
Create Partitions to Be Able to Read the Data
Paste the following code in a new query window:
```
ALTER TABLE default.vpc_flow_logs
ADD PARTITION (dt='{Year}-{Month}-{Day}')
location 's3://{your_log_bucket}/AWSLogs/{account_id}/vpcflowlogs/us-east-1/{Year}/{Month}/{Day}';
```
Update the following elements in the query window with the values from this hands-on lab:

{your_log_bucket}
{account_id}
{Year},{Month}
{Day}
Click Run query.
You should receive a Query successful. message.
check_circle
### Analyze VPC Flow Logs Data in Athena
Run the following query in a new query window:
````
SELECT day_of_week(from_iso8601_timestamp(dt)) AS
  day,
  dt,
  interfaceid,
  sourceaddress,
  destinationport,
  action,
  protocol
FROM vpc_flow_logs
WHERE action = 'REJECT' AND protocol = 6
order by sourceaddress
LIMIT 100;
````