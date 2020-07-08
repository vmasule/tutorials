Create a VPC Endpoint and S3 bucket in AWS
Introduction
In this Hands-on lab we will create a VPC endpoint and an S3 bucket, to illustrate the benefits available to our cloud implementations. VPC Endpoints can be used, instead of NAT Gateways, to provide access to AWS resources. Many customers have legitimate privacy and security concerns about sending and receiving data across the public internet. VPC endpoints for S3 can alleviate these challenges by using the private IP address of an instance to access S3 with no exposure to the public internet.

Logging In
Please log into the AWS environment by using the cloud_user credentials provided on the hands-on lab overview page.

Once inside the AWS account, make sure you are using us-east-1 (N. Virginia) as the selected region.

Getting Our Bearings
Let's see what the lab environment looks like. Once we're logged into the AWS Management Console, head up to the Services menu, and select EC2 from the Compute section. We'll find two EC2 instances sitting there. If we click on each one and look at each one's details, we'll see that one is has a public gateway (and has a public IP address) while the other one is behind a private gateway (with no public IP). Click on the Name column in each one, then name them private and public so we can keep them straight.

Create an S3 Bucket
Back in the Services menu, look in the Storage section and choose S3. Then follow these ssteps to create the bucket:

Click + Create bucket.
On the Name and region screen, give your bucket a unique name. (Note: It must be all lowercase letters and be unique across all AWS accounts.) Leave the Region set to US East (N. Virginia), then click Next.
All we're going to do on the Configure options screen is set a tag. Set it to whatever you like (the name of the bucket might be best) and click Next.
On the Set permissions screen, the default is Block all public access. We'll leave this page alone, and click Next.
This review page is where we can make sure we've got everything set the way we want. If it's good, click Create bucket.
Create a VPC Endpoint
From the Management Console, go into VPC in the Networking & Content Delivery section. Then:

Click Endpoints.
Click Create Endpoint/
Leave the Service Category set to AWS services, then scroll down until we find the com.amazonaws.us-east-1.s3 option.
Select our VPC from the VPC* box below.
Now we need to select a route table.
Open up a new browser tab, and navigate (in the VPC dashboard) to Route Tables.
Look at each one, and name them appropriately (private or public), based on whether or not there is an igw-xxxxx type of Target in the Route tab. If there is one, then that's our public route table.
Make note of the Route Table name for the private route, then check its box back in the VPC tab we were in a few minutes ago.
Next, we've got to scroll down and make sure that our Policy* is set to Full Access.
Click Create endpoint.
Check and Verify That the VPC Endpoint Has Access to S3
Once it's created, head to Route Tables (over in the left VPC menu), and select our private route table. Down in the Routes tab, we'll see our endpoint details, like what IP addresses S3 will use.

Now let's get into the Subnet Associations tab, click Edit subnet associations, and select our private one. Click Save.

Now we're going to use SSH, and log into the instances and test all this. We'll have to flip back and forth between the terminal the the hands-on lab overview page to get credentials for the appropriate servers. Let's get into the public one first:

ssh cloud_user@publicIPaddressofpublicinstance
Once we're in, we can use SSH again to hop from there into the private instance:

ssh cloud_user@privateIPaddressofprivateinstance
Now let's try to see the S3 buckets in our environment. Run this:

aws s3 ls