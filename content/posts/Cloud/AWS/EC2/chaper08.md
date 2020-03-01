# Encryped Root Device Volumes & Snapshots


* Snapshots of encrypted volumes are encrypted automatically
* Volumes restored from encrypted snapshots are encrypted automatically
* You can share snapshots, but only if they are unencrypted.
* These snapshots can be shared with other AWS accounts or made public


Process
1. Create a Snapshot of the unencrypted root device volume
2. Create a copy of the Snapshot and select encrypt option
3. Create an AMI from the encrypted Snapshot
4. Use that AMI to launch new encryted instances