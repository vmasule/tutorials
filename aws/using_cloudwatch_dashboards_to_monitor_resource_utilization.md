## Learning Objectives

### Create a custom CloudWatch Dashoboard
In the CloudWatch console, select Dashboards.
Create a Dashboard, and add the name BHCPUUTILIZATION.
Click Create Dashboard.
Select the Line Widget.
Select the EC2 metrec, and choose Per-Instance Metrics.
Now, search for CPUUTILIZATION and select the BH EC2 instance.
Customize the time interval to 15 minutes.
Create the widget and select Save Dashboard.

### Create the second custom dashboard
In the CloudWatch console, select Dashboards.
Create a Dashboard, and add the name APPCPUUTILIZATION.
Click Create Dashboard.
Select the Stacked widget.
Add the metric for EC2, and select Per-Instance Metrics.
Now search for CPUUTILIZATION and select the two wordpress EC2 instances and the RDS instance.
Customize the time interval to 15 minutes.
Create the widget and select Save Dashboard.

### Add an additional widget to the custom dashboard
Click Add Widget.
Select the Number widget.
Select the metric for Application ELB, then click on Per AppELB Metrics, and select RequestCount.
Create the widget and save the dashboard.
Select Add Widget
Choose the Line widget.
For metrics, select EC2 again, and then Per-Instance Metrics.
Search for NetworkIn and again select our two wordpress EC2 instances and our database instance
Save the dashboard.

## Using CloudWatch Dashboards to Monitor Resource Utilization

How to Use Cloud Watch Dashboards for Monitoring Resource Utilization
Now that we're logged in, we can start working on our dashboards for CloudWatch.

Create a Custom CloudWatch Dashboard
To create a CloudWatch dashboard, go to the Services dashboard. Under the Management & Governance section, select CloudWatch. Here, select Dashboards, then Create dashboard.

We want to name this dashboard BHCPUutilization. Once you've added in the name, select Create Dashboard. You will then be prompted to add a widget. We want to add a Line widget. Once chosen, select Configure.

Now, we need to add a metric for the dashboard. To do that, select EC2, then Per-InstanceMetric. Here, search for CPUUtilization, and then select the Bastion Host. At the top of the page, select the custom dropdown, and set the time interval to 15 in the minutes section. After you've set the time, click Create Widget and then select Save Dashboard.

Create the Second Custom Dashboard
Now, we need to make our second dashboard. Once again, in the CloudWatch console, select Dashboards. SelectCreate dashboard, and name it APPCPUutilization. Now, click Create Dashboard. This time, select the Stacked widget. Again, add the metric for EC2, and select Per-InstanceMetric. Search for CPUUtilization. Select the following instances:

database
instance-wordpress
instance-wordpress
With those selected, set the custom time interval to 15 minutes once again. Now select Create Widget and then Save Dashboard.

Add Additional Widgets to the Custom Dashboard
Now that we've created our two dashboards, and provided them both with widgets, we need to create a widget for an already created dashboard. To do this, on the desired dashboard (for now, let's use the one we just made), select Add widget from the top of the page. This time, let's select the Number Widget. For the metric, select Application ELB, then Per AppELB Metrics, and select the metrics named RequestCount. Finally, select Create Widget and then Save Dashboard.

Now we need one more widget. Again, select Add Widget, and then this time select Line. For the metrics, select EC2, Per-Instance Metrics, search for NetworkIn, and then select the two wordpress EC2 instances and our database instance. Create the widget and then save the dashboard.

Generate Traffic
With our dashboards created, we now want to generate some traffic for these dashboards. To do so, go to the EC2 Dashboard and then, under Load Balancing, select Load Balancers.

Copy the DNS Name and then post it into a new tab of our preferred web browser. We are taken to our WordPress site. Select your desired language and then Continue. Fill in the information as:

Site Title: Labdemo
Usrename: LAuser
Password: (Whatever we want)
Your Email: The email you wish to use for this lab
Now, select Install Wordpress. Using the user name and password, log into WordPress. Wonderful, we're able to use WordPress.

Go back to the CloudWatch dashboard. Under Management & Governance select CloudWatch. On the next page, select Dashboards. Select the first dashboard. Here, we should see information has started to populate our widgets, which tells us that we have set up everything correctly.