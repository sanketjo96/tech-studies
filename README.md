# AWS

#### EC2 Basics

1. General purpose
    - Balence between compute, networking and memory
    - Ideal for code repo or web servers
    - Example T family
2. Compute optimize
    - Used for compute intensive workloads like
      - Batch processing
      - Media transcoding
      - High perf web servers
      - Scientific data modeling 
      - Gaming 
      - Example C family
3. Memory optimized
    - Useful for processing large data in memory
      - High perf relational/non relational DBs
      - In mem DBs for BI
      - Real time processing apps
      - Example R family
4. Storage optimized
    - Use cases where there are high sequential read and writes to local storage
      - High frequency online transactions
      - Relational and NOSQL DBs
      - Cache for in mem databases like redis
      - Distributed file system
      - Example I, D or H1 family

#### Launch Types
1. On demand
2. Reserved (From 1 to 3 yrs)
    - Standard
    - Scheduled
    - Covertible (can change EC2 types)
3. Spot instances  (less cost)
    - Batch processing
    - Image processing
    - One time JOBS 
    - JOBS with flexible start and end time
4. Dedicated host
    - Full dedicated machine
    - Can give you full complice
    - Dedicated H/W with full acess
    - Control over softwares
    - Higher cost
5. Dedicated instance
    - Dedicated machine
    - But no acess to h/w
    - Useful in cases where high level complices needs are in place i.e. not sharing h/w with any other aws account

---

#### Security Groups
1. Classic Ports
    - SHH - 22
    - FTP - 21
    - SFTP - 22
    - HTTP - 80
    - HTTPS - 443
    - RDP - 3389
