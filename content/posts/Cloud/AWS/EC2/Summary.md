# Summary

Tips 

## Pricing model

1. on Demand
2. Reserved
3. Spot
    - If the Spot instance is terminated by EC2, you will not be charged for a partial hour of usage, However if you terminate the instance yourself, you will be charged for any hour in which the instance ran
4. Dedicated Hosts

## Instance Type


## EBS

 - Terminatin Protection is turned off by default, you must turn it omn
 - On an EBS-backed instance, the default action is for the root EBS volume to be deleted whe the instance is termicated.
 - EBS Root Volumes of your DEFAULT AMI's can be encrypted. You can also use a third party tool(such as bit locker etc) to encrypt the root volume, or this can be done when creating AMI's (remember the lab) in the AWS console or using the API.
 - Additional volumes can be encrypted.

## Security Groups
 - All inbound traffic is blocked by defailt.
 - All Outbound traffic is allowed.
 - Changes to Security Groups take effect immediately.
 - You can have any number of EC2 instances within a security group.
 - You can have multiple security groups attached to EC2 instances.
 - Security Groups are STATEFUL.
 - If you create an inbound rule allowing traffic in, that traffic is automatically allowed back out again.
 - You cannot block specific IP address using Security Groups, instead use Network Access Control Lists.
 - You can specify allow rules, but not deny rules.

## Compare EBS Types
 1. SSD
    - General Purpose SSD
    - Provisioned IOPS SSD
 2. HDD
    - Throughput Optimized HDD
    - Cold HDD
    - EBS Magnetic

## EBS snapshots

 - Volumes exist on EBS, Think of EBS as a virtual hard disk.
 - Snapshots exist on S3. Think of snapshots as a photograph of the disk.
 - Snapshots are point in time copies of Volumes.
 - Snapshots are incremental : this mean that only the blocks that have changed since your last snapshot are moved to S3
 - If this is your first snapshots, it may take some time to create.
 - To create a snapshots for amazon EBS volumes that serve as root devices, you should stop the instance before taking the snapshot.
 - However you can take a snap while the instance is running.
 - You can create AMI's from both volumes and snapshots
 - You can change EBS volume sizes on the fly, including changing the size and storage type.
 - Volumes will ALWAYS be in the same availability zone as EC2 instance

## Migrating EBS

 - To move an ev2 volume from one AZ to another, take a snapshot of it, create an AMI from the snapshot and then use AMI to launch the EC2 instance in a new AZ.
 - To move an EC2 volume from one region to another, take a snapshot of it, create an AMI from the snapshot and then copy the AMI from one region to the other. Then use the copied AMI to launch the new EC2 instance in the new region.

## EBS Encryption
 - Snapshots of encrypted volumes are encrypted automatically.
 - Volumes restored from encrypted snapshots are encrypted automatically.
 - You can share snapshots, but only if they are unencrypted.
 - These snapshots can be shared with other AWS accounts or made public.
 
 - Q1
    Root Device Volumes can now be encrypted. If you have an unencrypted root device volume that needs to be encrypted do th follwing;
    - Create a Snapshot of the unencrypted root device volume
    - Create a copy of the snapshot and select the encrypt option
    - Create an AMI from the encrypted Snapshot
    - Use that AMI to launch new encrypted instances

  - EBS vs Instance Store
    - Instance Store Volumes are sometimes called Ephemeral Storage.
    - Instance store volumes cannot be stoppted. If the underlying host fails, you will lose your data.
    - EBS backed instances can be stopped. You will not lose the data on this instance if it is stopped.
    - You can reboot both, you will not lose your data.
    - By default, both ROOT volumes will be deleted on termincation. However, with EBS volumes, you can tell AWS to keep the root device volume.

## Encrypting Root Device Volumes
 - Create a Snapshot of the unencrypted root device volume.
 - Create a copy of the Snapshot and select the encrypt option.
 - Create an AMI from encypted Snapshot
 - Use that AMI to launch new encypted instances.

## CloudWatch

 - CloudWatch is used for monitoring performance
 - CloudWatch can monitor most of AWS as well as your applications that run on AWS.
 - CloudWatch with EC2 will monitor events every 5minutes by default.
 - You can have 1 minute intervals by turning on detailed monitoring.
 - You can create CloudWatch alarms which trigger notifications.
 - CloudWatch is all about performance, CloudTrail is all about auditing.

 - What can i do with CloudWatch?
    - Dashboards - creates awsome dashboards to see what is happening with your AWS environment.
    - Alarms - Allows you to set Alarms that notify you when particular thresholds are hit.
    - Events - CloudWatch Events helps you to respond to state changes in your AWS resources.
    - Logs - CloudWatch Logs helps you to aggregate, monitor, and store logs.
 - CloudWatch vs CloudTrail
    - CloudWatch monitors performance
    - CloudTrail monitors API calls in the AWS platform.

## CLI
 - You can interact with AWS from anywhere in the world just by using ther command line(CLI)
 - You will need to set up access in IAM
 - Commands themselves are not in the exam, but some basic commands will be useful to know for real life.

## Roles
 - Roles are more secure than storing your access key and secret access key on individual EC2 instances.
 - Roles are easier to manage.
 - Roles can be assigned to an EC2 instance after it is created using both the console & command line.
 - Roles are universal - you can use then in any region.

## Bootstrap scripts
 - Bootstrap scrips run when an EC2 isntance first boots.
 - Can be powerful way of automating software installs and updates.

## Instance meta data & User data
 - Used to get information about an instance (Such as public IP)
 - curl http://123.123.123.123/latest/meta-data/
 - curl http://123.123.123.123/latest/user-data/

## EFS
 - Supprots the Network FIle System version 4 (NFSv4) Protocol
 - You only pay for the storage you use (no pre-provisioning required)
 - Can scale up to the petabytes
 - Can support thousands of concurrent NFS connections
 - Data is stored across multiple AZ's within a region
 - Read After Write Consistency

## EC2 Placement Groups

Three types placement groups
    
1. Clusterd Placement Group
    - Low Network Latency / High Network Throughput
2. Spread Placement Group
   - Individual Critical EC2 instances
3. Partitioned
    - Multiple EC2 instances HDFS, HBase, and Cassandra
 - Tips
    - A clusted placement group can't span multiple Availabilty zones.
    - A spread placement and partitioned group can.
    - The name you specify for a placement group must be unique within your AWS accout
    - Only certain types of instances can be launched in a placement group(Compute Optimized, GPU, Memory Optimized, Storage Optimized)
    - AWS recommend homogenous instances within clustered placement groups.
    - You can't merge placement groups.
    - You can't move an exising instance into a placement group. You can create an AMI from your exiting instance, and then launch a new intance from the AMI into a placement group
