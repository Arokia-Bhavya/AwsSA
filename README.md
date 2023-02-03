# AwsSA
IAM (global)
-IAM user - user to access 
-IAM group - set of users
-IAM policies - set of permissions to aws services in json format
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
