## Using RAID Configurations on an AWS EBS Volume
Introduction
We're going to learn a bit about using RAID configurations with our storage devices in AWS. We'll focus on RAID 0, but also talk a bit about RAID 1, and define the differences between the two. Here, we'll be working with RAID 0 for EBS volumes, but the concepts will apply for other types of storage too. We will create two EBS volumes with the same size and performance characteristics (it's best to RAID together similar, if not exact, volumes).

We'll be using a combination of the AWS Management Console and the CLI to both configure our RAID volume and verify configuration.

Let's log into the AWS environment by using the cloud_user credentials provided. Once inside the AWS account, make sure we're are using us-east-1 (N. Virginia) as the selected region.

### Create Two EBS Volumes
In the AWS Management Console Dashboard, navigate to EC2, then click on Volumes in the left task bar under Elastic Block Store. Click Create Volume, change the volume Size to 10 GiB. The rest is fine, so we can click Create Volume. Repeat this process to create a second volume.

Attach Two EBS Volumes to the EC2 Instance
To attach a volume to something, we can either right click it and select Attach, or find Attach in the Actions menu. In the Instance dropdown we see in the popup window, select ours.

Repeat this process for the second volume.

Once both volumes have a state of in-use, we can proceed.

### Configure RAID
ssh linuxacademy@[Public IP of Instance] (The initial password is 123456). You will be prompted to change the password for security.

Let's log into our EC2 instance with SSH, using the public IP we can find on the EC2 dashboard. Once we're in, run a quick su - to become root right off.

Now, we can start working on the RAID configuration. First, let's check and see what's in /dev:

```
[root@host]# cd /dev
[root@host]# ls
```

Our two devices, xvdf and xvdg, show up in the ls output.

Now let's actually create the RAID array, using those two volumes. We'll need to install mdadm first:

```
[root@host]# yum -y install mdadm
```

```
[root@host]# mdadm --create --verbose /dev/md0 --level=0 --name=linuxacademy-raid --raid-devices=2 /dev/xvdf /dev/xvdg
```

Now we can write a filesystem to the RAID device, across all 20 GiB:

```
[root@host]# mkfs.xfs -L linuxacademy /dev/md0
```

To view the two RAID devices in md0, we can run:

```
[root@host]# lsblk
```
Now we can mount the RAID array, just like we can with any other disk:

```
[root@host]# mount /dev/md0 /mnt
```
To see if it worked, we can view the file system with this:
```
[root@host]# df -h
```