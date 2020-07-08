## Creating and Configuring a Network Load Balancer in AWS
### Introduction
In this hands-on lab, we prepare the AWS environment for the Network Load Balancer (configuring subnets, network ACL, and EC2 instances). When the preparation is complete, we will create and configure a Network Load Balancer. After configuration of the load balancer, we will work from the CLI to run a small test on the load balancer and view the results in the CloudWatch Monitor.

### Solution
Log in to the live AWS environment using the credentials provided. Make sure you're in the N. Virginia (us-east-1) region throughout the lab.

## Prepare the Environment
### Create and Configure a Subnet
    Navigate to EC2, and click to view our one running instance.
    In a new browser tab, navigate to VPC.
    Click Subnets in the left-hand menu, and then click Create subnet.
    Name tag: Public B
    VPC: Select the listed VPC
    Availability Zone: us-east-1b
    IPv4 CIDR block: 10.0.2.0/24
    Click Create.
    On the subnets page, with the new subnet selected, click the Route Table tab below.
    Click the route table link.
    With it selected, click the Routes tab below.
    Click Edit, and then Add another route.
    Set the following values:
    Destination: 0.0.0.0/0
    Target: Select the listed internet gateway
    Click Save.
    Click the Subnet Associations tab.
    Click Edit.
    Select the Public B subnet, and click Save.

### Edit the Network ACL
    Note: Instead of the 100 ALL Traffic Allow Rule, change it to http Allow.

    Click Subnets in the left-hand menu.
    Click the Network ACL tab below, and then click the network ACL link.
    With it selected, click the Inbound Rules tab.
    Click Edit, and then click Add another rule.
    Set the following values:
    Rule #: 101
    Type: HTTPS (443)
    Protocol: TCP (6)
    Port Range: 443
    Source: 0.0.0.0/0
    Click Add another rule.
    Set the following values:
    Rule #: 102
    Type: SSH (22)
    Protocol: TCP (6)
    Port Range: 22
    Source: 0.0.0.0/0
    Click Add another rule.
    Set the following values:
    Rule #: 103
    Type: Custom TCP Rule
    Protocol: TCP (6)
    Port Range: 1024 - 65535
    Source: 0.0.0.0/0
    Click Save.

### Create EC2 Instances

#### First Instance
    In the EC2 browser tab, click the running instances.
    Click Launch Instance.
    On the AMI page, select the Amazon Linux 2 AMI.
    Leave t2.micro selected, and click Next: Configure Instance Details.
    On the Configure Instance Details page:
    Network: Leave default
    Subnet: us-east-1a
    Auto-assign Public IP: Enable
    Click Next: Add Storage, Next: Add Tags, and then Next: Configure Security Group.
    Click to Select an existing security group.
    Select the provided security group (not the default security group) from the table.
    Click Review and Launch, and then Launch.
    In the key pair dialog, select Create a new key pair.
    Give it a Key pair name of "nlb".
    Click Download Key Pair, and then Launch Instances.
    Click View Instances.

#### Second Instance
    Click Launch Instance.
    On the AMI page, select the Amazon Linux 2 AMI.
    Leave t2.micro selected, and click Next: Configure Instance Details.
    On the Configure Instance Details page:
    Network: Leave default
    Subnet: us-east-1b
    Auto-assign Public IP: Enable
    Click Next: Add Storage, Next: Add Tags, and then Next: Configure Security Group.
    Click to Select an existing security group.
    Select the provided security group (not the default security group) from the table.
    Click Review and Launch, and then Launch.
    In the key pair dialog, select Choose an existing key pair.
    Select nlb, and then Launch Instances.
    Click View Instances, and give them a few minutes to enter the running state.

### Create and Configure a Network Load Balancer
    Click Load Balancers in the left-hand menu.
    Click Create Load Balancer.
    In the Network Load Balancer card, click Create.
    In the Basic Configuration section, set the following values:
    Name: NLB4LA
    Scheme: internet-facing
    Leave the settings in the Listeners section as-is.
    In the Availability Zones section, select the listed VPC.
    Check both availability zones.
    Click Next: Configure Routing.
    In the Target Group section, set the following values:
    Target group: New target group
    Name: nlbTarget
    Protocol: TCP
    Port: 80
    Target type: instance
    In the Health checks section, set the following values:
    Protocol: TCP
    Leave the settings in the Advanced health check settings section as-is.
    Click Next: Register Targets.
    Select the two instances we created (not the AdminInstance), and click Add to registered.
    Click Next: Review.
    Click Create.
    Click Target Groups in the left-hand menu.
    In the Targets tab, after a minute or two, we should see the instances are unhealthy.

### Test and Monitor the Network Load Balancer
### Configure Instances as Web Servers
#### Configure First Instance as a Web Server
    Open a terminal session.
    Make sure you're in your downloads directory.
    On the instances page of the AWS console, select one of our instances and click Connect.
    Copy the chmod command in the dialog, and run it in the terminal.
    In the connection dialog, copy the ssh command, and run it in the terminal.
    Run a YUM update:

    sudo yum update -y
    Install Apache:

    sudo yum install -y httpd
    Ensure the web server starts if the instance is rebooted:

    sudo service httpd start
    Automate the web server starting:

    sudo chkconfig httpd on
    Log out of the instance:

    exit

#### Configure Second Instance as a Web Server
    On the instances page of the AWS console, select our other instance and click Connect.
    Copy the ssh command, and run it in the terminal.
    Run the same series of commands:

    sudo yum update -y

    sudo yum install -y httpd

    sudo service httpd start

    sudo chkconfig httpd on
    Log out of the instance:

    exit

### Check Health Checks and Test Network Load Balancer
    In the AWS console, click Target Groups in the left-hand menu.
    In the Targets tab, after a minute or two, we should see the instances are now healthy.
    Click Load Balancers in the left-hand menu.
    Copy its DNS name, and paste it into a new browser tab. It should result in the Apache test page.
    In the AWS console, click Instances in the left-hand menu.
    Click our running instances, select the AdminInstance, and copy its public IP address.
    In the terminal, log in to it:

    ssh linuxacademy@<PUBLIC IP OF ADMININSTANCE>
    The password is 123456.

    In the AWS console, click Load Balancers in the left-hand menu.

    In the Description tab, copy the DNS name and paste it into a text file. We'll need it for the next command.
    In the Monitoring tab, keep an eye on the CloudWatch metrics.
    In the terminal, we'll bombard our load balancer with requests while monitoring the CloudWatch metrics in the AWS console. Run the following commands:

    while true;
    Hit new line.

    do
    Hit new line.

    wget <LOAD BALANCER DNS NAME>
    Hit new line.

    done
    Hit Return — your terminal will most likely go crazy.

    Hit Ctrl+C to get out of the loop.
    In the AWS console, take a look at the Monitoring tab of our load balancer. We should see the spikes in the different charts.