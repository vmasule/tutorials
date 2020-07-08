## Utilizing Proxy Protocol and Nginx to Solve Problems through the AWS ELB

### The Scenario
The problem with using ELB in AWS is that when we look at our web server's access log, it always looks like the ELB, instead of a real visitor, is the one accessing pages on our site. We're going to solve that problem by using Proxy Protocol.

### Get Logged In
    Let's get logged into the AWS management console using the credentials provided. Once inside the AWS account, make sure we are using us-east-1 (N. Virginia) as the selected region.

    We also want a terminal open, so find the public IP of the EC2 instance we've been provided with (which you can find in the EC2 dashboard of the AWS Management Console), and log into it with SSH:

    ssh linuxacademy@<PUBLIC IP ADDRESS>
    The password is 123456.

    We're going to need root privileges, so just su - once we get in as the default user.

### Assess the Environment
    Nginx is supposed to be installed and running already, but let's check:

    [root@host]# systemctl status nginx
    Now, plug our EC2 instance's public IP address into a web browser. We should see an Nginx welcome page. We should also see that there have been some visitors to the web page, by looking at the log:

    [root@host]# tail -f /var/log/nginx/access.log
    What we'll see is that our own public IP address is what's showing up in the log file. What we want is to have our ELB's IP address there, which would mean that any visitors coming in would be going through the ELB.

    We can go through our ELB manually by grabbing its DNS name from the AWS Management Console and pasting it into a web browser. The log (the one we're still watching with tail -f) should now reflect our own browsing traffic coming through the ELB.

    We can kill our tail -f command by pressing Ctrl+C.

    Now we need to install the awscli application, then run it:

    [root@host]# pip install awscli==1.6.6
    [root@host]# aws configure
    When we're prompted, we can press Enter to set the defaults on all but the Default region name. On that prompt, enter us-east-1.

### Create a Proxy Protocol Policy for an ELB
    Before we go making any policies, let's see what's available to us:

    [root@host]# aws elb describe-load-balancer-policy-types
    In the output, we'll see a section for ProxyProtocol, and that's the one we're concerned with.

    In the EC2 Management Console, find Load Balancers in the left-hand menu and copy our load balancer's name. Let's paste it into a text document somewhere, actually. We'll need it a few times.

    From the AWS CLI, enter:

    [root@host]# aws elb create-load-balancer-policy --load-balancer-name "<LOAD_BALANCER_NAME>" --policy-name linuxacademy-protocol-policy --policy-type-name ProxyProtocolPolicyType --policy-attributes AttributeName=ProxyProtocol,AttributeValue=true
    Before we assign this policy to our ELB, we have to check our load balancer, to make sure there aren't already policies assigned to it:

    [root@host]# aws elb describe-load-balancer-policies --load-balancer-name “<LOAD_BALANCER_NAME>” 
    Now, we need to attach it to a port:

    [root@host]# aws elb set-load-balancer-policies-for-backend-server --load-balancer-name “<LOAD_BALANCER_NAME>” --instance-port 80 --policy-names linuxacademy-protocol-policy

### Configure Nginx for Proxy Protocol
    Now, change directory to Nginx:

    cd /etc/nginx/
    We need to edit the Nginx configuration file (this example uses Vim):

    [root@host]# vim nginx.conf
    Locate the section that denotes where the server is listening. It should look like:

    server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name _;
    root /usr/share/nginx/html;
    Let's change the listen value from default_server to proxy_protocol:

    server {
    listen 80 proxy_protocol;
    listen [::]:80 proxy_protocol;
    server_name _;
    root /usr/share/nginx/html;
    We'll also need to set the real_ip_header to proxy_protocol:

    server {
    listen 80 proxy_protocol;
    listen [::]:80 proxy_protocol;
    set_real_ip_from 10.0.0.0/16;
    real_ip_header proxy_protocol;
    server_name _;
    root /usr/share/nginx/html;
    To get the VPC's CIDR, get into the VPC Dashboard, in Your VPCs. It will be listed in there.

    Next, we need to replace the $remote_addr variable with the $proxy_protocol_addr variable:

    http {
    log_format main '$proxy_protocol_addr - $remote_user [$time_local] “$request” '
                    '$status $body_bytes_sent “$http_referer” '
                    '”$http_user_agent” “$http_x_forwarded_for”';
    Save the file and exit Vim (by pressing Esc, then :wq), then restart Nginx and check its status:

    [root@host]# systemctl restart nginx
    [root@host]# systemctl status nginx
    Now, let's do the same sort of testing we were in the beginning of the lab. Watch the log:

    [root@host]# tail -f /var/log/nginx/access.log
    Now, in the browser we had open, refresh the Nginx welcome page. This should be the URL of the load balancer, not the EC2 instance itself. If we watch the logs, we should see our own public IP address (the computer we're sitting at in our house or office), not the load balancer.