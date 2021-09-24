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
---

#### Security Groups
1. Classic Ports
    - SHH - 22
    - FTP - 21
    - SFTP - 22
    - HTTP - 80
    - HTTPS - 443
    - RDP - 3389
