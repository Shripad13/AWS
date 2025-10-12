# AWS
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