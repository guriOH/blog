# S3 & IAM

Identiti Access Management Consists of the follwing;
 - Users
 - Groups
 - Roles
 - Policies


IAM summary

 - IAM is universal. if does not apply to regions at this time.
  - The "root account" is simply the account created when first setup your AWS account. It has complete Admin access.
  - New Users have NO permissions when first created.
  - New Users are assigned Access Key ID & Secret Access Keys when first created.
  - These are not the same as a password. You cannot use the Access Key ID & Secret Access Key to Login in to the console. You can use this to access AWS via the APIs and CLI
  - You only get to view these once. If you lose them, you have to regenerate them, so save them in a secure location.
  - Always setup Multifactor Authentication on your root account.
  - You can create and customise your own password rotation policies.

S3 summary
 - Rememver that S3 is Object-based; i.e.allows you to upload files.
 - Files can be from 0 Bytes to 5 TB.
 - There is unlimited storage.
 - Files are stored in Buckets.
 - S3 is a universal namespace. That is, names must be unique globally.

 - Not suitable to install an operating system on.
 - By default, all newly created buckets are PRIVATE. You can setup access control to your buckets using;
    - Bucket Policies
    - Access Control Lists
 - S3 buckets can be configured to create access logs which log all requests made to the S3 bucket. This can be sent to another bucket and even another bucket in another account.

 The Key fundamentals of S3 
  - Key
  - Value
  - Version ID
  - Metadata
  - Subresources
  - Read after consistency for PUTS of new Objects
  - Eventual Consistency for overwrite PUTS and DELETES (can take some time to propagate)

Exam Tips
 - S3 standard
 - S3 - IA
 - S3 One zone - IA
 - S3 - intelligent Tiering
 - S3 Glacier
 - S3 Glacier Deep Archive

Encryption In Transit is achieved by
 - SSL/TLS
Encryption At Rest (Server Side) is achieved by
 - S3 managed Keys - SSE-S3
 - AWS Key Management Service, Managed Keys - SSE-KMS
 - Server Side Encryption With Customer Provided Keys SSE-C
Client Side Encryption
 
Cross Region Replication
 - Versioning must be enabled on both the source and destination buckets.
 - Regions must be unique.
 - Files in an existing bucket are not replicated automatically
 - All subsequent updated files will be replicated automatically.
 - Delete markers are not replicated.
 - Deleting individual versions or delete markers will not be replicated.
 - Understand what Cross Region Replication is at a high level.
 
Lifecycle Policies
 - Automates moving your objects between the different storage tiers.
 - Can be used in conjunction with versioning.
 - Can be applied to current versions and previous versions.

 
CloudFront 
 - Edge Location : This is the location where content will be cached. This is separate to an AWS Region/AZ.
   - Edge location are not just READ only -- you can write to them too.(ie. put an object on to them) 
   - Objects are cached for the life of the life of the TTL (Time To Live)
   - You can clear cached objects, but you will be charged.
 - Origin : This is the origin of all the files that the CDN will distribute. This can be either an S3 Bucket, an EC2 instance, an Elastic Load Balancer, or Route53
 - Distribution : This is the name given the CDN which consists of a collection of Edge Locations.
 - Web Distribution : Typically used for Websites.
 - RTMP : Used for Media Streaming.

Snowball
 - Understand what snowball is
 - Snowball can
 - Import to S3
 - Export from S3

Storage Gateway
 1. File Gateway
   - For flat files, stored directly on S3.
 2. Volume Gateway 
   - Stored Volumes - Entire Dataset is stored on site and is asynchronuly backed up to S3.
   - Cached Volumes - Entire Dataset is stored on S3 and the most frequently accesed data is cached on site
 3. Gateway Virtual Tape Libary
   - Used for backup and uses popular backup applications like NetBackup, Backup Exec, Veem etc.
   
Tips
 * Read the S3 FAOs before taking the exam, It comes up a LOT !!!!!