# AWS SA certification notes

### EC2 (main)
1. Types
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

2. Launch Configs
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
       - Can give you full compliance 
       - Dedicated H/W with full access
       - Control over softwares
       - Higher cost
   5. Dedicated instance
       - Dedicated machine
       - But no access to h/w
       - Useful in cases where high level compliance needs are in place i.e. not sharing h/w with any other aws account

### EC2 storage (main)
1. EC2 instance store (**ephemeral storage**)
    - EBS volume has limited perf as in the end they are network drive
    - if you need high IOPs ec2 instance store is an option which are disk drives mounted on physical server
    - Data will get lost on stop. So backing it up is the users responsibility
    - ideal if you need high IOPs for use case like temp storage, cache
    - This used to be default storage when one create ec2, but now its not and EBS took that position
    - Problems
       - Non persistent
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
         - Big data, data warehousing
      - Provisioned IOPS SSD
         - Very high IOPS
         - High performance 
         - High cost 
      - General purpose SSD
         - Balance IOPS/Perf and cost effective
   - Problems with EC2
      - You cant have shared EBS for multiple EC2
      - You cant have EBS in one AZ and EC2 at other AZ
   - **Solution**
      - EBS muti attach feature
      - Limitation 
         - Your instance needs to be [nitro](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances) (nitro is advanced hardware)
         - Your volume type needs to be **Provisioned IOPS SSD** which is expensive
      - EFS - ELASTIC FILE SYSTEM Preferred!!!
         - Its a common network drive
         - Its a different service (unlike EBS)
         - It makes file system available to multiple ec2 instances with using [mount targets](https://miro.com/app/board/uXjVOibrNh4=/?moveToViewport=-436,-397,1874,818&embedId=446201865057)
         - Smart options
            - Can select storage classes like standard (replicate data across zone), One-Zone (no replication)
            - You can opt in feature like IA where you can save money by marking file system Infrequent access (IA)
         - Limitation
            - This is good across AZs but not across regions
         - Practical gotchas
            - Install nfs-utils on instances as EFS is NFS based system
            - Create common folder on ec2
            - Mount common folder to EFS by running mount command
            
### Load balancer
   - Classic load balancer
      - Supports all protocols HTTP,HTTPS,TCP,UDP
      - Expensive and less performant
      - EC2 attached directly to load balancer
   - Application load balancer
      - Layer 7 protocols - HTTP, HTTPS etc
      - Cost optimized and fast
      - Ec2 needs to be added first in target group and then TG connects to load balancer
      - **Target group and listners**
         - This is difference from classic load balancer
         - Its just a logical grouping of servers for creating ideal load balancing
         - One need to add Listener on ALB through which user requests forwarded to target groups (based on paths, ports, user IPs etc.)
   - Network load balancer
      - Layer 4 protocol - TCP, UDP,TLS
      - Its similar to ALB, the difference lies in performance, its way faster and used when you need to support millions req/sec

   - ASG - (auto scaling group)
      - What if instances went down or terminated due to error or reached to mem threshold
      - Its an entity to add/remove instance without manual intervention (a chef whop creates dishes)
      - It provides high availability
      - You need to provide recipe to take care of auto scaling - ec2 launch template is that recipe for **a chef ASG**
      - Now its responsibility of chef to maintain good cooking quality i.e maintain number of instances (min, max desired)
      - Scaling policy
         - Manual Scaling
         - Dynamic scaling
            - Simple scaling 
               - **Example - If CPU > 70% add 1 instance, if CPU < 20% remove 1 instance**
               - Limit: Not very flexible
            - Step scaling
               - Increase or decrease desired capacity based on set of adjustments
               - **Example - If 50% < CPU < 60% - add1, If 70% < CPU < 60% - add2, If 80% < CPU < 90% - add3**
      <img src="https://user-images.githubusercontent.com/31438283/182691970-82cac0f9-4a54-4877-9b24-c1df92e1c5f3.png" width="300" height="200">
      

### Virtual private cloud
- Components of VPC
   - **VPC** - is a regional entity, which is ones own private network  
   - **Subnet** - Within VPC we have sub groups called subnets and its AZ level
   - **Public and Private subnet** - Public subnets holds front facing components (UI server) and private subnets are to have your private components (DBs etc.) 
   - **Implicit router** - This drives communication across subnets
   - **Internet gateway** - A main access point through which public internet enters to your VPC
   - **Routing table** - Servers within public subnets will never directly connect to internet gateway they need to refer routing tables
   - **NAT Gateway** - Private servers will never get internet directly, rather they will request NAT gateway resides in Public subnet to get internet
   <img width="300" height="200" alt="image" src="https://user-images.githubusercontent.com/31438283/182776631-1cd10eae-a466-4c6e-8bff-ad4790d815a9.png">
- Details
   - By default we will get default VPC in all regions
   - Creating VPC
      - CIDR needs to provide
      - Tenancy - option to select where to run VPC servers on - dedicated h/w or not (dedicated = high perf and cost)

### Firewalls
   - **Security Group** 
      - Instance level firewall and one has to allow trafic through this
      - Stateful - When there is inbound request, SG remembers corresponding outbound and doesn't evaluate it for security
      - You can just allow target traffic but explicit denial is not possible. what you don't allow is denial by default
         - What if you want allow 1-2000 ips but not 444b which targeting attack to ur app
         - We need to add 2 rules
            - 1-443 - allow rule 1
            - 445-2000 - allow rule 2
   - **NACL** 
      - Like security group, subnet do have firewall which is NACL
      - Stateless - Evaluates inbound and outbound
      - Explicit denial is possible

### S3 (main)
   - Simple storage service, Public service
   - Service is global but you have to create buckets (container) at regional level
   - Bucket name must be globally unique
   - Object storage unlike block storage like EBS, cant be attached with EC2
      - Object key - filename
      - Object value  - file data
      - No matter what - even you create hierarchy of folder > file etc, the data stored in key value way
      - Security (3 layers)
         - Bucket level 
         - Object level
            - ACL (outdated approach)
               - Old way
               - Limited control
            - Bucket policy (through IAM)
               - Preferred way
               - Granual  
   - Versioning
      -  Bucket versioning enables to save versions of content with same key/filename3
      -  Prevents accidental deletes
   -  S3 replication
      -  Default
         -  Within same region
         -  Replication across AZ
         -  No extra changes
      -  Cross Region - Replicate data across region
      -  Cross Account - Replication from one account to other account
   -  Storage classes 
      -  AWS decided discounts on s3 buckets bills based on storage classes.
      - Classes 
         -  Standard - Frequently access data and results in ms, No min storage duration
         -  Standard IA - Infrequent access (once in month) results in ms, (need to save data for min 30 days duration)
         -  One zone - Infrequent access but data stored in single AZ (so less disaster coverage). Can store re-creatable and IA data (min 30 days duration)
         -  Glacier instant retrieval - Long-lived archived data with access may be once in qurater
         -  Intelligent tiering - Highest cost but fits all access patterns of data and it automatically fits to use case
      -  Lifecycle rules on bucket allows us to move stored objects across different classes

   - Performance
      - Multipart data upload
      - Transfer from edge locations
   - Security
      - Encryption
         - Server side
            - Managed by s3
            - Managed by KMS 
         - Client side 

### Database (main)
   - RDS
      - RDS vs EC2
         - Its possible to install any RDBMS on ec2 and work with it but then we need to manage it (security, perf tweaks, version update)
         - However, aws manages the RDS service (but will allow only latest DB versions)
      - Setups
         - Single instance 
            - Less coverage from failover 
         - Multi AZ DB setup 
            - Better coverage from failover
            - Needs frequent sync replication to maintain standby copy
            - Standby can only be used for read purpose, it does not accept write from outside (for replication its supports write)
            - This setup is highly available (HA) but not a fault tolerant (FT), 
               - **FT** means handling failure without disruption in no time
               - **HA** means being available without downtime - (even you are on standup still application is running with some capability)
            - You are not getting any performance benefit of having standby along with main instance 
            - But we can prevent some perf impact - say you need to take a backup from DB file when application is live, this can cause perf issues as your are doing it when ppl are using app. You can take backup from standby in such case to ensure smooth app experience 
            <img width="300" height="200" alt="image" src="https://user-images.githubusercontent.com/31438283/184273660-df445814-78db-4ef7-835f-d3513952fb97.png">
         - Multi AZ DB cluster
            - Multiple standby DBs
            - This prvides addfitional perf benifits as your app now can have 2 servers for read operation and one for write 
         - Multi read replicas setup
            - Read replicas are duplicates of your primnary DB
            - Up to 5 are possible (in contrast of just standby DB) 
            - Multiple read replica to provide even more HA 
            - Can have across region 
            - The only thing is data sync is asynchronus so bit lagish (not as quick as standby)
            - We may use these replicas for read operation ffor reporting and analytics (may be not on app if its need realtime reads)
             <img width="300" height="200" alt="image" src="https://user-images.githubusercontent.com/31438283/184948898-27e90f4f-8233-4ff3-aad4-bbf499155087.png">
          - **Amezon arrora**
            - Its a new database engine
            - **Can use same DB understanding to apply here (MySQL and PostgreSQL compatible)**
            - **We can have multiple master**
               - Earlier we used to have single master to support both read and write
            - **Global DB feature** - we can have instannces spanning across globe for better performance
            - **Parallel query**
            - **Serverless** - No need to configure machine, it will managed automatically by aws
            - Suppprts 15 read replicas
         - Dynamo DB
            - NOSql DB 
            - Useful for application which needs scalablity
            - Serverless no need to select server confs
            - RCU and WCU calculations can be done while creation and assigne them as needed
               - RCU - read capacity unit
               - WCU - read capacity unit 
            - Indexing
               - Local secondary index
                  - Can be created only at creation of table
                  - Allows you to define only one sort key 
                  - RCU and WCU will be shared with index
               - Global secondary index
                  - Can be created while creating or even after creation
                  - Basically its a new table in memory to boost perf of your query
                  - Allows to add new partition key and sort key
                  - You can assign RCU and WCU values
            - Global table
               - With global table you basically can create replica 
               - It happens with help of DynamoDB stream, so anything you add anything to table gets replicated to replica
      - Elastic cache
         - Its in memory database with redis/memcache 
         - Work as perf optimizer for DBs, usecase - Storing session data
      - Redshift
         - Amazon Redshift is a data warehouse product which forms part of the larger cloud-computing platform 
         - Its basically query optimizer where master accepts your complex query gets devided to chunks and send across multiple nodes 
         - Those multiple nodes process query and return data to master
         - Master is responsible for data management and return to client
         - Used for OLAP 
         <img width="300" height="200" alt="image" src="https://user-images.githubusercontent.com/31438283/185526160-18882a55-1563-498e-9ecf-c95e8722809e.png">

### Cloud formation
   - Automated and programatic way to create your infra (ec2, s3, etc..)
   - Once dev team complete there work, devops team lookup the resourses list from devs and create CF template to deploy at production

### SQS
   <img width="377" alt="image" src="https://user-images.githubusercontent.com/31438283/185536919-e51663e0-0183-4583-88bd-961724ecc3de.png">
### SNS
   - It is a notification service which are helpful for sending emails, sms etc.
   - But on top of all this you can creare pub-sub pattern which will help your arcitecture 

### SES
   - It used to send email programatically

### IAM (main)
   - Identity and acess management
   - Root user vs IAM users -  root user will have all access. Other IAM users are deemed users
   - IAM user creation
      - TWo access types and one need to choose this during IAM user creation
         - Programatic access - AWA API, CLI, SDK
         - Console access  - Console UI
      - Policies
         - Specific access permissions to use aws services
         - One can add/attach these Policies to user to have restricted access
         - We can create custom policies as well
         - Types of policy and weights
            - By default everything is deny  (low)
            - **Explicit Allow** (mid)
            - **Explicit deny** (highest)
      - IAM Group
         - If you need to assign some set of policies to multiple user better to create policy group called IAM group
         - And assign users to the created group 
      - IAM role
         - For user to access some service we can use group and policies, but what if one service wanted to use another service
         - Here comes role , role is the way to grant or deny access for service who is willing to access some other service
         - This is not only usecase for the role but its a main use of it
         <img src="https://user-images.githubusercontent.com/31438283/186744144-de843d4e-4dbe-4afc-a5aa-62c307fb1f95.png" width="300" height="200">
         
 ### CloudWatch
 - This is to monitor your entire AWS account
 - It can also trigger alarms for example trigger alarm if ec2 cpu utilization > 70%
 - It also allows to attach actions on some triggers which can be useful in order to provide FTA arch. Actiuons like 
   - Sending email
   - EC2 actions like stop ec2 etc.+
   - Adding new server by notifying ASG
 - In order to attach action and react, You can create a alarm basxed on service metric
 - Logs
   - Logs group - we do have log groped by service  

 ### Lambda
 - To run code without server
 - Whenever there is event generation, lambda will trigger and will process things as you need
 <img src="https://user-images.githubusercontent.com/31438283/186752527-9e3e4880-4a7e-4dd6-a36d-1f2d69ee854b.png" width="300" height="200">




#### Insights
- [What is OPS and Throughputs ? ](https://www.youtube.com/watch?v=YD_Lg2lzTYI)
- [What is CIDR Block?](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c02/learn/lecture/13528536#overview)
- [How to write user script for EC2 ?](https://www.javacodegeeks.com/2020/05/how-to-install-apache-web-server-on-ec2-instance-using-user-data-script.html)
- [What is Cross zone load balancing](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/how-elastic-load-balancing-works.html)
         - What if you need to balance traffic across Az A and AZ B, but A have 4 ec2 and b have just 1, AZ b will be overloaded if both AZs get 50% traffic
         - To address this -  LB nodes get created at respective AZs and then its LB node's responsibility to distribute load further.
<img width="300" height="200" alt="image" src="https://user-images.githubusercontent.com/31438283/182283025-999f40a7-4294-4290-bf32-f42f1f751fce.png">
- TBD - What is indexing
- TBD - Launch template vs Launch configuration
- TBD - OLAP
