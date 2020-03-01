---
title: VPC overview
date: 2020-01-15 12:55:35
tags:
---

# What can we do with a VPC
 - Launch instances into a subnet of your choosing
 - Assign custom IP address ranges in each subnet
 - Configure route tables between subnets
 - Create insternet gateway and attach it to our VPC
 - Much better security control over your AWS resources
 - Instance security groups
 - Subnet network access control lists (ACLS)

 # Default VPC vs Custom VPC
  - Default VPC is user friendly, allowing you to immediately deploy instances.
  - All Subnets in default VPC have a route out to the internet
  - Each EC2 instance has both a public and private IP address.

# VPC Peering
 - Allows you to connect one VPC with another via a direct network route using private IP Addresses.
 - Instances behave as if they were on the same private network
 - You can peer VPC's with other AWS accouhnts as well as with other VPCs in the same account.
 - Peering is in a star configuration : ie 1 central VPC peers with 4 others.
 NO TRANSITIVE PEERING
 
 # NAT instance 
  - When creating a NAT instance, Disable Source/Destination Check on the instance.
  - NAT instances must be in a public subnet.
  - There must be a route out of the private subnet to the NAT instance, in order for this to work.
  - The amount of traffic that NAT instances can suppprot depends on the instance size. If you are bottlenecking, increase the instance size.
  - You can create high availability using Autoscaling Groups, multiple subnets in different AZs, and a script to automate failover.
  - Behind a security Group.

# NAT Gatways
 - Redundant inside the Availability Zone
 - Preferred by the enterprise
 - Starts at 5Gbps and scale currently to 45Gbps
 - No need to patch
 - Not associated with security groups
 - Automatically assigned a public ip address
 - Remeber to update your route tables.
 - No need to disable Source/Destination Checks

 