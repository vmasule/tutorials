Using Route 53 to Configure DNS in an AWS Environment
Introduction
In this hands-on lab, we work through various options available for configuring DNS. The first section of the lab entails configuring DNS within a Virtual Private Cloud, which involves working with AWS Route 53. Route 53 is the perfect tool for configuring DNS within an AWS environment and specifically for a VPC. The second part of the lab presents hybrid scenarios where on-premises servers are still part of the compute environment, and we'll have to configure a hybrid environment with DNS. Finally, we'll dive into a scenario where we've got to configure a completely external DNS server, utilizing just AWS and Route 53. Ultimately, we will have covered configuring DNS for any scenario someone may encounter in the real world.

Solution
Log in to the AWS environment using the cloud_user credentials provided. Once inside the AWS account, make sure you are using us-east-1 (N. Virginia) as the selected region.

Create an EC2 Instance
From the AWS Management Console Dashboard, navigate to EC2.
Click Launch Instance.
Select an Amazon Linux 2 AMI (it should be the first in the list).
Select t2.micro.
Click Next: Configure Instance Details.
Auto-assign Public IP: Enable
Click Next: Add Storage.
Click Next: Add Tags.
Click Add Tag.
Key: Name
Value: Client
Click Next: Configure Security Groups.
Assign a security group: Select an existing security group
Click the checkbox next to the Security Group ID that has "SecurityGroup" in the name.
Click Review and Launch.
Click Launch.
Choose a Key Pair
Choose Create a new key pair.
Key pair name: dnslab
Click Download Key Pair.
Click Launch Instances.
Click View Instance.
Connect to the EC2 Instance
Once the instance is fully created, check the checkbox next to the instance name and click Connect at the top of the window.
Copy the connection string, found under the Example section.
Open a terminal window and navigate to your Downloads folder:

cd downloads
Set permissions on the key pair:

chmod 400 dnslab.pem
Paste the connection string into your terminal window.

Create a Route 53 Hosted Zone
In the AWS Console, navigate to the VPC service.
Click Your VPCs.
Select the checkbox next to the VPC name and click Actions > Edit DNS Resolution.
Ensure DNS Resolution is set to enable and click Save.
Select the checkbox next to the VPC name and click Actions > Edit DNS Hostname.
Ensure DNS Hostnames is set to enable and click Save.
Navigate to the Route 53 service.
Click Hosted Zones.
Click Create Hosted Zone.
Create Domain Name: awscloud.local.
For type, select Private Hosted Zone for Amazon VPC.
For VPC ID, in the dropdown select the VPC provided.
Click Create.
Create Record Sets
Duplicate your existing tab in your browser to open a new AWS Console tab.
In this new tab, navigate to the EC2 service.
Click Instances in the menu in the left.
Click the checkbox next to the Client instance and find the Private IPs section in the details at the bottom of the page. Copy the private IP address to your clipboard.
On the Route 53 tab, click Create Record Set.
Name: client
Value: Paste the private IP address copied earlier
Click Create.
Test in the Terminal
Note: It may take a few minutes for the new DNS record set to propagate fully.

In your terminal window:

nslookup client.awscloud.local
Configure On-Premises DNS
In the EC2 tab in your browser, select the checkbox next to the On Premise DNS instance.
In the details pane at the bottom of the page, find the Private IPs section and copy the IP.
In your terminal, view the contents of /etc/resolv.conf

cat /etc/resolv.conf
Edit the file:

sudo vi /etc/resolv.conf
Modify the line, replacing the existing nameserver IP with the private IP you just copied.

View the on-premises record:

nslookup ns1.onpremise.local
Ping ns1.onpremise.local and compare it to the output of a ping to ns1:

ping ns1.onpremise.local
ping ns1
Note: Hit Ctrl+C to exit the ping.

Our system is not able to resolve just ns1. Let's fix that by editing the search line in our /etc/resolv.conf file:

Change:

search ec2.internal
To:

search onpremise.local
You should now be able to ping ns1.

In the AWS Console, navigate to the VPC service.
Click DHCP Options Sets.
Click Create DHCP options set.
Name tag: on-premise
Domain name: onpremise.local
Domain name servers: Set this to the private IP address of your On Premise DNS EC2 instance
Click Create DHCP options set.
Click Your VPCs.
Check the box next to the VPC name, click Actions > Edit DHCP Options Set.
In the dropdown menu, select the DHCP options set we created, on-premise,
Click Save.

In the terminal, enter:

sudo reboot
Log back in to the instance using the connection string from earlier.

View the auto-generated contents of /etc/resolv.conf:

cat /etc/resolv.conf
Ping ns1:

ping ns1