
## Performing a Backup and Restore Using AMI and EBS

### Create an EBS Snapshot
Create a snapshot from a web application server's EBS volume.

1. Navigate to EC2 > Instances.
2. Check the box beside one of the "webserver-instance" instances.
3. Click the link to the Root device, and then the EBS volume link.
4. On the Volumes page, click Actions, and then Create Snapshot.
5. Enter a description.
6. Click Create Snapshot.

### Create a New EBS Volume from a Snapshot

Create a new EBS volume from the snapshot you just created in the previous step. This is how you restore an EBS volume from a snapshot.

1. Navigate to the Snapshots page.
2. Check the box beside the snapshot you just created.
3. Click Actions, and then Create Volume.
4. Change the volume size to 10 GiB.
5. Click Create Volume.
6. Click Close.

### Create Two EC2 AMIs

Amazon Machine Images can be created via several different methods. Create two AMIs following the methods detailed in the video.

* Method 1
1. Navigate to the Instances page.
2. Check the box beside one of the webserver-instance instances.
3. Click Actions, then Image, and then Create Image.
4. Add a name and description.
5. Click Create Image, and then Close.
6. Choose AMIs from the left menu to see the image you created.
7. Once the image is available, navigate to the instances page.
8. Click Launch Instance.
9. Choose My AMIs in the left column.
10. Click Select, and then you can configure all the other options for a new instance.
11. For now, click Cancel.

* Method 2
1. Navigate to Snapshots.
2. Check the box beside the snapshot you made earlier.
3. Click Actions, and select Create Image.
4. Enter a name and description.
5. Change Virtualization type to Hardware-assisted virtualization.
6. Click Create.
7. Click Close.
8. Choose AMIs from the left menu to see the image you created.
