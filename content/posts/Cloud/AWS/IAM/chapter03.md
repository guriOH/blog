# S3

What is S3?
 - S3 is a safe place to store your files.
 - It is Object-based storage.
 - The data is spread across multiple devices and facilities.

Basic of S3
 - S3 is Object-based - i.e. allows you to upload files.
 - Files can be from 0 Bytes to 5 TB.
 - There is unlimited storage.
 - Files are stored in Buckets.
 - S3 is a universal namespace That is names must be unique globally.
 ex> httpL//s3-eu-west-1.amazoneaws.com/aaaa
 - When you upload a file to S3, you will receive a Http 200 code if the upload was sucessful.

Object
 - Key
 - Value
 - Version ID
 - Metadata
 - Subresources;
    - Access Control lists

How does data consistency work for S3?
 - Read after Write consistency for PUTs of new Objects
 - Eventual Consistency for overwrite PUTS and DELETES (can take some time to propagate)

In other words;
 - If you write a new file and read if immediately afterward, you will be able to view that data.
 - If you update AN EXISTING file or delete a file and read it immediately, you may get the older version, or you may not, Basically changes to objects can take a little bit of time to propagate.

S3 features;
 - Tiered Storage Available
 - Lifecycle Management
 - Versioning
 - Encryption
 - MFA delete
 - Secure your data using Access Control lists and Bucket Policies

S3 Storage classes
1. S3 Standard
2. S3 - IA(Infrequently Accessed) 
3. S3 One Zone - IA
4. S3 - Intelligent Tiering
5. S3 Glacier
6. S3 Glacier Deep Achive

S3 Transfer Acceleration
