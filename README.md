# AwsSA
## IAM (global)
- IAM user:- user to access 
- IAM group:- set of users
- IAM policies:- set of permissions to aws services in json format
version,statement(effect,action,resource,principal)
- IAM password policy 
- MFA multifactor authentication for enhanced security for your login
- google authenticator virtual,U2F security key physical
- UI (Managment console,CLI,SDK)
- login to Management console protected by password + MFA(optional)
- login to CLI protected by access key ID,secret access key
- login to SDK protected by access key ID,secret access key
- Cloudshell is an alternative terminal to execute cli commands (regions limited)
- Best practice to get permissions is via IAM role
- IAM Security Tools 
- IAM credential report (account) audit user credentials status in account
- IAM Access Advisor report (user level) audit services accessed by user

## EC2
- OS,Instace Size & config,Storage,Security Group,Bootstrap
- Bootstrap code runs as root user
- instance naming convention instanceclass:generation:size
- https://aws.amazon.com/ec2/instance-types/
- General,Compute Optimized,Memory Optimized
- https://instances.vantage.sh/

##### Security Group
- region specific
- they act as firewall to Ec2 instances
- If application gets timed out,security group issue
- Connection refused its an application issue
- All inbound traffic are blocked by default
- All outbound traffic are allowed

##### EC2 access
- SSH to the box
- EC2 instance connect only amazon linux 2
- Elastic IP is static public IP.It s limited to 5 per account
- Not a great practice to use elastic IP.use DNS instead
- SSH uses public IP for EC2 instance connect
- Placement group (cluster,spread,partition)
- Big Data Job uses cluster placement group 
- Spread placement group 7 per AZ
- Hadoop,Cassandra,Kafka uses partition
- ENI provides Private IP Address,Elastic IP Address,MAC Address.It can be attached or detached to Ec2.used in fail over scenarios
- EC2 hibernate Usecases like long running processes,preserve EBS volume 
- EC2 hibernate no more than 60 days.It should have root EBS volume
- EBS volume is a network drive.Its locked to AZ.To move volume across AZ snapshot should be taken
- root EBS volume deleted on termination by default other EBS volumes are not
- EBS Snapshot Archive moves snapshot to archive tier
- Recycle bin helps setting up rules to restore deleted snapshots from 1 day to 1 year
- Fastest Snapshot Restore no latency
- AMI amazon machine image can be public,custom and market place
- EC2 instance store provides high performance but they are temporary i3 series 100124 random read IOPS and 35000 write IOPS

## LoadBalancing
- Servers that forward traffic to multiple EC2 instance
- Classic Load Balancer (v1 old generation) support HTTP,HTTPS,TCP,SSL(Secure TCP)
- Application Load Balancer (v2 new generation) support HTTP,HTTPS,Websocket
- Network Load Balancer (v2 new generation) support TCP,TLS,UDP
- Gateway LoadBalancer Network layer IP Protocol
### Application Load Balancer
- It s layery7 loadbalancer supporting HTTP & HTTPS
- Routing based on URL,hostname and query string
- great fit for microservices (container based ECS because of port mapping)
- Target groups are EC2 instances with ASG,ECS tasks,lambda functions
- Client's details can be obtained from headers like x-forwarded-for,x-forwarded-port,x-forwarded-proto
### Network Load Balancer
- Layer 4 load balancer
- TCP,UDP traffic to your instances
- handle millions of request with low latency
- supports static IP
### Gateway Load Balancer
- Deploy,scale 3rd party network virtual appliances
- example firewall,intrusion detection and prevention system,deep packet inspection systems,payload manipulation
- tansparent network gateway and load balancer
- uses GENEV protocol on port 6081
## Elastic Load Balancer
- Sticky sessions are implemented using cookies.
- cookies  are of 2 types they are application based and duration based
- custom cookies are generated by cookie they cannot use reserved names like AWSALB,AWSALBAPP,AWSALBTG
- application cookies are generated by loadbalancer with name AWSALBAPP
- duration based cookies are generated by load balancer AWSALB for ALB,AWSELB for CLB
- Cross Zone Load Balancing is enabled by default for Application Load Balancer
- Cross Zone Load Balancing is disable by default for Network Load Balancer and charges are payable
- Cross Zone Load Balancing is disable by default for Classic Load Balancer and charges are not payable
- Server Name Indication loading multiple SSL certificates onto one web server works with Application and network load balancer
- Connection draining for CLB and deregistering delay for ALB,NLB
- default 300s between 1 to 3600s

## AutoScaling Group
- Scale out,Scale in,automatic registering and dregistering from loadbalancer
- Launch template containing AMI + instance type,EC2 user data,EBS vloumes,security group,ssh key pair,IAM roles,network subnet info and load balancer
- Target tracking,simple step up scaling policy,scheduled actions,Predictive scaling
- CPU utilization,RequestCountPerTarget,Average network in/out,any custom metric
- default cool down period is 300s,ASG will not launch or terminate additional instances

## Instantiating the applications quickly
- Use Golden AMI for Ec2 instances and user data scripts for dynamic configurations
- Use both golden AMI and bootstrap data for elastic bean stalk
- Restore snapshot for RDS instances
- Restore snapshot for EBS

## Elastic Bean Stalk
- Components are configuration,version,environments
- create app->upload version->launch enviornment
- Supported platforms are Go,Java,.Net,Node js,PHP,Python,Ruby,Packet builder,Single,Multi docker,Preconfigured
- Webserver tier includes ALB,ASG
- Worker tier ALB,ASG,SQS

## ECS,EKS and Fargate
- Docker is a platform using which we can deploy apps
- Building docker creates docker image which can be stored in public repo called docker hub or private hub called elastic container registry(ECR has public gallery)
- VMs run on hypervisor whereas dockers run on docker daemon.containers share networking
- ECS data vloumes is EFS
- While creating ECS cluster 3 options like fargate,EC2 instances,external instances using ECS anywhere
- ECS service to run and maintain a specified number of instances of a task definition simultaneously in an Amazon ECS cluster
- For automatic scaling we can use ECS service average CPU utilization,ECS service memory utilization,ALB request count per target
- architectures example ECS with eventbridge & SQS
- EKS supports 2 launch modes Fargate and EC2.kubernetes is cloud agnostic
- EKS Pods in worker nodes.
- Managed node groups & self managed node groups(prebuild AMI)
- AWS AppRunner fully managed service to deploy web apps

## RDS (Relational database service)
- Managed service with automated provisioning,OS patching,continous backup,read replica,multi AZ setup,scaling and EBS storage.we cannot SSH
- RDS engines are mariadb,mysql,oracle,postgres,SQL server
- Read replicas are limited to 5.within AZ,cross AZ or region
- Replcation is async
- Replcas can be promoted to main
- Applications update connection string for read replicas
- Use cases are where read workload should not affect the regular workload
- Same region replication traffic is free whereas cross region is payable
- RDS multi AZ provides Disaster Recovery
- one DNS name automatic app failover to standby
- read replicas be setup as multi AZ for diasaster recovery
- 0 downtime from single to multi AZ.Internally snapshot is taken,new DB is restored from snapshot in new AZ and finally sync between the dbs
- Database authentication are of 3 types.they are password authentication,IAM database authentication and Kerberos authentication
- RDS custom for Microsoft SQL server and oracle access to underlying OS,can do SSH access
### Amazon Aurora 
- proprietary technology and compatible drivers 
- aws cloud optimized 5X performance improvement over MySQL 3x performance improvement over postgres
- It s 20% cost more and has features High availability,15 replicas
- 

### Elasticache
- Managed redis or memcached
- in memory databases with low latency and high performance
- reduce load off of databases of read intensive workloads
- helps make your application stateless
- using elasticache needs heavy application code changes
- Acts as db cache code changes for cache hit,cache write and invalidation 
- Acts as user session store
- Rediscache has multi AZ,read replicas have high availability,back and restore just like RDS underlying concept set and sorted set
- MemCached has multi node for partitioning of data,No high availability,non persistent,No backup and restore, underlying concept is sharding
- IAM authentication for Redis only.Redis auth includes password token and inflight SSL encryption 
- Memcached uses SASL
- Lazy loading,Write through and session store(TTL)
- Redis sorted sets provides both uniqueness and ordering.usecase is gaming leaderboard
-
## S3
- Storage,archive,Application hosting,media hosting,diaster recovery,data lakes,analytics
- S3 stores files in buckets.Files are called objects
- Bucket name should be globally unique name.It should not have uppercase,underscore.It is 3 - 63 characters long.not an ip.must not start with prefix xn.must not send with suffix s3alias.
- Objects have key.The key is entire path.no concepts of directories within buckets 
- Max file size 5TB .Files greater than 5 GB should use multipart upload
- 

## Lambda
- virtual functions
- It supports nost languages like java,nodejs,python,go lang
- lambda container image
- 1 million requests are free and it s charged 0.02$ per request there after
- 400,000 GB-seconds compute time per month free and charged 1$
- lambda execution time limit 15 mins,memory limit 128 MB to 10GB
- environment variables should be of 4KB
- concurrency executions are 1000 can be increased if requested
- cloudfront functions in js and lambda @ edge available in other langauages
- cloudfront functions s free and exection time < 1ms ,usecase cache customizaton,url rewrites
- rds proxy for maintaining pool of functions for lambdas to access rds


## Dynamodb
- DAX helps read congestion by caching.no application code changes required
- Dynamo db streams cross region replication and global table
- kinesis data streams are newer.in this case kinesis fire house
- TTL time to live automatically deletes after expiry
- Continous backups(35 days) and On demand backups will available
- export to s3 works for point in time recovery


## Other serverless 
- API gateway HTTP,rest and websocket types
- API gateway edge optimized,regional and private
- Authentication using IAM roles,cognito
- custom domain name HTTPS using ACM and R53 record
- Step functions build serverless visual workflows to orchestrate lambda functions
- possibility of human intervention.usecases are order fulfilment,data processing,web applications,
- Cognito give users an identity to interact with web and mobile
- Cognito User pools integrate with API GW and ALB.Its a serverless database with username & password.It can be integrated with federated identities (facebook,google)
- Cognito Identity pools users gets temporary AWS credentials


## Databases
- RDS supports RDBMS usecases are perform sql queries,transactions
- Aurora has modules like aurora serverless,aurora multi master,aurora global,aurora machine learning,aurora db cloning
- elasticache manged redis,memcached.requires code changes at application level.usecases are Key/value store,Frequent reads,less writes,cache results for dbqueries,store session data for websites,cannot use SQL
- Dynamodb is managed Nosql databse,DAX cluster for read cache,usecases small documents 100KB
- Neptune graph dataset usecase social media 
- DocumentDB aws managed mongodb 
- keyspaces aws managed cassandra
- QLDB qudric ledger financial transactions DB.records are immutable.decentralized 
- Timestream timeseries database 

## Data & Analytics
- Athena is serverless query engine on columnar data
- redshift is based on postgreSQL.Has indexes .faster queries and results
- redshift supports multi AZ mode.Cluster snapshots can be taken from region and copied to another
- Data can be loaded using kinesis firre house,from s3,using Ec2 instance
- Opensearch successor of elastisearch.can search data across columns irrespective of primary key.
- Open search patterns using dynamodb streams,kinesis data streams & kinesis firhouse
- EMR helps to create hadoop clusters.comes with apache spark,flink,hbase,presto
- Quicksight is ML based BI service providing dashboard
- Glue is serverless ETL service.convert data to parquet format
- Glue data catalog run datalog crawlers to get data from various data sources 
- Glue job bookmarks prevents from reprocessing
- Glue Studio provides GUI
- Glue elastic views leverages materialized view
- Glue databrew clean and normalize data 
- aws lake central place of data for analysis.built on top of glue
- Flink application in java,scala to process and analyze data.read MSK and kinesis data streams
- kafka is alternative for kinesis.MSK fully manages kafka.has serverless version too



## Machine Learning
- Rekognition helps in image regonition,labelling
- Transcribe converts speech to text.features like automatic speech recognition,redaction(removess PII info),automatic language audio
- Polly converts text to speech.features like pronunciation lexicons and speech synthesis Markup language enables more customization
- Translate helps in translating from one language to another
- lex widely used in alexa.helps in automatic speech recognition,building chatbots
- connect is used in contact centres.recieve calls,contact flows.virtual call center
- comprehend is NLP based fully managed serverless service.analyse customer mails and create group articles
- comprehend medical that extracts clinical information
- sagemaker is to develop ML models
- Kendra is ML based document search engine 
- Personalize fully managed ML service with real time recommendations
- textract extracts text,handwriting,data from scanned documents
- forcasts fully managed service to provide highly accurate forecast.usecases like financial planning,product planning,resource planning











