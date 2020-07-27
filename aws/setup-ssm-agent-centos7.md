Installing and Configuring SSM Agent on Linux
Introduction
The Systems Manager Agent (SSM Agent) is at the heart of all the automation, management, and other tasks possible via Systems Manager. It must be installed on any machine managed by SSM. In this hands-on lab, we will manually install and configure SSM Agent on a Linux OS on an EC2 instance. We will then assign an appropriate SSM IAM role to our EC2 instance to be configured with Systems Manager. This lab assumes some knowledge of Linux — specifically, RHEL/CentOS package management and some general Linux skills.

Log in to the EC2 Instance via SSH
Open a terminal session, and log in to the EC2 instance provided on the lab page via SSH using the credentials listed:

ssh cloud_user<PUBLIC_IP_OF_EC2_INSTANCE>
Install the SSM Agent Using YUM
Download and install the SSM Agent for this Linux machine (the EC2 instance in our case is a CentOS 7 system):

```
wget https://s3.amazonaws.com/ec2-downloads-windows/SSMAgent/latest/linux_amd64/amazon-ssm-agent.rpm
sudo yum -y localinstall amazon-ssm-agent.rpm
```

Enter the cloud_user password.

Log in to the AWS Console and Create IAM SSM Role for EC2
In the browser, log in to the AWS console using the credentials provided on the lab page.
Navigate to IAM > Roles.
Click Create role.
With AWS service selected as the type of trusted entity, select EC2 as the service that will use this role.
Click Next: Permissions.
In the search bar, type "AmazonEC2RoleforSSM".
Select this role, and click Next: Tags.
Leave tags as default, and click Next: Review.
Give your role the name "MyEC2SSMRole".
Click Create role. Give it a minute to finish creating.
Attach IAM Role to EC2 Instance
Navigate to EC2 > Instances.
Select the listed SSMInstallInstance instance.
Select Actions > Instance Settings > Attach/Replace IAM Role.
Select your IAM role in the dropdown.
Click Apply and then Close.
Log Back in to Command Line of EC2 Instance

In the terminal, enable SSM Agent:

```
sudo systemctl enable amazon-ssm-agent
```

Start SSM Agent:

```
sudo systemctl start amazon-ssm-agent
```
To confirm the SSM Agent has started and is running successfully, check its status:

```
sudo systemctl status amazon-ssm-agent
```
The output should show an `active (running)` status.

Check Logs for SSM Agent and Enable Debug Logging for It
Look at the SSM Agent log file:

```
sudo tail -f /var/log/amazon/ssm/amazon-ssm-agent.log
```

We should see the SSM Agent has ongoing communication with Systems Manager.

Press Ctrl+C to quit the process.

Copy the example template to its original file name so it can be detected by SSM Agent:

```
sudo cp /etc/amazon/ssm/seelog.xml.template /etc/amazon/ssm/seelog.xml
```
Open the file you just copied:
```
sudo vim /etc/amazon/ssm/seelog.xml
```
Look for this line:
````
<seelog type="adaptive" mininterval="2000000" maxinterval="100000000" critmsgcount="500" minlevel="info">
````
Change `minlevel="info"` to `minlevel="debug"`.

Save and quit the file by pressing Escape followed by:
```
:wq!
```
Restart SSM Agent:

```
sudo systemctl restart amazon-ssm-agent
```

Tail the SSM Agent's log file to observe the newly enabled verbosity (debug):

````
sudo tail -f /var/log/amazon/ssm/amazon-ssm-agent.log
````