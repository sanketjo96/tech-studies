# AWS

#### EC2

1. EC2 Types
   1. General purpose
       - Balance between compute, networking and memory
       - Ideal for code repo or web servers
       - **Example T family**
   2. Compute optimize
       - Used for compute intensive workloads like
         - Media transcoding
         - High perf web servers
         - Scientific data modeling 
         - Gaming 
         - **Example C family**
   3. Memory optimized
       - Useful for processing large data in memory
         - High perf relational/non relational DBs
         - In mem DBs for BI
         - Real time processing apps
         - **Example R family**
   4. Storage optimized
       - Use cases where there are high sequential read and writes to local storage
         - High frequency online transactions
         - Relational and NOSQL DBs
         - Cache for in mem databases like redis
         - Distributed file system
         - **Example I, D or H1 family**

2. EC 2 Launch Types
   1. On demand
   2. Reserved (From 1 to 3 yrs)
       - Standard
       - Scheduled
       - Convertible (can change EC2 types)
   3. Spot instances  (less cost)
       - Batch processing
       - Image processing
       - One time JOBS 
       - JOBS with flexible start and end time
   4. Dedicated host
       - Full dedicated machine
       - Can give you full complice
       - Dedicated H/W with full access
       - Control over softwares
       - Higher cost
   5. Dedicated instance
       - Dedicated machine
       - But no access to h/w
       - Useful in cases where high level compliance needs are in place i.e. not sharing h/w with any other aws account

3. IPs
   1. Private
   2. Public
   3. Elastic (charchable and used mostly for redirecting traffic to required instance on failure)

4. Placement groups
   1. Controls placements for EC2
   2. Startegies
       - Cluster - grouped with Low latency h/w, within single AZ
       - Spread - Spread across different H/w, spread across different AZ (critical apps)
       - Partitions - Similar to spread but spread across partitions - racks. They are still separated rack wise within same AZs, so low latencies.
5. ENI

6. Hibernate state

7. Advance topics
   1. EC2 Nitro - next gen aws technology for virtualization. Better networking and very high IOPS EBS volumes
   2. VCPUs 
         - AWS provided physical CPUs to machines called as core but it also runs multiple thread on each of them and try to add compute power. 
         - Example - ec2 has 2 cores running 2 main thread each so vcpus are 4
         - It is very important to tweak number of main thread (vcpus) per core according to app so that one can save cost
         - Better the judgement on exact core # and vcpu# better the cost saving
   3. Capacity reservation - one can specify the required capacity of ec2 in terms of required nums,total ram, space etc. So that it will be available when needed

---

#### Security Groups
1. Classic Ports
    - SHH - 22
    - FTP - 21
    - SFTP - 22
    - HTTP - 80
    - HTTPS - 443
    - RDP - 3389

---

#### EC2 storage

1. EC2 instance store (**ephemeral storage**)
    - EBS volume has limited perf as in the end they are network drive
    - if you need high IOPs ec2 instance store is an option which are disk drives mounted on physical server
    - Data will get lost on stop. So backing it up is the users responsibility
    - ideal if you need high IOPs for use case like temp storage, cache
    - This used to be default storage when one create ec2, but now its not and EBS took that position
    - Problems
       - Non persistant
       - Not reliable
2. EBS
   - Kind of USB stick which we can attach to ec2
   - These are network drives/volumes
   - we can detach and attach to instances but they **are locked to AZs**
   - Taking der snapshot and migrating to other AZs or regions is possible
   - ***EBS volume types***
      - Cold HDD
         - When low cost is needed
         - Data is rarely accessed

      - Throughput optimized HDD
         - Where we need to process huge data
         - Big data, data wearehousing

      - Provisioned IOPS SSD
         - Very high IOPS
         - High performance 
         - High cost

      - General purpose SSD
         - Balance IOPS/Perf and cost effective
   - Problems with EC2
      - You cant have shared EBS for multiple EC2
      - You cant have EBS in one AZ and EC2 at other AZ 
  
---

#### AMI 
   - customization of ec2 instance while launching
   - we can pre-package our ec2 instance
   - can create our own or pick from market place to save some time

