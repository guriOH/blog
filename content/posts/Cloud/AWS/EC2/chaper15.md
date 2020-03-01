# EC2 Placement Groups


Three Types of placement Groups;

1. Clustered Placement Group
    * Low Network Latency / High Network Throughput
2. Spread Placement Group
    * Individual Critical EC2 instances
3. Partitioned
    * Multiple EC2 instances HDFS, HBase, and Cassandra


Tips
* A clustered placement group can't span multiple Avilability Zones.
* A spread placement and partitioned group can
* The name you specify for a placement group must be unique within your AWS account.
* Only certain types of instances can be launched in a placement group(Compute Optimized, GPU, Memory Optimized, Storage Optimized)
* AWS recomment homogenous instances within clustered placement groups.
* You can't merge placement groups
* You can't move a existing instance into a placement group. You can create an AMI from your exisiing instance, and then launch a new instance from the AMI into a placement group.