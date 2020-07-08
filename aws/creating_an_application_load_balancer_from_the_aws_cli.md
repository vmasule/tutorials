## Creating an Application Load Balancer from the AWS CLI

### Introduction
    In this hands-on lab, you will learn to create an Application Load Balancer from the command line interface. It only takes five commands, offers some speed advantages, and can ultimately be scripted. From the CLI, you can create and configure the ALB, target groups, targets, listeners, and health checks. There is a specific sequence for creating the Application Load Balancer, meaning fewer configuration issues get introduced. This hands-on lab offers the opportunity to perform some realistic troubleshooting on the ALB.

### Solution
    Open a terminal session, and change to your downloads directory, like this:

    cd Downloads
    Log in to the live AWS environment using the credentials provided, and make sure you're in the N. Virginia (us-east-1) region. We'll mostly use the AWS console for monitoring throughout the lab while we run commands in the CLI.

### Creating an Application Load Balancer from the CLI
    In the AWS console, navigate to EC2.
    Click the Running instances link.

### Create First Instance
    On the instances page, click Launch Instance.
    On the AMI page, select the Amazon Linux 2 AMI.
    Leave t2.micro selected, and click Next: Configure Instance Details.
    On the Configure Instance Details page:
    Network: Leave default
    Subnet: us-east-1b
    Auto-assign Public IP: Enable
    Click Next: Add Storage, Next: Add Tags, and Next: Configure Security Group.
    Click to Select an existing security group.
    Select the one provided in the table (not the default security group).
    Click Review and Launch, and then Launch.
    In the key pair dialog, select Create a new key pair.
    Give it a Key pair name of "albLab".
    Click Download Key Pair, and then Launch Instances.
    Click View Instances.

### Create Second Instance
    On the instances page, click Launch Instance.
    On the AMI page, select the Amazon Linux 2 AMI.
    Leave t2.micro selected, and click Next: Configure Instance Details.
    On the Configure Instance Details page:
    Network: Leave default
    Subnet: us-east-1a
    Auto-assign Public IP: Enable
    Click Next: Add Storage, Next: Add Tags, and Next: Configure Security Group.
    Click to Select an existing security group.
    Select the one provided in the table (not the default security group).
    Click Review and Launch, and then Launch.
    In the key pair dialog, select Choose an existing key pair and make sure albLab is selected.
    Click Launch Instances.
    Click View Instances, and give the instances a few minutes to enter the running state.

### Log in to Admin Instance
    Select the AdminInstance listed.
    Copy the public IP address for it.
    In the terminal window, log in to the admin instance via SSH:

    ssh linuxacademy@<PUBLIC IP OF ADMININSTANCE>
    The password is 123456. You will be prompted to change the password and then log in again with the new password. Make sure it's a password you can easily remember, as we'll need it again toward the end of the lab.

    Verify that the admin instance has the AWS CLI:

    aws elbv2 help
    Hit q to quit out of it.

    Open aws configure:

    aws configure
    Set the following values (press Enter to leave the default as [None]):

    AWS Access Key ID [None]:
    AWS Secret Access Key [None]:
    Default region name [None]: us-east-1
    Default output format [None]:

### Create the Load Balancer
    In the AWS console, select one of the instances we created.
    Copy its subnet ID in the Description section, and paste it into a text file.
    Repeat this process for the other instance.
    Click Security Groups in the left-hand menu.
    Select the one we set for our instances (not the default one).
    Copy its group ID in the Description section, and paste it into a text file.
    In the terminal, enter the following command (replacing <SUBNET 1 ID>, <SUBNET 2 ID>, and <SECURITY GROUP ID> with the IDs you just copied):

    aws elbv2 create-load-balancer --name alblab-load-balancer  \
    --subnets <SUBNET 1 ID> <SUBNET 2 ID> --security-groups <SECURITY GROUP ID>

### Create a Target Group
    In the AWS console, right-click Load Balancers in the left-hand menu to open it in a new browser tab.
    Copy its VPC ID and paste it into a text file.
    Also, copy and paste its ARN into a text file, as we'll need it later.
    In the terminal, enter the following command (replacing <VPC ID> with the ID you just copied):

    aws elbv2 create-target-group --name my-targets --protocol HTTP --port 80  \
    --vpc-id <VPC ID>
    In what's returned, copy and paste the TargetGroupArn into a text file for the next command.

### Register the Targets
In the AWS console, navigate to our EC2 instances.
Select one of the instances we created.
Copy its instance ID in the Description section, and paste it into a text file.
Repeat this process for the other instance.
In the terminal, enter the following command (replacing <TARGET GROUP ARN> with the TargetGroupArn you just copied, as well as replacing <INSTANCE 1 ID> and <INSTANCE 2 ID> with the instance IDs):

aws elbv2 register-targets --target-group-arn <TARGET GROUP ARN>  \
--targets Id=<INSTANCE 1 ID> Id=<INSTANCE 2 ID>
We can ignore the warning/error message we receive.

### Create a Listener
    In the terminal, enter the following command (replacing <LOAD BALANCER ARN> with the one you copied earlier and <TARGET GROUP ARN> with the TargetGroupArn):

    aws elbv2 create-listener --load-balancer-arn <LOAD BALANCER ARN>  \
    --protocol HTTP --port 80  \
    --default-actions Type=forward,TargetGroupArn=<TARGET GROUP ARN>

### Perform a Health Check
    Enter the following command (replacing <TARGET GROUP ARN> with the TargetGroupArn)

    aws elbv2 describe-target-health --target-group-arn <TARGET GROUP ARN>

### Troubleshooting the Application Load Balancer
    In the AWS console, in EC2, click Target Groups in the left-hand menu.
    In the Targets tab, we'll see the status for the instances is unhealthy. To rectify this, we need to configure out instances as web servers.

### Configure Instances as Web Servers
    Configure First Instance as a Web Server
    Log out of the admin instance:

    exit
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
### Configure Second Instance as a Web Server
    On the instances page of the AWS console, select our other instance and click Connect.
    Copy the ssh command, and run it in the terminal.
    Run the same series of commands:

    sudo yum update -y

    sudo yum install -y httpd

    sudo service httpd start

    sudo chkconfig httpd on

### Check Instances and Load Balancer
    In the AWS console, select the first instance you configured.
    Copy its public IP address and paste it into a new browser tab.
    You should see the Apache test page. If not, check the ingress on the instance's security group, which should be HTTP on port 80.
    In the AWS console, navigate to our load balancer.
    Copy its DNS name and paste it into a new browser tab, which should result in the Apache test page.
    In the AWS console, navigate to our target groups.
    We'll see the instances' statuses are still unhealthy.
    The health check for the application balancer is looking for a return code of 200, but it isn't getting it. We need to set up an index.html page for each of our instances to return the 200 code to the application load balancer, causing our health checks to pass.

### Configuring EC2 instances and Checking Health Checks
    Set Up index.html Pages for Instances
    First Instance
    In the terminal, where we should still be logged in to one of the instances, enter the following:

    cd /var/www/html
    Create the file:

    sudo touch index.html
    Set up the file for read and write permissions:

    sudo chmod 777 index.html
    Open the file:

    vim index.html
    To avoid adding unnecessary spaces and hashes, enter:

    :set paste
    And then i to enter insert mode.

    Add the following to the file:
    ```
    <html>
    <head>
    <title>ELB Heartbeat</title>
    </head>
    <body>
    ?php echo '<p>OK</p>';  
    </body>
    </html>
    ```
    Save and exit the file by pressing Escape followed by :wq!.

    Log out of the instance:

    exit

### Second Instance
    On the instances page of the AWS console, select our other instance and click Connect.
    Copy the ssh command, and run it in the terminal.
    Run the same series of commands:

    cd /var/www/html
    sudo touch index.html
    sudo chmod 777 index.html
    vim index.html
    To avoid adding unnecessary spaces and hashes, enter:

    :set paste
    And then i to enter insert mode.

    Add the following to the file:
    ```
    <html>
    <head>
    <title>ELB Heartbeat</title>
    </head>
    <body>
    ?php echo '<p>OK</p>';  
    </body>
    </html>
    ```
    Save and exit the file.

    Log out of the instance:

    exit

### Check Health Checks
    In the AWS console, select the admin instance and copy its public IP address.
    In the terminal, log in to the admin instance:

    ssh linuxacademy@<PUBLIC IP OF ADMININSTANCE>
    The password is 123456 — unless you were prompted to change it earlier, in which case, enter the password you created for it earlier.

    In the AWS console, refresh the target groups page to check the health. Both instances should have a healthy status.

    In the terminal, run the target health check command:

    aws elbv2 describe-target-health --target-group-arn <TARGET GROUP ARN>
    In the AWS console, navigate to our load balancer.

    Copy and paste its DNS name into a new browser tab, which should result in our "OK" message.