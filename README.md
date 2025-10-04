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


# How patching for AMI works in AWS? 