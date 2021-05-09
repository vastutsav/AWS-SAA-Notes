# AWS-SAA-Notes

_Notes for preparing for AWS Solution Architect Associate Certifications_


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
    - AMazon Machine Image
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
- ephemeral
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
- Enxryption at rest using KMS
- POSIX file system
- Storage classes
  - Performance mode
    - general purpose
    - MAx I/O
  -  Throughput mode
     - Bursting
     - Provisioned
  -  Storage Tiers
      - Standard: for frequently accessed file
      - Infrequent access (EFS-IA): cost to retrieve files, lower price to store 
