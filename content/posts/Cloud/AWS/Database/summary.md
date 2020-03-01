# Databases Summary

# RDS (OLTP)
 - SQL
 - MySQL
 - PostgreSQL
 - Oracle
 - Aurora
 - MariaDB

# DynamoDB (NoSQL)

# Red Shift OLAP

# Elasticache
 - Memcached

 - Redis

# Remember 
 - RDS runs on virtual machines
 - You cannot log in to these operating systems however.
 - Patching of the RDS Operating System and DB is Amazon's responsiblilty 
 - RDS is NOT Serverless
 - Aurora Serverless IS Serverless

# Two different types of Backups for RDS
 - Automated Backups
 - Databases Snapshots

# Read Replicas
 - Can be multi-az
 - Used to increase performance.
 - Must have backups turned on.
 - Can be in different regions.
 - Can be MySQL, PostgreSQL, MariaDB, Oracle, Aurora
 - Can be promoted to master, this will break the Read Replica


# MultiAZ
 - Used For DR.
 - You can force a failover from one AZ to another by rebooting the RDS instance.


# DynamoDB
 - Storead on SSD storage
 - Spread Across 3 geographically distinct data centres
 - Eventual Consistent Reads (Default)
 - Strongly Consistent Reads

 # Redshift Backups
  - Enabled by default with a 1 day retnetion period.
  - Maximun retention period is 35 days.
  - Redshift always attempts to maintain at least three copies of your data (the original and replica on the compute nodes and a backup in amazon S3)
  - Redshift can also asynchronously replicate your snapshots to S3 in another region for disaster recovery

# Aurora
 - 2 copies of your data is contained in each availablity zone. with minu



# EFS (Elastic File System)
 - AWS 클라우드 서비스와 온프레미스 리소스에서 사용할 수 있는, 간단하고 확장 가능하며 탄력적인 완전관리형 NFS 파일 시스템을 제공
 - 애플리케이션을 중단하지 않고 온디맨드 방식으로 페타바이트 규모까지 확장하도록 구축되어, 파일을 추가하고 제거할 때 자동으로 확장
 - 수천 개의 Amazon EC2 인스턴스에 대한 병렬 공유 액세스를 대규모로 제공하도록 설계되었으므로, 애플리케이션은 일관되게 낮은 지연 시간을 유지하면서 높은 수준의 집계 처리량과 IOPS를 달성할 수 있습니다.

# EBS (Elastic Block Storage)
 - 대규모로 처리량과 트랜잭션 집약적인 워크로드 모두를 지원하기 위해 Amazon Elastic Compute Cloud(EC2)에서 사용하도록 설계된 사용하기 쉬운 고성능 블록 스토리지 서비스입니다