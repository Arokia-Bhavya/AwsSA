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
## IAM Advanced
- AWS Organization helps to manage many accounts
- consolidated payment method,shared reserved instances & shared savings plan,cross account role
- Organization Unit can have management account,dev member account & QA member account
- Service control policies are IAM policies applied to OU or accounts to restrict users and roles
- SCP has blocklist and allowlist.child account OU scp inherits from root account OU scp
- aws:SourceIP restrcits client IP from making API calls
- aws:Requestedregion restrict the region the API calls are making to client
- aws:ec2Resource Tag resource based on tags
- aws:multiFactorAuthPresent boolean to check
- aws s3 policies list bucket will have bucket level permission
- get,put will have object level permissions
- resource policies can have principalOrgId
- accountA accessing S3 bucket in account B in 2 ways cross account role and bucket policy
- resource based policies are preferred since assigning role gives away all permissions
- For an eventbridge rule to execute for lambda,S3,SNS,SQS resourced based policies s required whereas for kinesis IAM role s required
- IAM permission boundaries are for roles and users(not groups)
- Explixit Deny takes precedence
- SCP->resource based->Identity based->IAM permission boundary->session policy(STS)
- AWS identity centre is AWS SSO.one login to multiple AWS
- users can be IAM identity centre built in identity or 3rd party active directory like OKTA,OneLogin
- In management account,create roles in IAM identity centre and it can be associated with OU
- Fine grained permissions and assignments provided by IAM identity centre
- AWS directory services has 3 flavours like simple AD,AD Connector uses proxy and AWS Managed Microsoft AD establish trust connections with on-prem AD
- AWS control tower is to setup and govern a secure and multi account AWS environment
- Guardrails are of 2 types namely preventive guardrail using SCPs,detective guardrail using AWS Config

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
- Launch configuration is no more supported by AWS.launch template is better than launch configuration due to multi version support.
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

## Messaging
- SQS queue model,SNS Pub Sub,Kinesis real time streaming model
- SQS aws oldest offering used to decouple applications
- retention of messages 4 to 14 days.message size 256 Kb.low latency < 10 ms
- can have duplicate messages and out of order messages.unlimted throughput
- consumers can run on ec2 instances,lambda
- consumers once done with processing the message and delete them.consumers can be integrated with ASG based on queue length cloud watch metric
- inflight encryption using HTTPS and at REST encryption using KMS keys
- Access policy for SQS s there similar to S3
- dead-letter queue (DLQ) is a special type of message queue that temporarily stores messages that a software system cannot process due to errors
- message visibility timeout makes the message invisible for the consumers to recieve.visbility timeout too low leads to duplicates.visibility timeout too high its difficult to reprocess
- Long polling s where consumer waits even when there are no messages in the queue.can be enabled at queue level or API level using waittimeseconds
- FIFO queue limited throughput 300 msg/s without batching 3000 msgs with batching.exactly once send capbility.ordering of msgs maintained
- SNS one message multiple recievers.SNS FIFO topic only SQS subscription wheres standard topic supports other subscription protocols like SQS,lambda,mobile app,HTTP,SMS,EMail
- Topic publish API create a topic,subscription and publish to the topic.direct publish for mobile apps
- SNS securtiy inflight encryption using HTTPS api and at rest encryption using KMS keys
- SNS + muliple SQS queues,SNS + Kinesis Data Firehouse, .subscription based on Message filtering 
- Apps using RabbitMQ and ActiveMQ can be migrated to AWS using Amazon MQ.traditional protocols like AMQP.not scalable like SQS or SNS 
- Kinesis data streams uses shard based.messages having same partition key goes to same shard
- retention is 1 to 365 days.message once inserted cannot be deleted
- for producers we can use sdk,kinesis producer library and kinesis agent
- for consumers we can use sdk,kinesis client library and kinesis agent
- provisioned mode and on demand mode
- kinesis data for house can streams data to S2,redshift,opensearch.fully managed.near realtime
- for ordering partition key in kinesis and group id in FIFO
- 20 trucks per shard.max amount of consumers in parallel is 5
- FIFO queue uses max 100 group id

## ECS,EKS and Fargate
- Docker is a platform using which we can deploy apps
- Building docker creates docker image which can be stored in public repo called docker hub or private hub called elastic container registry(ECR has public gallery)
- VMs run on hypervisor whereas dockers run on docker daemon.containers share networking
- ECS data vloumes is EFS
- While creating ECS cluster 3 options like fargate,EC2 instances,external instances using ECS anywhere
- ECS service to run and maintain a specified number of instances of a task definition simultaneously in an Amazon ECS cluster
- For automatic scaling we can use ECS service average CPU utilization,ECS service memory utilization,ALB request count per target
- ECS with EC2 launch type is charged based on EC2 instances and EBS volumes used. ECS with Fargate launch type is charged based on vCPU and memory resources that the containerized application requests
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

## R53
- AWS Fully managed DNS
- Each Record contains domain,record type,routing policy,value,TTL
- record types are A(IPV4),AAAA(IPV6),CNAME(hostname to hostname),NS(name servers of hosted zone)
- container for records that define how to route traffic.public and private hosted zone
- creating the domain in AWS will create hosted zone.user has to create records in hosted zone
- High TTL 24 hr Low TTL 60 secs.mandatory for all records except alias
- CName record maps hostname with another hostname (works for non root domain)
- Alias record maps hostname to resource (works for both root and non root domain)
- Simple routing policy with multi values randomly redirects to the urls provided
- weighted routing policy redirects to the urls provided based on the percentage
- latency routing policy redirects to the resource  with least latency
- R53 health checks are for public resources they may be another endpoint,monitor other health checks and monitor cloud watch alarm
- R53 health checkers are outside VPC.they cant access private endpoints.you can create a cloud watch metric and associate cloud watch alarm to it
- Failover routing policy redirects to secondary when primary s down 
- geoproximity routing policy ability to shift more traffic to resource based on the defined bias
- IP based routing policy based on client's IP address.
- multi value routing policy is not a substitute of ELB
- If you buy domain name from 3rd party still use them as DNS service provider
## S3
- Storage,archive,Application hosting,media hosting,diaster recovery,data lakes,analytics
- S3 stores files in buckets.Files are called objects
- Bucket name should be globally unique name.It should not have uppercase,underscore.It is 3 - 63 characters long.not an ip.must not start with prefix xn.must not send with suffix s3alias.
- Objects have key.The key is entire path.no concepts of directories within buckets 
- Max file size 5TB .Files greater than 5 GB should use multipart upload
- User based policy IAM
- Resource based policy - bucket policy,object access control,bucket access control
- Bucket policy are json based and has the same syntax as IAM policy
- Bucket settings are enabled for preventing data leaks
- we can host static website using S3.enabling static website host will ask you to create index.html and all the bucket contents as public
- versioning of the files is at bucket level.Any file that is not versioned prior to enabling version will be have version as null
- Delete just adds delete marker only when permanently delete the object gets deleted
- Replication are of 2 types cross region replication and same region replication.Versioning has to be enabled as copying s done async
- After replication,only new objects gets replicated,for existing it s S3 batch replication
- replication can replicate only delete marker not permanently delete
- S3 standard used for frequent access 
- S3 standard IA is for infrequent access cost less than standard.use case Diaster Recover and backup.minimum storage 30 days
- S3 one zone IA infrequent storing secondary backup.minimum storage 30 days.cost effective than S3 standard IA
- S3 Glacier low cost storage for archiving 
- S3 Glacier instant retrieval is for ms retrievalminimum storage 90 days
- S3 Glacier Flexible retrieval expedited ( 1 to 5 mins),standard(3 to 5 hrs),bulk(5 to 12hrs).minimum storage 90 days
- S3 Glacier Deep archive for long term storage.standard(12 hrs) bulk (48 hrs).minimum storage 180 days
- S3 intelligent tiering moves objects based on usage
- S3 lifecycle are rules that helps to move objects between storage classes based on configured conditions
- Transition actions are rules for moving and expiration actions are rules for deletion.u can give both current and non current versions
- While transitioning minimum 30 days duration has to be met by storage class.
- S3 analytics for analysing the storage class usage with suggestions
- S3 requester pays networking cost.requester must be IAM user so that billed accordingly
- S3 event notifications need IAM persmissions
- S3 event notifications with event bridge helps to send notifications to many
- S3 transfer accleration uses edge locations to speed up transfer upload.compatible with multipart
- S3 byte range fetches can be used to increase downloads.can be used in retrieving partial data
- S3 select and Glacier select retrieves less data using SQL 
- S3 batch operations have numerous usecases like encyrpt unencrypted objects
- S3 inventory to get object listand use S3 to filter
- S3 encryption has 4 types namely SSE-S3,SSE-KMS,SSE-C and client side encryption
- SSE-S3 header set for AES-256
- SSE-KMS keys manged by KMS limitation due to KMS quota
- SSE-C key provided by the customer.It will never be stored in AWS.https is used
- encryption at flight is done by HTTPS endpoint.can be forced by securetransport of bucket policy
- Default encryption is SSE-S3.It can be set via bucket policies using explicit deny when the key string not equals.Pleas note bucket policies are evaluated before default encyrption
- CORS cross origin resource sharing
- if a client requests files from other origin,CORS headers has to be set
- MFA delete uses forces to generate code on MFA hardware device.It will be required for permanent deletion and suspend versioning
- to enable MFA delete,versioning has to be enabled on the bucketand only root account can do that and through CLI
- S3 access logs ,we can enable access logging and configure target bucket
- S3 presigned URL generates URL only for accessing the file irrespective of permissions.url will get expire via console 1 min to 72o min.CLI 
- Glacier vault locking write once read many.data rention and compliance
- S3 object lock rention mode compliance and rention mode governance.legal hold protects the object indefinitely
- S3 Access points we can define access permissions for the bucket.each access point will have its own dns path
- for lambda functions to change the object we need not create another s3 bucket .we can use S3 access point and S3 object lambda access point

## CloudFront
- Content Delivery Network improves read performance,content is cached at the edge
- 216 point of prescence globally
- DDoS protection (shield,AWS web application firewall)
- custom origin like ALB,EC2 instance,S3,any HTTP backend
- cloudfront works with EC2 or ALB public
- cloudfront works with S3 with public bucket policy
- cloudfront works for static content where as S3 cross region replication works for dynamic content
- AWS global accelerator uses unicast and anycast IP to support global access irrespective of the region application is deployed
- cloudfront caches at edge locations .it works for specific TTL.inorder to reflect changes immediately cache invalidation s done
- cache can invalidated by given file path
## AWS Storage Extra
- Snow Family are offline devices to perform data migrations.more than a week use snowball devices
- client->AWS Snowball->AWS snowball->import/export->S3 (physical route not network)
- Snowball edge storage optimised 80 TB of HDD capacity snowball edge compute optimised 42 TB of HDD
- Snowcone 8TB HDD Snowcone SSD 14TB .data sync agaent preinstalled
- Snow mobile is the AWS tractor for large data like exabytes
- we setup snowballedge/snow cone at edge location
- all can run EC2 instances or lambda functions
- Historically snow family devices can be used using CLI now AWS OpsHub
- cannot use snowball to glacier directly.have to use S3 first and S3 lifecycle policy
- launch 3rd party high performance file systems on AWS
- FSx lustre (linux+cluster).distributed file system for large scale computing
- this s used for machine learning,high performance computing
- scratch file system used for short term processing,optimse costs.
- persistent file system used for long term processing,sensitive data
- FSx for NetApp ONTAP file system compatible with NFS,SMB,iSCSI protocol
- Point in time instaneous cloning helpful for testing new workloads
- FSx for OpenZFS compatible with NFS.move workloads to AWS using ZFS
- bridge between on premises data and AWS cloud data is storage gateway.4 types
- Server->(NFS,SMB)->S3 File Gateway -> S3 standard,S3 standard IA,S3 one zone IA,S3 intelligent tiering->(lifecycle policy)->Glacier
- FSx file gateway provides local cache for frequently accessed data.useful for group file shares
- Volume gateway block storage using iSCSI protocol backed by S3
- cached volumes low latency access to most recent data
- stored volumes entire dataset is on premise
- Tape gateway virtual tape library backed by S3 glacier
- Storage hardware appliance works with File Gateway,Voulme gateway,Tape gateway
- transfer family is fully managed service to transfer data to S3 or EFS using FTP protocol
- supported protocols are SFTP,FTP,FTPS
- Data sync to move large amount of data onpremises to cloud using agent,from aws service to aws service without agent
- File permissions and metadata are preserved (NFS POSIX,SMB)
- NFS/SMB server->datasync agent->datasync->AWS service

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
- redshift spectrum
- Data can be loaded using kinesis fire house,from s3,using Ec2 instance
- Opensearch successor of elastisearch.can search data across columns irrespective of primary key that is partial matches.
- Opensearch can complement another database.can run on 2 modes managed or serverless
- Open search patterns using dynamodb streams,kinesis data streams & kinesis firehouse
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

## Cloudwatch,Cloudtrail and Config
- Cloudwatch logs are not pushed from EC2 by default.either cloudwatch agent is installed on premises or in AWS
- Amazon event bridge is earlier called as cloudwatch events
- cron job,event pattern,trigger lambda functions,send SQS/SNS messages
- archive events and sent to event bus
- schema registry allows to you to generate code for your application
- event bridge resource based policy aggregate all events of aws orgnization to single aws account or aws region
- cloudwatch container insights collect,aggregate,summarize metrics and logs for ECS,EKS,Fargate
- cloudwatch lambda insights collect,aggregate,summarize metrics and logs for lambda
- cloudwatch contributor insights for contributor data
- cloudwatch application insights provides automated dashboard showing potential problems in application
- cloudtrail provides governance,comliance and audit
- management event,data events,cloudtrail insight events
- enable cloud trail insights to detect unusual activity in account like inaccurate provisiong,service limits,burst of IAM actions
- events can be stored in cloudtrail only for 90 days after that it can be moved to S3 and analyzed using athena
- API logs->cloudtrail->eventbridge->SNS alerts
- AWS Config Helps with auditing and record compliance of aws resources
- Config Rules - remidations automate noncompliant resources using SSM automation
- we can set remediation retries
- we can use eventbridge or SNS to trigger notifications
- both aws managed and custom rule can be configured
## Security
- InFlight encryption is done by SSL certificates.encrypted before sending and descrypted after recieving
- Server side encryption is Encryption at rest by server
- Client side encryption envelope encryption.encryption decryption happens at client side
- KMS can manage keys and easily integrate with other AWS services
- KMS keys are of 2 types symmetric(AES-256) and asymetric(RSA & ECC)
- KMS keys either of aws owned,aws managed.keys has to be rotated after 1 year
- They are locked per region.KMS key policy is of 2 types default KMS key policy access by root user
- KMS multi region are not global.it s managed independently
- custom KMS key policy defines users,roles to access KMS key.useful for cross account access
- custom key stores are cloud HSM
- SSM parameter is secure storage of secrets,version tracking and serverless
- we can store hierachy wise as well.assign TTL for advanced parameters
- SSM Parameter types are of 3 types string,stringlist and secure string
- AWS CloudHSM lets you manage and access your keys on FIPS-validated hardware, protected with customer-owned, single-tenant HSM instances that run in your own Virtual Private Cloud (VPC).
- AWS secrets manager for storing and rotation of secrets.supports multi region secrets
- ACM helps manage TLS certificates.to request public certificates domain names has to be included,automatic renewal of 90 days,validation method of certificates
- importing of a certificate in ACM requires daily expiration events set before 45 days from expiry.AWS config has managed rule for acm-certification-expiration-check
- WAF needs fixed IP hence loadbalancer is used woth global accelerator
- protects web application against common web exploits layer 7.can be deployed in ALB,cloudfront,API gateway
- set of web ACL rules to protect from SQL injections & cross site scripting
- AWS shield protects from DDoS attacks
- Sheild standard is free service can detect attckes using syn/udp pools.
- Shield advance s chargeable,for sophistacted attacks and 24X7 support from DDoS response team.it automatically deploys WAF to detect layer 7 attacks
- Firewall manager Manage rules in AWS orgnaization.common security policy
- WAF for layer7.Shield for DDoS attacks.Firewall manager to set common security rules across organization
- Best practices are using WAF,shield with cloudfront & global accelerator,R53.
- infrastructure layer and web applicationlayer defence
- amaon guarduty to protect aws account usues machine learning algo for detecting unusual events or traffics from logs.good to detect crpto currency attacks
- amazon inspector automated security assesments for Ec2 instances,container images,lambda.send findings to eventbridge.
- package vulnerabilities and network reachability
- Macie is fully managed machine learning to identify and alert about PII information

## VPC
- Virtual Private Cloud is to protect the network from outside
- Subnets are to divide the VPC into private and public.for eg:- db servers in private and web servers in public
- CIDR classless inter domain routing is for defining the IP range(IP and subnet mask)
- Internet Gateway is for connecting from the VPC to internet
- NAT Gateway is for communication between private and public subnet
- soft limit 5 VPCs per region.min size /28 and max size /16 since VPCs are private IP addresses has to be in private range
- private ranges are 10.0.0.0/8 (big networks),172.16.0.0/12(default VPC),192.168.0.0/16(home networks)
- In a subnet,5 IP address are reserved for eg:- 10.0.0.0/24 10.0.0.1(network address),10.0.0.1(default vpc router),10.0.0.2(amazon provided DNS),10.0.0.3(reserved for AWS future use),10.0.0.255(broadcast)
- Bastion host is from public subnet to ssh to private subnet for administrative purposes
- NAT instances from private subnets to connect to internet.must have elastic IP attached to it.NAT instance has to be created from AMI
- NAT Gateway works for the same puporse but differently.Private subnet->NATGW->IGW.It s restricted to one AZ at a time.
- NACL are like firewall to subnets
- NACL rules will take number (1 - 32766) higher precedence with lower number
- Default NACL accepts everything inbound/outbound

## Diaster Recovery
- RPO is recovery point objective .recovering the system to the point before diaster happens
- RTO is recovery time objective.recovering the system to the point after diaster happened
- backup restore is taking backups from time to time and restoring it when diaster happens
- pilot light is light version of the app being brought when diaster happens
- warm standby there s a scaled down but fully functional.when diaster happens scales up immediately
- active active has higher RTO
- Database Migration Service helps to migrate databases to AWS,resilient and self healing
- source db->Ec2 instance running DMS->target db
- when source and target db doesnt have same engine.AWS schema conversion tool is used
- DMS can be deployed in multi AZ
- migration from mysql RDS to aurora by DB snapshot and setting up aurora replica
- external RDS  to aurora through percona dump and mysqldump utility
- we can use DMS if both databases are up
- migration from postgres RDS to aurora by DB snapshot and setting up aurora replica
- external RDS  to aurora through backup and aws_s3 extension
- Onpremise data migration using AWS
- ability to export/import .iso format,vm export/import,aws directory service and aws data migration service,aws server migration service(incremental replication)
- AWS backup fully managed service for manage and automate backups across AWS services
- AWS backup vault lock.write once read many.cannot be deleted as written using vault lock
- AWS directory service plans migration service either through agentless discovery service or through agent based discovery
- Resulting data can be viewed with AWS migration hub
- AWS migration service is the simplest way to migrate application.lift shift application.reduced downtime
- Transferring large dataset via site to site VPN(185d),over direct connect(18.5d),over snowball(1 wk),ongoing  replication transfers
- Vmware cloud on AWS helps to migrate the vmware based workloads to AWS,have a disaster recoverage
- AWS DataSync is a secure, online service that automates and accelerates moving data between on premises and AWS Storage services. DataSync can copy data between Network File System (NFS) shares, Server Message Block (SMB) shares, Hadoop Distributed File Systems (HDFS), self-managed object storage, AWS Snowcone, Amazon Simple Storage Service (Amazon S3) buckets, Amazon Elastic File System (Amazon EFS) file systems, Amazon FSx for Windows File Server file systems, Amazon FSx for Lustre file systems, Amazon FSz for OpenZFS file systems, and Amazon FSx for NetApp ONTAP file systems.
https://aws.amazon.com/datasync/

## More Solution Architectures
- Event processing architectures involves services like SQS,SNS and lambda
- S3 events can be used with eventbridge to send to different AWS services
- Cloudfront does caching at frontend,APIGateway too has caching w.r.t region.Lambda,EC2 instance doesnt have caching.
- DAX,Memcached,Redis are in memory ones
- NACL has deny rule.security group doesnt have deny but can allow certain IPs.optional firewall installed at instance s feasible as well.
- Network load balancer doesnt have security group and connection termination
- ALB + WAF(web application firewall) costly but can filter IPs
- Highly available Ec2 instanse is possible by creating EC2 instance using elasticIP,cloudwatch metric and lambda
- EC2 instance+elasticIP+EC2 userdata+ASG as well as EC2 instance+elasticIP+EBS+ASG
- AWS Direct connect move GB/s of data.Snowball & SnowMobile move PB of data.AWS Datasync move large amount of data between on premise and S3
- EC2 enhanced networking by ENA upto 100 Gbps and Intel 82599 VF upto 10 Gbps.elastic fabric adapter works for linux only
- Network storage by S3,EFS,FSx for lustre
- AWS paralel cluster automate creation of VPC,subnet,cluster,instance types.can enable EFA

## Other Services
- Cloudformation Template provides infrastructure as a service
- SES inbound and outbound e-mails.usecases are transactional,marketing and bulk  e-mail communication.
- Amazon pinpoint 2 way communication like SMS.run campaigns by sending bulk transactional SMS.
- in SNS and SES application is responsible for message format,delivery and schedule whereas in pinpoint itself s responsible of it
- SSM session manager allows you to start a secure shell on your EC2 and on premises server.without SSH keys and security group for port 22
- Run command,Patch Manager,maintenance windows,automation with maintenance and run are other services of SSM 
- cost explorer visualize,understand,manage AWS costs.Reports like monthly,resource level,savings plan alternatives for reserved instances,forecast 
- elastic transcoder is used to convert media files stored in S3 into other media formats required by consumer playback devices
- AWS batch fully managed batch processing at any scale.batch jobs are defined as docker images run on ECS
- Appflow integration service that enables to transfer data from software as a application and AWS.sources can be servicenow,slack,Zendesk whereas destinations can be amazon S3,redshift.frequency can sceduled or on demand.data transformation like filtering and validation.it canbe encrypted over the public or private link 

## White Papers
- 6 pillars are operational excellence,security,reliability,performance effiency,cost optimization and sustainability
- AWS Well-Architected Tool (AWS WA Tool) is a service in the cloud that provides a consistent process for measuring your architecture using AWS best practices.
- In addition to AWS best practices, you can use custom lenses to measure your workload using your own best practices.
- AWS Trusted Advisor provides recommendations that help you follow AWS best practices. It evaluates your account by using checks. These checks identify ways to optimize your AWS infrastructure, improve security and performance, reduce costs, and monitor service quotas.











