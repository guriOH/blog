# AMI types (EBS vs Instance store)


* Select AMI based on:

1. Region
2. Operating system.
3. Architecture (32-bit or 64-bit)
4. Launch Permissions
5. Storage for Root device


## All AMIs are categorized as either backed by EBS or Instance store

* For EBD Volume : The root device for an instance launched from the AMI is an Amazon EBS volume created from an Amazon EBS snapshot

* For Instance Store Volumes : The root device for an instance launched from the AMI is an instance store volume created from a template stored in Amazon S3.

tips 
* Instance Store Volume are somethimes called Ephemeral Storage
* Instance store volume cannot be stopped If the underlying host fails, you will lose your data.
* EBS backed instances can be stopped. You will not lose the data on this instance if it is stopped.
* You can reboot both, you will not lose your data.
* By default, both ROOT volumes will be deleted on termination. However, with EBS volumes, you can tell aws to keep the root device volume

