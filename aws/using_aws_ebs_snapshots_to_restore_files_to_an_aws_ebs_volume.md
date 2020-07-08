## Using AWS EBS Snapshots to Restore Files to an AWS EBS Volume
### Introduction
In this hands-on lab, we will learn how to restore a file from an EBS snapshot to its original location in an EBS volume. This is a useful skill for restoring files after an accidental deletion.

To accomplish this, we will:

    Create an EBS snapshot
    Create a new volume from the snapshot
    Attach the new volume to the existing EC2 instance
    Restore the file to its original location on the EBS volume
    Log in to the AWS Management Console using the credentials provided on the lab instructions page.

Make sure you are using the us-east-1 region.

### Create an EBS Snapshot
    In the AWS Management Console, navigate to the EC2 service.
    Click Running Instances.
    In the Description tab at the bottom of the page, copy the IPv4 Public IP address to your clipboard.
    Open your terminal application in a new window.
    In your terminal window, run the following command:
    ssh linuxacademy@<PUBLIC_IP_ADDRESS>
    Type yes at the prompt.
    Type 123456 at the password prompt. You will be prompted to change your password, then logged out. You'll have to log in again with the new password.
    Display the file systems and their sizes:
    df -h
    Change to the /mnt directory:
    cd /mnt
    List the contents of the directory:
    ls
    Go back to your AWS Management Console window, and click Volumes in the left sidebar. Check the box next to the volume whose Attachment Information column shows /dev/sdf (You can see it by either making that column wider, or selecting the volume and looking in the Description tab in the lower part of the screen).
    Click Actions > Create Snapshot.
    On the Create Snapshot page, type appname-backup-date for the Description.
    Click Create Snapshot, then Close.
    Click Snapshots in the left sidebar.
    When it's done being created, go back to your terminal window, and delete the restore-this-file file:
    sudo rm restore-this-file
    Enter our password (the one we reset when we first logged into the server) at the prompt.
    List the contents of the directory:
    ls
    restore-this-file should no longer appear in the directory.
### Create an EBS Volume from the Snapshot
    On the Snapshots page of the AWS Management Console, click Actions > Create Volume.
    On the Create Volume page, configure the following settings:
    Volume Type: General Purpose SSD (GP2)
    Size (GiB): 10
    Availability Zone: us-east-1a
    Click Create Volume, then Close.
    Click Volumes in the left-hand menu, and verify that its State is available.
    Select the volume we just created (with a status of available) from the list.
    Click Actions > Attach Volume.
    In the Attach Volume menu, select the WebServer instance from the Instance dropdown.
    Click Attach.
    Go back to your terminal window.
    Elevate to the root user (using the new password we set earlier):
    su
    Just like when first logging into the server, when we had to change the linuxacademy user's password, we've got to change root's here too. Then we'll be logged out (so that we're linuxacademy again) and have to run su a second time, using the new root password.
    Change to the /dev directory:
    cd /dev
    List the contents of the directory:
    ls
    Make a new directory called /restore:
    mkdir /restore
    Mount the new volume we created to the /restore directory:
    mount /dev/xvdg1 /restore
    Change to the /restore directory:
    cd /restore
    List the contents of the directory:
    ls
    Copy restore-this-file to its original location:
    cp restore-this-file /mnt/
    Change to the /mnt directory:
    cd /mnt
    List the contents of the directory:
    ls
    restore-this-file should be listed