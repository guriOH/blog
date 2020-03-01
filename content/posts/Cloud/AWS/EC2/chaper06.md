# EBS1 Volumne

EBS 스토리지는 ec2 인스턴스와 같은 가용공간에 있어야함.

추가적인 볼륨들은 Ec2인스턴스 삭제를 해도 지워지지 않는다.


Tips

* EBS : virtual hard disk
* Snapshots exist on S3. Think of snapshots as a photograph of the disk
* Snapshots are point in time copies of Volumes.
* Snapshots are incremental - this means that only the blocks that have changed since your last snapshot are moved to S3.
* To create a snapshot for Amazone EBS volumes that serve as root devices, you should stop the instance before taking the snapshot.
* however you can snap while instance running.
* You can create AMI's from both Volumes and Snapshots
* You can change EBS volume size on the fly, including changing the size the type.
* Volume will ALWAYS be in the same availability zone as the EC2 instance.

* To move an EC2 volume from one AZ to another, take a snapshot of it, create an AMI from the snapshot and then use the AMI to launch the EC2 instance in a new AZ.

* To move an EC2 volume from one region to another.
EC2 -> snapshot -> AMI -> copy AMI to another zone.
