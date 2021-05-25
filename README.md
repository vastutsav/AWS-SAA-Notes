# AWS-SAA-Notes

                    *Estimated reading time: 30 minutes*

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
  - [Athena](#athena)
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
  - has min 2, max 6, usually 3 AZ
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
- Elastic Block Store - network drive - used by EC2 instance
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
  - *Multi AZ keeps the same connection string regardless of which database is up. Read Replicas imply we need to reference them individually in our application as each read replica will have its own DNS name*
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
- pay as you go
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
- Types of endpoint:
  - Cluster endpoint - supports writing/reading 
  - Reader endpoint - supports reading from one of the the Read Replicas - acts as a load balancer
  - Custom endpoint - a set of DB instances that you choose - load balancer
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
- REDIS sorted sets - guarantees uniqueness and element ordering 
  - each time a new element is added, it is ranked in real time, then added in correct order
- Security
  - Does not support IAM authentication - any operation within cache is not using IAM
  - IAM used only for AWS API-level security - for deleting the cache
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
- Domain Name System
- Global service
- helps client understand how to reach a server through URLs using collection of rules and records
  - A: hostname to IPV4
  - AAAA: hostname to IPV6
  - CNAME: hostname to hostname
  - Alias: hostname to AWS resource
- can use public domain names you own or private domain names that can be resolved in your VPC
- Load balancing
- Health check
- Routing policy
  - Simple Routing Policy
    - Maps a hostname to another hostname
    - Use when you need to redirect to a single resource
    - can't attach health checks
  - Weighted Routing Policy
    - Control the % of the requests that go to sprcific endpoint
    - Can be associated with Health Checks
  - Latency Routing Policy
    - Redirect to the server that has the least latency close to us
  - Failover Routing Policy
    - route traffic to a resource when the resource is healthy or to a different resource when the first resource is unhealthy.
  - Geo Location Routing Policy
    - routing based on user location
    - should create a default policy
  - Geo Proximity Routing Policy
    - Route traffic to your resources based on the geographic location of users and resources
    - Can choose to route more traffic or less to a given resource by specifying a value, known as a bias
    - To use geoproximity routing, you must use **Route 53 traffic flow**
  - Multi-value Routing Policy
    - Use when routing traffic to multiple resources
    - Associate health checks with the records
    - up to 8 healthy records are returned
- Can use Route 53 with domain bought from a 3rd party
  - Create a hosted zone in Route 53
  - Update NS Records on 3rd party website to use Route 53 name servers

- CNAME vs ALIAS
  - |CNAME|ALIAS|
    |-|-|
    |Points a hostname to another hostname|Points a hostname to another AWS resource|
    |Only for NON-ROOT domain|Works for ROOT and NON-ROOT|
    |Charged|Free of charge|
    |No native health check| Native Health Check|

- Health Checks
  - Have X health checks failed => unhealthy (default 3)
  - After X health checks passed => health (default 3)
  - Default Health Check Interval: 30s (can set to 10s – higher cost)
  - About 15 health checkers will check the endpoint health
    - one request every 2 seconds on average
  - Can have HTTP, TCP and HTTPS health checks (no SSL verification)
  - Possibility of integrating the health check with CloudWatch
  - Health checks can be linked to Route53 DNS queries!
  - In health check, the resource is pinged from health checkers in different locations.
## Elastic Beanstalk

## S3
- store objects(files) in buckets(like directories).
- Buckets
  - globally unique name
    - no uppercase, or underscore, or IP
    - 3-63 characters long
    - start with a lowercase of number
  - defined at region level
- Object have
  - Key - prefix+object name
  - value are the content of the Object body
    - max size 5TB
    - for uploading more than 5GB, use multi-part upload
  - Metadata
  - Tags - useful for security/lifecycle
  - Version ID
- Versioning of files is enabled at bucket level
  - Any file that is not versioned prior to enabling versioning will have version “null”
  - Suspending versioning does not delete the prev versions
- Objects can be encrypted by using:
  - SSE-S3 - encryption usig keys handled and managed by AWS
    - Object encrypted server side
    - AES-256
    - Must set header **"x-amz-server-side-encryption": "AES256"**
  - SSE-KMS - using KMS to manage keys
    - user control + audit trail
    - encrypted server side
    - Must set header **"x-amz-server-side-encryption": ”aws:kms"**
  - SSE-C - you manage keys
    - server side encryption
    - S3 does not store the encryption key you provide
    - HTTPS mandatory
    - Encryption key to be provided in headers
  - CLient side encryption
    - Clients must encrypt data themselves before sending, and decrypt data themselves when retrieving
    - uses AmazonS3EncryptionClient
-  S3 exposes HTTP and HTTPS endpoints
-  S3 access can be defined using:
   -  IAM policies
   -  Bucket policies
      -  JSON based policies
      -  Allow/Deny a Principal on a Resource
   -  Object Access Control List
   -  Bucket Access Control List
- Supports VPC Endpoints
- Access Logs can be stored in other S3 buket
- API call can be logged in AWS CLoudTrail
- User Security
  - MFA Delete - can be required in versioned bucket to delete objects
  - Pre-Signed URLs: URLs that are valid only for a limited time
- S3 can host static websites
  - *\<bucket-name\>.s3-website-\<AWS-region\>.amazonaws.com* OR *\<bucket-name\>.s3-website.\<AWS-region\>.amazonaws.com*
- S3 CORS
  - Cross-Origin Resource Sharing
  - An origin is a scheme(protocol), host(domain) and port
  - Web Browser based mechanism to allow requests to other origins while visiting the main origin
  - The requests won't be fulfilled unless the other origin allows for the requests, using CORS Headers *(Access-Control-Allow-Origin)*
  - If a client does a cross-origin request on our S3 bucket, we need to enable the correct CORS headers
  - can allow for a specific origin or for * (all origins)
- Consistency Model
  - read after write consistency
  - list consistency
- MFA Delete
  - when versioning enabled
  - for permanently deleting object version
  - for suspending versioning
  - only bucket owner can enable/diable - enable from CLI only
- Acess Logs
  - can log all access to S3 buckets
  - will be logged into another S3 bucket
- Replication
  - must enable versioning in source and destination
  - Cross Region Replication
  - Same Region Replication
  - Buckets can be in different accounts
  - Copying is asynchronous
  - Must give proper IAM permissions to S3
  - After activation only new objects are replicated
  - Delete operations not replicated - with version, a delete marker is added
  - No chaining of replications
- Pre-Signed URLs
  - Can generate pre-signed URLs using SDK or CLI
  - Default 3600 seconds validity, can be set using TIME_BY_SECONDS argument
  - with presigned URL, user inherits the permissions of the creator
- S3 Storage classes
  - S3 Standard - General Purpose
    - High durability across multiple AZ
    - 99.99% Availability
    - Sustain 2 concurrent faciity failures
  - S3 Standard - Infrequent Access
    - when data is less frequently accessed, but requires rapid access when needed
    - High durability across multiple AZ
    - 99.9% Availability
    - lower cost than S3 Standard
    - Sustain 2 concurrent facility failures
    - Retrival fee per GB retrieved
  - S3 One-Zone - Infrequest Access
    - same as IA, but stored in single AZ
    - High durability - but data lost when AZ is destroyed
    - 99.5% Availability
    - Low latency and high throughput performance
    - Low cost compared to IA
    - Retrieval fee per GB retrieved
  - S3 Intelligent Tiering
    - low latency and high throughput same as S3 Standard
    - small monthly monitoring and auto-tiering fee
    - automatically moves objects between two access tiers based on changing access patterns
    - High durability
    - Retrival fee per GB retrieved
  - Glacier
    - low cost storage - archiving and backup
    - data retained for longer term
    - each item is called Archive
    - Archives are stored in Vaults
    - Minimum storage duration of 90 days
    - Retrival fee per GB retrieved
    - Retrieval options
      - Expedited (1 to 5 minutes)
      - Standard (3 to 5 hours)
      - Bulk (5 to 12 hours)
  - Glacier Deep Archive
    - for long term storage - cheaper
    - min storage of 180 days
    - Retrieval options
      - Standard (12 Hours)
      - Bulk (48 hours)
    - Retrival fee per GB retrieved

- Moving objects between different storage tiers can be automated using **lifecycle configuration**
  - Transition actions - defines when objects are transitioned to another storage class
  - Expiration actions - configure objects to expire(delete) after some time
  - Rules can be created for certain prefix (s3://mybucket/mp3*) or object tags (Department:Finance)
- S3 Analytics
  - Storage class analytics to help determine when to transition objects
  - does not work for One zone or Glacier
  - daily report
- S3 - KMS limitation
  - All key upload and download activity is counted towards the KMS quota per second - quota can not be increased
- Multi Part upload
  - for files > 100MB, must for > 5GB
  - helps parallelize uploads
- Transfer Accelaration
  - Increase transfer speed by transferring file to an AWS edge location which will forward the data to the S3 bucket in teh target region
  - Compatible with multi-part upload
- Byte range fetches
  - Parallelize GETs by requesting specific byte ranges
  - better resilience in case of failures
  - can be used to retrieve only partial data
- S3 Select & Glacier Select
  - Retrieve less data using SQL - server side filtering
  - less network transfer, less CPU cost client-side
- In case of **Requester Pays** buckets, the requester instead of bucket owner pays the cost of the request
  - the requester must be authenticated in AWS
- Glacier Vault Lock
  - Adopt a Write Once Read Many model
  - lock the policy for future edits
  - helpful for compliance and data retention
- S3 object lock
  - versioning must be enabled
  - block an object version deletion for a specified amount of time
  - Object retention - Retention period(fixed period) or Legal Hold(no fixed period)
  - Modes
    - Governance mode - can't overwrite or delete object version or alter lock permissions unless they have special permissions
    - Compliance mode - object version cannot be overwritten or deleted by any user, including root user.
      - retention mode can't be changed - retention period can't be shortened

## Athena
- Serverless service to **perform analysis directly against S3 files**
- Uses SQL language to query the files
- JDBC/ODBC driver
- charged per query and amount of data scanned
- supports CSV, JSON, ORC, Avro, and Parquet

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

