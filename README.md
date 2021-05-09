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


## ASG
- Auto Scaling Groups

## RDS

## Aurora

## Elasticache

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

