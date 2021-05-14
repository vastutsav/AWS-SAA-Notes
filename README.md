# AWS-SAA-Notes

_Personal Notes for preparing for AWS Solution Architect Associate Certifications_

- [AWS-SAA-Notes](#aws-saa-notes)
  - [Global Infrastructure](#global-infrastructure)
  - [IAM](#iam)
  - [EC2](#ec2)
  - [EBS](#ebs)
  - [Instance store](#instance-store)
  - [EFS](#efs)
  - [Load Balancer](#load-balancer)
    - [Classic Load Balancer](#classic-load-balancer)
    - [Application load balancer](#application-load-balancer)
    - [Network Load Balancer](#network-load-balancer)
  - [ASG](#asg)
  - [RDS](#rds)
  - [Aurora](#aurora)
  - [Elasticache](#elasticache)
  - [Route 53](#route-53)
  - [Elastic Beanstalk](#elastic-beanstalk)
  - [S3](#s3)
  - [CloudFront](#cloudfront)
  - [AWS Global Accelerator](#aws-global-accelerator)
  - [Snow](#snow)
  - [Storage Gateway](#storage-gateway)
  - [Amazon FSx for Windows](#amazon-fsx-for-windows)
  - [Amazon FSx for Lustre](#amazon-fsx-for-lustre)
  - [SQS](#sqs)
  - [SNS](#sns)
  - [Kinesis](#kinesis)
  - [MQ](#mq)
  - [ECS](#ecs)
  - [Fargate](#fargate)
  - [ECR](#ecr)
  - [EKS](#eks)
  - [Lambda](#lambda)
  - [DynamoDB](#dynamodb)
  - [API Gateway](#api-gateway)
  - [Cognito](#cognito)
  - [SAM](#sam)
  - [CloudFront](#cloudfront-1)

## Global Infrastructure

- Regions
  - us-east-1, eu-west-3
  - has min 2, max 6, usually 3
  - cluster of data centers
  - choose region based on Complianced, Promixity, Available services, Pricing
- Availability Zones
  - e.g. ap-southeast-2a, ap-southeast-2b
  - one or more discrete data centers - reduntant power, networking, and connectivity
  - isolated from disasters
  - connected with high bandwidth, ultra-low latency networking
- Edge Locations
  - CloudFront uses these locations to cache copies of content for faster delivery to users at any location

## IAM
- Identity and Access Management - Global service
- Root account created by default
- Users - physical person
- Groups - contains only users, not other groups
- Roles - used when AWS service needs to perform activity on our behalf.
- users may or may not belong to one or more groups
- Policies
  - defines permissions in JSON doc
  - least privilege principle
  - can be applied to User or Group or Role
- can set Password Policy - Strong policy - min pass length, upper case, numbers, non-alphanumeric - prevent pass reuse - expiration policy
- MFA
- We can access AWS using
  - AWS Management Console
  - AWS Command Line Interface (CLI)
  - AWS Software Development Kit (SDK)
  - Access Key ID, Secret Access Key for SDK and CLI
  - Username and Password for Console
- IAM security tools
  - IAM Credential Report - list acc users and status of their credentials
  - IAM Access Advisor - shows permissions granted to a user and when those were last accessed.

## EC2
- Elastic Cloud Compute
- configuration options -
  - OS
  - CPU
  - RAM
  - Storage - EBS, EFS, Instance Store
  - Firewall rules - using Security Groups
  - EC2 User Data
    - contains commands to run when an instances starts for the first time.
- EC2 Instance Types
  - General Purpose - balanced Compute, memory and networking
  - Compute Optimized - high performance processors
  - Memory Optimized - process large data in memory
  - Storage Optimized - lots of read and write access to large data on local storage
- Security Groups
  - Controls traffic into or out of EC2 Instances
  - contains only allow rules
  - regulate on the basis of :
    - Port
    - IP ranges
    - Other Security Groups
    - Inbound traffic - blocked by default
    - outbound traffic - authorized by default
  - Can be attached to multiple instances
  - locked in VP/region combo
  - Application gives time out in case of SG issue
  - Application give connection refused error, in case of application error or it is not launched.
- Purchasing Options:
  - On-demand Instances 
    - short and uninterrupted workload
    - per second billing for Linux, per hour billing for others
    - highest cost - no upfront payment - no longterm commitment
  - On-Demand Capacity Reservations
    - reserve compute capacity for EC2 instances in a specific AZ for any duration
    - No 1 or 3 year commitment
    - Billing starts as soon as the capacity is provisioned
    - Cancel the Capacity Reservation to stop incurring charges
    - Specify
      - AZ
      - Number of instances
      - attributes like instance type, platform/OS
    - No billing discount.
  - Reserved
    - long workloads - period 1 or 3 years
    - 72 % discount
    - no upfront or partial upfront or all upfront payment
    - Convertible - flexibility of changing instance types
    - Scheduled - launch within time window
  - Spot instances
    - short workload, cheap, less reliable
    - use for failure resilient tasks
    - Not suitable for critical
    - define max spot price and get instance when *current price < max*
    - if *current price > max*, instance is stopped or terminated - 2 min grace time
    - Spot Block - block insatnce during specific time frame
    - Spot request is defined with 
      - max price
      - desired number of instances
      - type - One time or persistent
      - Valid from and to
    - only **open, active, or disabled** requests can be cancelled
    - First cancel Spot request and then terminate associated Spot instances
    - Spot Fleet
      - automatically request spot instances within cost and capacity
      - Strategies
        - lowest price - lowest price pool
        - diversified - across different pools
        - capacity optimized - pools with good capacity
  - Dedicated Hosts
    - book entire physical server
    - expensive
    - useful for compliance needs or complicated licences
  - Dedicated Instances
    - hardware is not shared with other customers
    - instances of the same account are on the same hardware
    - No control of which hardware will be used. Hardware may change when instance is stopped and started
- Placement groups
  - Control over where the EC2 instances are placed
  - Cluster - low-latency group
  - Spread - high available - spread across different hardware - max 7 instance per group per AZ
  - Partition - instances in partition - all instances in partition in same hardware - 7 partition per AZ
- An EC2 instance has Public and Private IP
  - Public IP changes after every restart
  - Private IP stays constant
- Elastic IP
  - fixed public IP
  - can attach it to one instance at a time
  - can be quickyly remapped to another instance
  - the mapped instance will accessible using the elastic IP
- Elastic Network Interface
  - has Primary Private IP , one or more secondary IP
  - One Elastic IP
  - One Public IP
  - 1 or more security groups
  - A MAC address
  - Can be created independently and attached on EC2 instnaces
  - Bound to AZ
- EC2 Hibernate
  - RAM is preserved - written to a file in the root EBS
  - boots faster
  - RAM size must be less than 150 GB
  - Root volume must be encrypted
  - For On-demand and Reserved Instances
  - cannot be hibernated more than 60 days
- Nitro
  - better performance
  - better netwroking options
  - Higher speed EBS - 64000 EBS IOPS
  - better security
- In EC2, we can change number of CPU cores (decrease licensing costs), or decrease number of threads

## EBS
- Elastic Block Store - network drive
- allows instance to persist data after termination
- bound to one AZ
  - To move, need to create Snapshot
- might be a bit of latency
- can be attached and detached easily
- Provisioned capacity
  - can increase capacity over time
- We can delete EBS on termination
 Snapshot
  - to create backup
  - stop EC2 before creating snapshot
  - AMI
    - Amazon Machine Image
    - can add your own software, conf, operating system, monitoring
    - has faster boot/configuration time
    - Built for specific region
    - can be Public/Own AMI/Marketplace
    - create AMI from EBS snapsht
    - Volume types
      - gp2/gp3 - general purpose - 1GB to 16TB - max IOPS 1600
      - io1/io2 - low-latency - max IOPS - 32000 for other and 64000 for nitro, multi-attach
      - st1 - low cost HDD, frequently accessed, throughput -intensive , max IOP - 500
      - sc1 -  infrequently accessed - max IOP - 250
  - Encryption
      - encrypted at rest and in transit
      - transparent - Key from KMS
      - Snapshots of encrypted EBS is encrypted
      - To create an encrypted EBS from unencrypted
      - create the encrypted snapshot
      - create EBS vol from snapshot
      - attach the EBS vol to the original instance
  - RAID Options
    - RAID 0
      - increases performances
      - combining multiple volume - getting total space and IO
      - not fault tolerant
    - RAID 1
      - increases falt tolerance
      - mirroring a volume to another
    - RAID 5, 6 not supported

## Instance store
- better I/O performance
- ephemeral - data lost when instance stopped
- Good for buffer/cache/ scratch data
- Risk of data loss if hardware fails
- Backups and Replication - our responsibility

## EFS
- Elastic file system
- network file system
- works in multiple AZs - EC2 in different AZ can access a single EFS
- Highly available, scales autamically , expensive, pay per use
- use case - content management
- uses NFSv4.1 protocol
- Compatible with Linux, not Windows
- Encryption at rest using KMS
- POSIX file system
- Storage classes
  - Performance mode
    - general purpose
    - Max I/O
  -  Throughput mode
     - Bursting (1 TB = 50MiB/s + burst of up to 100MiB/s)
     - Provisioned - set throughput regardless of size
  -  Storage Tiers
      - Standard: for frequently accessed file
      - Infrequent access (EFS-IA): cost to retrieve files, lower price to store


## Load Balancer
- why?
  - Spread load across multiple downstream instances
  - Expose a single point of access (DNS) to your application
  - handle failures of downstream instances
  - healthchecks
  - SSL endpoint
  - enforce stickiness
    - Classic and Application load balancers
    - helps in maintaining session data - redirecting the same client to the same instance
    - uses cookie - cookie has expiration date
  - Separate public traffic from private

- Cross Zone load Balancing
  - when enabled - distributes evenly across instances in all AZ

- SSL Certs 
  - needed for inflight encryption
  - can be managed using ACM (AWS Certificate Manager) Or upload own certs
  - can use Server Name Indication to handle multiple certs for multiple websites - client needs to indicate hostname

- Connection Draining
  - Time to complete in-flight requests while the instance is deregistering or unhealthy
  - Stops sending new requests to the instance which is de-registering
  - 0 to 3600 secs, (default 300) (0 Disabled)

### Classic Load Balancer
- HTTP/HTTPS, TCP
- Fixed hostname *XXX.region.elb.amazonaws.com*
- Classic Load Balancer needed per application
- Cross Zone load Balancing
  - through Console - Enabled by default 
  - through CLI / API - Disabled by default
  - No charges for inter AZ data if enabled
- Support only one SSL certificate
  - Must use multiple CLB for multiple hostname with multiple SSL certificates
- Connection draining available

### Application load balancer
- HTTP
- Fixed hostname *XXX.region.elb.amazonaws.com*
- load balancing - multiple machines or multiple apps on same machine
- supports HTTP, WebSocket and redirects (like from HTTP to HTTPS)
- Routing table - contains routing rules - rules can be based on
  - path in URL
  - Hostname in URL
  - Query String, Headers
- Targets for Routing can be
  - EC2 instances
  - ECS tasks
  - Lambda Functions
  - IP Addresses
- Health checks at target group level
- Cross Zone load Balancing - Always On
- SSL Cert - Supports multiple listeners with multiple SSL certificates
  - Uses Server Name Indication (SNI) to make it work
- Connection draining is called Deregistration Delay
- app can see the true details of client in header:
   |Item| Field|
   |--|--|
   |IP|X-Forwarded-For|
   |Port|X-Forwarded-Port |
   |proto |X-Forwarded-Proto|

### Network Load Balancer
- Forward TCP & UDP
- millions records/sec
- less latency
- one static IP / AZ
- extreme performance
- Cross Zone load Balancing - Disabled by default - if enabled, charges for inter AZ
- SSL Cert - Supports multiple listeners with multiple SSL certificates
  - Uses Server Name Indication (SNI) to make it work
- Connection draining is called Deregistration Delay

## ASG
- Auto Scaling Groups
- Scale out for increased load, Scale in for decreased load - num of instances is within min and max defined
- Automatically registers new instances to a load balancer
- Has attributes:
  - Defined in a lainch configuration
    - AMI + Instance Type
    - EC2 User Data
    - EBS Volumes
    - Security Group
    - SSH Key Pair
  - Min/Max/initial Capacity
  - Nw + subnet info
  - Load Balancer info
  - Scaling policy
- Scaling Metric can be 
  - CloudWatch alarms can be used to scale
    - alarm to monitor metrics for all ASG instances like Avrg CPU
    - based on alarm - we can increase or decrease the # of instances
  - Scale using EC2 managed rules
    - Target Avg CPU Usage
    - #of requests on the ELB per instance
    - Avg Nw In/Out
  - Custom Metric
    - can create custom metric
    - send metric from app on EC2 to CloudWath *PutMetric API*
    - create Cloudwatch alarm to react to low/high values
    - use CW alarm as the scaling policy
  - Metric can be based on schedule
- To update ASG, prve new launch configuration/launch template
- IAM roles on ASG will be assigned to EC2
- can terminate instances marked as unhealthy by LB

- Scaling Policies:
  - Target Tracking - e.g. avg CPU to stay around 40%
  - Simple/step Scaling - e.g. When CW alarm is triggered, add 2 units or remove 1 units
  - Scheduled Actions - e.g increase the min cap to 10 at 5pm on Fridays

 - Scaling Cooldown
   - ensures ASG does not launch or terminate addn. instances before the prev scaling activity takes effect.
   - can have scaling specific cooldown period
 
 - ASG balances #of instances across AZ
   - Default termination Policy - find AZ with most number of instances - delete the one with the oldest launch configuration

- Lifecycle Hooks
  - By Default, launched instance is in service
  - Can perform steps before the instance goes in service
  - Can perform steps before the instance is terminated

|Launch Configuration| Launch Template|
|-|-| 
|must be created everytime|multiple version supported
||Create parameter subset - partial configs for re-use or inheritance
||Provision using both On-Demand and Spot Instances
||Can use T2 unlimited burst feature|

## RDS
- Relational Database Service
- Supports:
  - Postgres
  - MySQL
  - MariaDB
  - Oracle
  - MS SQL Server
  - Aurora (AWS Propriety database)

*<b>WHY Use?</b>*
- Automated provisioning, OS patching
- supports continuous Backups - Point in time restore
  - automatically enabled
  - daily during maintenance window
  - transaction logs backed up every 5 minutes
  - 7 days retention (can be max 35 days)
- DB snapshots
  - manually triggered
  - retention as long as you want
- monitoring dashboards
- read replicas for improved read performance
  - up to 5 replicas
  - within AZ, Cross AZ, or Cross Region
  - Async
  - Replica can be promoted to DB status
  - Applications must update connection string to leverage read replicas
  - For SELECT only
  - Netwrk cost when data goes from one region to another - no fee for within region
- Disaster recovery - multi AZ setup
  - SYNC replication
  - One DNS namme - automatic failover to standby server
  - increase availability
  - no manual intervention
  - not for scaling
  - Multi-AZ free
- Vertical and horizontal Scaling
  - dynamically increased - automatically scaled
  - have to set Maximum Storage Threshold (maximum limit for DB storage)
  - useful for unpredictable workloads
- gp2 or io1 EBS backed

- Can convert Single-AZ RDS to Multi-AZ with zero downtime
  - a snapshot is taken
  - a new DB is retired from the snapshot in a new AZ
  - Synchronization is established between the two DBs

- At rest Encryption
  - possible with AWS KMS - AES 256 encryption
  - has to be defined at launch time
  - if master is not encrypted, the read replicas cannot be encrypted
  - TDE for Oracle and SQL server

- In flight encryption
  - SSL cert

- Encrypt RDS backups - copy snapshot and enable encypted
- Encrypt RDS DB 
  - create snapshot 
  - copy snapshot and enable encryption for the snapshot
  - restore DB from encypted snapshot
  - Move apps to new DB - delete old DB

- Security
  - Network Security
    - deployed in pvt subnet
    - leverages security groups
  - Access Management
    - IAM policies - control who manages AWS RDS
    - can have tradional Usrname/Password OR IAM-based authentication to login into DB (IAM auth works for MySQL and PostgreSQL)


## Aurora
- Postgres and MySQL supported
- can have 15 replicas - sub 10 ms replica lag
- Automatic Failover
- costs more than RDS - more efficient
- 6 copies across 3 AZ
  - needs 4 for writes
  - needs 3 for reads
  - self haling with p2p replication
  - 100 of volumes
- One master takes writes
- Automated failover for master in less than 30 seconds - a read replica is promoted to be master
- Master + up to 15 Aurora Read Replicas serve reads
- Support for Cross Region Replication
  - useful for disaster recovery
- Backup and Recovery
- Isolation and security
  - Encyption at rest using KMS
  - Encryption in flight using SSL
  - can authenticate using IAM token
  - can't SSH
- Industry compliance
- Auto scaling
  - storage automatically grows in increments of 10GB, up to 64TB
- Automated Patching with Zero Downtime
- Advanced Monitoring
- Routine Maintenance
- Backtrack - restore data to any point of time without using backups
- Serverless
  - pay per second
  - Automated database instantiation and auto-scaling based on actual usage
  - for infrequent, unpredictable workloads
  - No capacity planning needed
- Aurora Multi-master
  - for immediate failover
  - every node performs R/W
- Global Database
  - 1 Primary Region (read/write)
  - upto 5 read-only region
  - upto 16 Read Replica per secondary region

## Elasticache
- Redis and Memcached
- Caches - high performance, low latency
- fo read intensive workloads
- AWS takes care of OS maintenance/patching, optimizations, setup, confiuration, monitoring, failure recovery and backups
- Apps queries ElastiCache - if not present, get from RDS and store in ElastiCache
- Cache must have an invalidation strategy to make sure most current data is used
- Redis vs Elasticache
  |Redis|Memcached|
  |-|-|
  |Read Replicas - High Availability|No High availability|
  |Perstience of data|Non persistent|
  |Backup and restore| No Backup and restore|
  |Uses single core|Multi-threaded architecture|

- Security
  - Does not support IAM authentication
  - IAM used only for AWS API-level security
  - REDIS
    - can set password when creating cluster
    - in-flight encryption using SSL
  - Memcached
    - Supports SASL-based authentication
- Caching Strategies
  - Lazy Loading - loads data into the cache only when necessary
  - Write Through - y adds data or updates data in the cache whenever data is written to the database
  - TTL - specifies the number of seconds until the key expires
## Route 53

## Elastic Beanstalk

## S3

## CloudFront

## AWS Global Accelerator

## Snow

## Storage Gateway

## Amazon FSx for Windows

## Amazon FSx for Lustre


## SQS

## SNS


## Kinesis


## MQ


## ECS

## Fargate


## ECR


## EKS


## Lambda


## DynamoDB


## API Gateway


## Cognito


## SAM


## CloudFront

