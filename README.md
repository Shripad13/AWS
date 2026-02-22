# AWS

# Waterfall:	
1. Requirements → 2. Design → 3. Implementation → 4. Testing → 5. Deployment → 6. Maintenance
# Agile:
	[ Sprint 1 ] → [ Sprint 2 ] → [ Sprint 3 ] → ... (Continuous delivery & feedback)


## Cross-account IAM in AWS
Refers to the practice of allowing resources or users in one AWS account (Account A) to securely access resources in another AWS account (Account B), using AWS Identity and Access Management (IAM).

🔁 Why Cross-Account Access?
To separate environments (e.g., dev, staging, prod) or teams (e.g., finance, engineering).

🛠️ Example Use Case
Scenario: An EC2 instance in Account A needs to access an S3 bucket in Account B.

In Account B:
Create an IAM role with permissions to access the S3 bucket.
Add a trust policy to allow Account A to assume the role.

In Account A:
The EC2 instance is configured to assume the role from Account B (using AWS SDK or CLI).

🔐 Security Best Practices:
1. Least Privilege: Only grant permissions necessary for the task. For example, use a custom IAM policy instead of broad policies like AmazonS3FullAccess if possible.
2. Use External IDs: When using third-party or external accounts, always enable external IDs to prevent unauthorized access.
3. Audit Role Assumptions: Enable CloudTrail to log all role assumptions for monitoring and auditing.

# How will you establish cross-account access between two AWS accounts using IAM roles and policies and network configurations?
To establish cross-account access between two AWS accounts using IAM roles and policies, follow these steps:
1. In Account B (the account with the resource to be accessed):
   a. Create an IAM Role:
      - Go to the IAM console and create a new role.
      - Select "Another AWS account" as the trusted entity.
      - Enter the Account ID of Account A (the account that needs access).
      - Optionally, enable "Require external ID" for added security.
   b. Attach Permissions Policy:
      - Attach a policy that grants the necessary permissions to access the resource (e.g., S3 bucket access).
   c. Note the Role ARN:
      - After creating the role, note down the Role ARN, as it will be needed in Account A. 
2. In Account A (the account that needs access):
   a. Configure the EC2 Instance or User:
      - If using an EC2 instance, ensure it has an IAM role that allows it to assume the role in Account B.
      - If using a user, ensure the user has permissions to assume the role in Account B.
   b. Assume the Role:
      - Use AWS SDKs or CLI to assume the role from Account B using the Role ARN noted earlier.
      - Example CLI command:
        ```
        aws sts assume-role --role-arn "arn:aws:iam::AccountB-ID:role/RoleName" --role-session-name "SessionName"
        ```
   c. Use Temporary Credentials:
      - The assume-role command will return temporary security credentials (Access Key ID, Secret Access Key, Session Token) that can be used to access resources in Account B.

3. Network Configurations:
   - Ensure that any necessary VPC peering or VPN connections are established if the resources being accessed require network-level access.
   - Configure security groups and NACLs to allow traffic between the accounts if needed.
  

## Vertical Scaling
1. Vertical scaling means adding resources to the same instance &  involves DOWNTIME.

STOP the Instance>> Actions> Change the Instance type>> START the Instance

In any cloud, Verting scaling requires downtime.


# Downtime involves by updating below things -
1. Instance type - t3 to t2
2. ami - it will change of the boot disk, like properties change & you can verify by instance ID change

# how to create a Amazon Machine Image on AWS console?
1. Select the EC2 Instance
Find and select the EC2 instance that you want to create an AMI from.

2. Create Image (AMI)
With the instance selected, click on the Actions button (top-right) to reveal a dropdown menu.
Under Image and templates, select Create image.

3. Configure Image Settings
A new window will pop up with fields to configure the image.
Image Name: Give your image a descriptive name (e.g., my-web-server-ami).
Image Description: Optionally, add a description for your AMI.

No Reboot: By default, AWS will reboot your instance to create the AMI. If you need the instance to remain running and can tolerate some inconsistencies in the image, you can select the "No Reboot" option. It’s generally recommended to allow AWS to reboot the instance for consistency.

Instance Volumes: You can select which volumes to include in the image (usually the root volume is selected by default).

4. Create Image
Once you’ve configured the settings, click Create image.
AWS will begin creating the AMI. You can monitor the progress in the AMIs section of the EC2 Dashboard.

5. Verify the AMI
In the left-hand sidebar of the EC2 Dashboard, click on AMIs under Images.
Your new AMI will appear in the list with the status "Pending" while it’s being created. Once it's ready, the status will change to "Available."

6. Launch Instances from the AMI
After your AMI is created, you can use it to launch new EC2 instances.
In the AMIs section, select the AMI, click Launch instance from AMI, and follow the usual instance launch process.

# How patching for AMI works in Organizations? 

* As per the organization policy, typically we patch our machines for every once in a month / quarter.
* $ yum update -y                  (to patch all packages)
* $ yum update -y --security       (to patch only security packages)
* $ yum update -y --exclude=java*  (to exclude if some java packages)

# How AMI works as a part of this patching?
* Based on the application , we will maintain our AMI's. (OS of our choice, app installed on it that are needed for the application, ansible )

* AMI's taht you see in the market place or communities are bare basic & minm & thats why relly we do hardening them & we ship it as ready to use.

* Take a community AMI --> patch it -->  Configure the paramters & them take an AMI out of it.

* Steps for Patching an AMI -
1. you will have already one AMI in use which have "AMI ID"
2. When you patch a AMI
 Take a community AMI --> patch it -->  Configure the paramters & them take an AMI out of it.
3. SO this way you will have a new different  "AMI ID"
4. For this reason you should not hardcode AMI ID in terraform.

## AWS S3 - Simple Secure Service
> For storing data & hosting website
1) A Service on AWS to store almost infite amount of storage ( we just need to pay based on the storage you used )
2) Storage Class: Type of storage and based on this billing would be coming through ( This defines how fast can you retrieve the stored data )
3) Be mindful when selecting the S3 Bucket Name

> Funny Incident: A user created a bucket and the bucket was empty and he was billed 1200$
> https://cybernews.com/news/amazon-bucket-name-can-cause-massive-bill/

# Keep in mind, the infra code that you're designing should be multi-environment and should be DRY.
>  Dev ----> QA ----> Prod 

# Code Structure :
1) tf-module-terraform: backend module ( actual code of the objects ) 
2) expense-terraform: root-module ( code to source the backend module )



## Web Application Firewall (WAF): 
A WAF is a security system that filters and monitors HTTP traffic between a web application and the Internet. Its primary role is to protect web applications from a range of attacks such as SQL injection, Cross-site scripting (XSS), Cross-site request forgery (CSRF),DDoS (Distributed Denial of Service) attacks, Bad bots and scraping (automated tools trying to exploit or collect data from a site).

Protects web applications from malicious attacks (SQL injection, XSS, etc.) by filtering HTTP requests.

## Amazon CloudFront: 
Accelerates the delivery of web content globally, improving performance for users and reducing server load.
CloudFront is a Content Delivery Network (CDN) service provided by Amazon Web Services (AWS). It is used to distribute content (such as images, videos, JavaScript, HTML, etc.) to users across the world with low latency and high transfer speeds.


# difference between AWS ECS & AWS EKS service :
AWS ECS is a fully AWS-native container orchestration service that is simple to set up, tightly integrated with AWS, and requires low operational effort.
AWS EKS is a managed Kubernetes service that provides standard Kubernetes APIs, greater flexibility, and multi-cloud portability.
ECS is ideal for AWS-centric teams looking for simplicity and faster deployment.
EKS is better suited for large-scale, complex platforms requiring Kubernetes features and ecosystem tools.


## Cloud Cost Optimization -
Cloud optimization is the process of improving performance, reducing cost, increasing efficiency, and ensuring reliability of cloud resources by right-sizing, eliminating waste, and applying best practices in architecture, operations, and governance.

Cloud cost optimization is not about cutting resources — it’s about using the right resource, at the right time, for the right workload.

Right-sizing means selecting the correct compute, storage, and database capacity based on actual usage rather than overprovisioning.

1️⃣ Visibility First (Most Ignored Step)
You can’t optimize what you can’t see.
✔ Enable AWS Cost Explorer / Azure Cost Management / GCP Billing
✔ Tag everything: env, team, service, owner
✔ Set budget alerts early (even for dev environments)
➡ 20–30% savings come just from visibility.

2️⃣ Right-Size Compute (Biggest Impact)
Most VMs and nodes are over-provisioned.
✔ Review CPU & memory usage
✔ Downsize underutilized EC2 / Azure VMs
✔ Reduce Kubernetes node sizes
✔ Set correct pod resource requests & limits
➡ Common saving: 30–50%

3️⃣ Use Correct Pricing Models
Paying on-demand for steady workloads is a mistake.
Compute
Reserved Instances / Savings Plans (AWS)
Reserved VM Instances (Azure)
Committed Use Discounts (GCP)

Batch / Non-critical workloads 
Spot Instances / Preemptible VMs

4️⃣ Kubernetes Cost Control (Hidden Drain)
Kubernetes hides costs behind abstraction.

✔ Remove unused namespaces
✔ Kill zombie pods & idle services
✔ Use Cluster Autoscaler
✔ Schedule non-prod clusters to shut down at night
✔ Avoid over-replicated services
➡ Kubernetes without control = silent money leak.

5️⃣ Storage Optimization (Easy Wins)
Storage costs grow quietly.
✔ Move cold data to cheaper tiers (S3 IA, Glacier, Azure Cool/Archive)
✔ Delete unused snapshots & volumes
✔ Set lifecycle policies
✔ Compress logs and limit retention

6️⃣ Network Costs (Often Missed)
Data transfer can shock you.
✔ Avoid cross-AZ & cross-region traffic
✔ Use CDN (CloudFront, Azure Front Door)
✔ Keep services close to consumers
✔ Watch NAT Gateway and egress costs

7️⃣ Serverless ≠ Always Cheaper
Serverless is great — but not free.
✔ Avoid chatty Lambda calls
✔ Optimize memory & execution time
✔ Use provisioned concurrency only when needed
✔ Long-running tasks → containers/VMs

8️⃣ Culture > Tools (Most Important)
Cost optimization fails without ownership.
✔ Introduce FinOps mindset
✔ Make teams accountable for spend
✔ Review costs monthly, not yearly
✔ Optimize continuously, not once



✨ 𝐄𝐂𝟐:
What is EC2?
What are instance types?
What input parameters do you consider while creating an EC2 instance?
What is key pair in EC2?
What is Security Group?
Difference between Security Group and NACL?
How do you login to an EC2 instance?
I am unable to login into an EC2 instance — how will you troubleshoot?
How do you check who logged in to your EC2 instance?
How do you check logs of an EC2 instance without CloudWatch?
What is a private EC2 instance?
How do you access an EC2 instance in a private subnet?



✨ 𝐋𝐨𝐚𝐝 𝐁𝐚𝐥𝐚𝐧𝐜𝐢𝐧𝐠 & 𝐃𝐍𝐒:
What is a Load Balancer?
What are the types of Load Balancers in AWS?
Difference between ALB and NLB?
When do you use ALB vs NLB?
What is Gateway Load Balancer?
How do you map a domain name to a Load Balancer?
What is Route 53?
What are the routing policy types in Route 53?
What is a health check in Route 53?

✨ 𝐑𝐃𝐒 & 𝐃𝐚𝐭𝐚𝐛𝐚𝐬𝐞𝐬:
What is RDS?
How do you securely connect to a database?
What parameters do you consider for DB security?
How do you create a user in RDS?
Can EC2 in one account access RDS in another account?


What are all backend services in AWS lambda?
How to launch Public EC2 instance?
How do you ensure that subnet is public or Private?
How will you troubleshoot & fix storage issue in EC2 instance?


1. how to provide a public access temporary for particular object in AWS S3 service?
To give temporary public access to a specific object in Amazon S3, the recommended and safest way is to use a pre-signed URL. This grants time-limited access without making the object or bucket public. We can share it through CLI command, Python Boto3 or Console.

2. Amazon S3, Lifecycle rules?
are used to automatically manage objects over time—for example, moving data to cheaper storage or deleting it after a certain period.
S3 Lifecycle rules define actions that Amazon S3 applies to objects in a bucket based on time.
They help you:
Reduce storage costs
Automate data retention policies
Manage backups, logs, and archives

3. Application running on Private EC2 machine  & application has to fetch some images which are in S2. How can this be achieved?
A private EC2 instance can access S3 securely by attaching an IAM role and using an S3 VPC Gateway Endpoint, which allows private access without NAT or internet exposure.

4. WHat are endpoints ?
An AWS VPC endpoint enables private connectivity between a VPC and AWS services without traversing the public internet
| Scenario                      | Best Endpoint      |
| ----------------------------- | ------------------ |
| Private EC2 → S3              | Gateway Endpoint   |
| Private EC2 → Secrets Manager | Interface Endpoint |
| Private VPC → SaaS            | Interface Endpoint |
| Traffic inspection            | GWLB Endpoint      |
S3 & DynamoDB use Gateway Endpoints — not Interface Endpoints

5. How will you ensure zero downtime for your application?
   a. Blue/green Deploymnet
   b. Canary Deployment
   c. ROlling Updates

6. What is Elatic Network Interface (ENI)?
An Elastic Network Interface (ENI) is a virtual network interface that can be attached to an EC2 instance in a VPC. It represents a virtual network card and can have its own private IP address, public IP address, security groups, and MAC address.

7. What is AWS Systems Manager (SSM) and how does it help in managing EC2 instances?
AWS Systems Manager (SSM) is a management service that helps you automatically manage your EC2 instances and other AWS resources. It provides a unified interface to view operational data from multiple AWS services and allows you to automate operational tasks across your AWS resources.
With SSM, you can:   
- Remotely manage and configure EC2 instances without needing SSH access.
- Automate patch management, software installation, and configuration changes.
- Collect inventory data about your instances and software.
- Execute commands on multiple instances simultaneously.   
- Monitor and maintain system compliance.
- Securely store and manage configuration data and secrets. 
- Integrate with other AWS services for enhanced management capabilities.
- Overall, SSM simplifies the management of EC2 instances, improves security, and reduces operational overhead.
  
8. How to monitor EC2 instances without CloudWatch?
You can monitor EC2 instances without CloudWatch by using alternative methods such as: 
1. SSH Access: Manually log in to the instance via SSH to check system metrics using commands like top, htop, vmstat, iostat, netstat, etc.
2. Third-Party Monitoring Tools: Use tools like Nagios, Zabbix, Datadog, New Relic, or Prometheus to monitor instance performance and health.
3. Custom Scripts: Deploy custom monitoring scripts on the instance that log metrics to files or external databases for analysis.
4. OS-Level Monitoring Tools: Utilize built-in OS tools like Windows Performance Monitor or Linux system monitoring utilities.
5. Log Analysis: Use log files generated by applications and the OS to monitor performance and issues.
6. SNMP Monitoring: Set up SNMP agents on the instance and use SNMP monitoring tools to collect metrics.
7. AWS Systems Manager: Use SSM to run commands and gather information about the instance's  state and performance.
8. Network Monitoring: Use VPC Flow Logs to monitor network traffic to and from the instance.
Note: While these methods can provide insights into EC2 instance performance, they may not offer the same level of integration and ease of use as CloudWatch.

9. How to troubleshoot & fix storage issue in EC2 instance?
To troubleshoot and fix storage issues in an EC2 instance, follow these steps:   
1. Check Disk Space Usage:
   - SSH into the EC2 instance.
   - Use commands like `df -h` to check disk space usage and identify if any partitions are full.
   - Use `du -sh /*` to find large directories consuming space.
2. Identify Large Files:
   - Use `find / -type f -size +100M` to locate large files that may be taking up space.
   - Check application logs, temporary files, and old backups that can be deleted.  
   - Remove Unnecessary Files:
   - Delete old log files, temporary files, and unused applications.
   - Clear package caches (e.g., `yum clean all` for Amazon Linux).
3. Check EBS Volume Status:
   - In the AWS Management Console, navigate to the EC2 dashboard and check the status of the attached EBS volumes.
   - Ensure that the volume is not in a degraded state.
   - Resize EBS Volume if Needed:
   - If the disk is full, consider resizing the EBS volume.
   - Stop the instance (if required), modify the volume size in the AWS console, and then restart the instance.
   - After resizing, extend the file system using commands like `resize2fs` (for ext4) or `xfs_growfs` (for XFS).
4. Check for Disk Errors:
   - Use `dmesg` or `sudo smartctl -a /dev/xvda` to check for disk errors that may indicate hardware issues.
   - If errors are found, consider replacing the EBS volume.
5. Monitor Disk I/O:
   - Use `iostat` or `vmstat` to monitor disk I/O performance and identify bottlenecks.
   - If high I/O wait times are observed, consider upgrading to a higher-performance EBS volume type.
   - Set Up Monitoring:
   - Implement monitoring solutions (e.g., CloudWatch, third-party tools) to keep track of disk usage and performance metrics.
6. Review Application Configuration:
   - Ensure that applications running on the instance are configured to use storage efficiently.
   - Check for any misconfigurations that may lead to excessive disk usage.
   - Regular Maintenance:
   - Schedule regular maintenance tasks to clean up unnecessary files and monitor disk usage trends.
   - Implement automated alerts for disk space thresholds.
By following these steps, you can effectively troubleshoot and resolve storage issues on an EC2 instance.

10. How to launch Public EC2 instance?
To launch a public EC2 instance, follow these steps:
1. Log in to the AWS Management Console.
2. Navigate to the EC2 Dashboard.
3. Click on "Launch Instance" to start the instance creation process.   
4. Choose an Amazon Machine Image (AMI) that suits your needs (e.g., Amazon Linux 2, Ubuntu, etc.).
5. Select an Instance Type based on your requirements (e.g., t2.micro for free tier).
6. Configure Instance Details:
7. In the "Configure Instance Details" step, ensure the following:
   - Network: Select the appropriate VPC.
   - Subnet: Choose a public subnet (one that has a route to an Internet Gateway).
   - Auto-assign Public IP: Set this to "Enable" to assign a public IP address to the instance. 
   - Add Storage: Configure the storage size and type as needed.
8. Add Tags: (Optional) Add tags to help identify your instance.
9. Configure Security Group:
   - Create a new security group or select an existing one.
   - Ensure that the security group allows inbound traffic on necessary ports (e.g., port 22 for SSH, port 80 for HTTP, port 443 for HTTPS).
   - Review and Launch:
   - Review your instance configuration.
   - Click "Launch" to proceed.
   - Select or create a key pair for SSH access to the instance.
 - Launch the Instance:
   - Click "Launch Instances" to start the instance.  
   - Access the Instance:
   - Once the instance is running, you can access it using the public IP address assigned to it.
   - Use an SSH client to connect to the instance using the key pair you selected during launch.
 - 
10. How do you ensure that subnet is public or Private?
To determine if a subnet is public or private in AWS, you can check the following criteria:
1. Route Table Association:
- Go to the VPC Dashboard in the AWS Management Console.
- Select "Subnets" from the left-hand menu.  
- Select the subnet you want to check.
- In the "Route Table" tab, check the associated route table.
- Examine the routes in the route table:
   - If there is a route that directs traffic to an Internet Gateway (IGW) (e.g., 0.0.0.0/0 -> igw-xxxxxxxx), the subnet is public.
   - If there is no such route, the subnet is private.
2. Auto-assign Public IP Setting:
3. While launching an instance in the subnet, check if the "Auto-assign Public IP" option is enabled by default.
- If it is enabled, the subnet is likely public.
- If it is disabled, the subnet is likely private.
3. Internet Gateway Association:
- Check if the VPC associated with the subnet has an Internet Gateway attached.
- A subnet in a VPC with an Internet Gateway and appropriate route table entries is typically public.
4. NAT Gateway/Instance:
 - If the subnet routes traffic to a NAT Gateway or NAT Instance for internet access, it is a private subnet.
 - If it routes directly to an Internet Gateway, it is a public subnet.
By checking these criteria, you can determine whether a subnet is public or private in AWS.


12. Application running on Private EC2 machine  & application has to fetch some images which are in S3. How can this be achieved?
A private EC2 instance can access S3 securely by attaching an IAM role and using an S3 VPC Gateway Endpoint, which allows private access without NAT or internet exposure.

13. What are all backend services in AWS lambda?
AWS Lambda can integrate with various backend services to enhance its functionality. Some of the key backend services that can be used with AWS Lambda include:
1. Amazon API Gateway: To create RESTful APIs that trigger Lambda functions.
2. Amazon S3: To trigger Lambda functions on object creation, deletion, or modification events.
3. Amazon DynamoDB: To trigger Lambda functions on table updates, inserts, or deletes.
4. Amazon RDS: To connect Lambda functions to relational databases for data processing.
5. Amazon SNS: To trigger Lambda functions based on notifications.
6. Amazon SQS: To process messages from SQS queues using Lambda.
7. Amazon Kinesis: To process real-time streaming data with Lambda.
8. AWS Step Functions: To orchestrate complex workflows involving multiple Lambda functions.
9. Amazon CloudWatch: To monitor and log Lambda function performance and errors.
    
10. How to make a  EC2 instance private which is launched in public subnet?
To make an EC2 instance private that is launched in a public subnet, you can follow these steps:
1. Detach the Public IP Address:
2. Stop the EC2 instance.
3. In the EC2 Dashboard, select the instance and go to the "Networking" tab.
4. Click on "Actions" and select "Manage IP Addresses."
5. Deselect the option to auto-assign a public IP address and save the changes.
6. Update the Route Table:
7. Go to the VPC Dashboard and select "Route Tables."
8. Find the route table associated with the public subnet where the EC2 instance is located. 
9. Remove any routes that direct traffic to the Internet Gateway (IGW) for the subnet.
10. Create a Private Subnet (if not already existing):
11. If you don't have a private subnet, create one in the same VPC without a route to the Internet Gateway.

   
11. What is cooldown period in aws auto scaling group ?
The cooldown period in an AWS Auto Scaling group is a configurable time interval that prevents the Auto Scaling group from launching or terminating additional instances immediately after a scaling activity has occurred. This period allows the newly launched instances to stabilize and start serving traffic before any further scaling actions are taken.
During the cooldown period, the Auto Scaling group ignores any additional scaling policies or alarms that would normally trigger further scaling actions. This helps to avoid rapid fluctuations in the number of instances, which can lead to instability and increased costs.   


12. What is Cloudwatch alarms & CLoudwatch events?
CloudWatch Alarms:
CloudWatch Alarms monitor specific metrics and trigger actions based on predefined thresholds. For example, you can create an alarm that monitors CPU utilization of an EC2 instance and sends a notification or triggers an Auto Scaling action if the CPU usage exceeds a certain percentage for a specified period.
CloudWatch Events:  
CloudWatch Events (now part of Amazon EventBridge) allow you to respond to changes in your AWS resources in near real-time. You can create rules that match specific events (like an EC2 instance state change or an S3 object creation) and route them to targets such as Lambda functions, SNS topics, or SQS queues for further processing.

13. How will you establish the communication between EC2 & S3 without internet?
To establish communication between an EC2 instance and S3 without using the internet, you can use a VPC Endpoint for S3. A VPC Endpoint allows private connectivity between your VPC and supported AWS services without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection. Here are the steps to set it up:
1. Create a VPC Endpoint for S3: 
2. Go to the VPC Dashboard in the AWS Management Console.
3. Select "Endpoints" from the left-hand menu and click "Create Endpoint." 
4. Choose the service category as "AWS services" and select "com.amazonaws.<region>.s3" as the service name.
5. Select the VPC where your EC2 instance is located.
6. Choose the route tables associated with the subnets where your EC2 instance resides. This will ensure that traffic to S3 is routed through the VPC Endpoint.
7. Configure Security Groups (if needed):
8. If you have specific security group rules, ensure that the security group associated with your EC2 instance allows outbound traffic to the S3 service.


# how to design CICD Pipeline for microservices based architecture application?
“I design CI/CD for microservices which has its own CI pipeline to build and scan an immutable image, and a GitOps-based CD pipeline deploys it safely to Kubernetes with observability and rollback.”

Overall, this design enables faster releases, isolation between services, and safe deployments.”

1. Source & Repo Strategy - Independent pipelines
Each microservice has its own repository and pipeline so services can be built and deployed independently.

2. CI Pipeline (per microservice)
On every commit:
Code checkout
Static code analysis
Unit test
Build artifact
Build Docker image
Security scans (SAST & container scan)
Push immutable image to a container registry

3. CD Pipeline (GitOps-based) - Build once, deploy everywhere
I use a GitOps approach:
Kubernetes manifests or Helm charts are stored in a separate deployment repo
The pipeline updates the image tag
Argo CD/Flux syncs the changes to the cluster

4. Environment Promotion
The same image is promoted across Dev → QA → Stage → Prod with environment-specific configs and manual approvals for production.

5. Deployment & Rollback
Deployments use rolling, blue-green, or canary strategies, and rollback is done by reverting the Git commit or Helm release.
6. Security & Observability
Secrets are managed via Vault or cloud secret managers, and we monitor deployments using Prometheus, Grafana, and centralized logging.

# Which tools did you use?
Jenkins/GitHub Actions for CI, Docker for containers, Helm + Argo CD for CD, Kubernetes for orchestration.
# How do you handle failures?”
Automated tests, health checks, alerts, and instant rollback via Helm/GitOps.
# How do services communicate?
Via APIs with contract testing and backward-compatible changes.

Git → CI → Image Registry → GitOps Repo → Argo CD → Kubernetes

# Can Elastic file store be used for 2 EC2 instances? YES
EFS is a FUlly Managed Network File System & Shared file storage.
Both instances read/write to the same files.

✅ EFS Specifically built for Multiple EC2 instances to mount the same filesystem simultaneously.
Port 2049 (NFS) inbound from EC2 security group
To use EFS with 2 EC2 instances: Both EC2 instances must be in the same VPC
EFS works within the same AWS region.

Example use cases:
Shared uploads folder
Shared logs
Shared application data
CMS systems like WordPress
Microservices sharing files


| Feature               | **EFS**             | **EBS**                        |
| --------------------- | ------------------- | ------------------------------ |
| Storage Type          | File Storage (NFS)  | Block Storage                  |
| Mount Type            | Network File System | Attached as disk to EC2        |
| Multi-Instance Access | ✅ Yes (many EC2s)   | ❌ No (one EC2 at a time*)      |
| Scalability           | Auto-scales         | Fixed size (resize manually)   |
| Performance           | Moderate            | High performance / low latency |
| Best For              | Shared files        | Databases, OS disks            |
* EBS can be detached and reattached to another EC2, but not simultaneously.

✅ Use EBS When:
Running database (MySQL, PostgreSQL, MongoDB)
Need low latency
Need high IOPS
Need consistent performance
Boot volumes
Best with: Amazon EC2 & Amazon RDS (internally uses EBS)

✅ Use EFS When:
Multiple EC2 instances need same files
Shared application data
Container workloads
Kubernetes persistent volumes
Auto-scaling web servers
Works great with: Amazon EKS & Amazon EC2