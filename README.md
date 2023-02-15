# AwsSA
##IAM (global)
-IAM user:- user to access 
-IAM group:- set of users
-IAM policies:- set of permissions to aws services in json format
version,statement(effect,action,resource,principal)
-IAM password policy 
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

##EC2
-OS,Instace Size & config,Storage,Security Group,Bootstrap
-Bootstrap code runs as root user
-instance naming convention instanceclass:generation:size
-https://aws.amazon.com/ec2/instance-types/
-General,Compute Optimized,Memory Optimized
-https://instances.vantage.sh/

###Security Group
-region specific
-they act as firewall to Ec2 instances
-If application gets timed out,security group issue
-Connection refused its an application issue
-All inbound traffic are blocked by default
-All outbound traffic are allowed

###EC2 access
-SSH to the box
-EC2 instance connect only amazon linux 2
-Elastic IP is static public IP.It s limited to 5 per account
-Not a great practice to use elastic IP.use DNS instead
-SSH uses public IP for EC2 instance connect
-Placement group (cluster,spread,partition)
-Big Data Job uses cluster placement group 
-Spread placement group 7 per AZ
-Hadoop,Cassandra,Kafka uses partition
-ENI provides Private IP Address,Elastic IP Address,MAC Address.It can be attached or detached to Ec2.used in fail over scenarios
-EC2 hibernate Usecases like long running processes,preserve EBS volume 
-EC2 hibernate no more than 60 days.It should have root EBS volume
-EBS volume is a network drive.Its locked to AZ.To move volume across AZ snapshot should be taken
-root EBS volume deleted on termination by default other EBS volumes are not
-EBS Snapshot Archive moves snapshot to archive tier
-Recycle bin helps setting up rules to restore deleted snapshots from 1 day to 1 year
-Fastest Snapshot Restore no latency
-AMI amazon machine image can be public,custom and market place
-EC2 instance store provides high performance but they are temporary i3 series 100124 random read IOPS and 35000 write IOPS







