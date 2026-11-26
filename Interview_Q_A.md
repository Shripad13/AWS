
This file contains all Questions asked in DevOps / Cloud Interview.

1. If logical volume is corrupts how do you restore safely?

2. After Patching, server is not accessible how do you troubleshoot?

3. Using Shell scripting, how do you create one user on 10 servers?

4. Listener and targets in load balancer?

5. through IP you are connecting to server, but through hostname you are not able to login what do you check(Network side is all ok)

6. NFS default path?

7. SFTP & FTP difference what is the default path?

8. How do you deny the S3 public access?

9. If ALB setup behind the EC2 instances and application is having issue , what would your troubleshooting approach ?

10. AWS lambda function working fine , after recent deploymnet lambda function not working, how do troubleshoot

11. Explain hub & Spoke Network in AWS cloud?

12. git rebase & merge
13. git & Github compararison
14. Ansible architecture
15. K8s architecture
16. if we apply manifest file, what happens next?
17. AWS S3 files need access in other region? how do you achieve?
18. AWS ec2 is in private subnet , how do you access?
19. you have provisioned the EC2 instance through terraform after provisioning EC2 is not able to access, how do you check?
	
20. below artefacts in a folder where Dockerfile resides:
datawrap/
config.zip      (which also includes script_app.py file)
Task:
Create an image which includes above 2 artefacts/files/folders and post that execute script_app.py file

21. If deployment manifest file made replica set to 0 , earlier it had 3 replica set, what would happen if i apply the manifest file ?

22. Terraform version does not released specific feature, but we need to use that , what are the ways to do it in Terraform configuration files?

23. what is Azure service principal

24. provision storage account using azurerm terraform, write configuration file.

25. Write Kubernetes Deployment file with rolling update strategy

What are these Errors in K8s.
OOM killed error
CrashLoopBackoff
ImagePullBackOff error

DB connection error

26. Explain Docker network - Overlay, Bridge, Default
27. NAT instance  & NAT gateway 
28. Pod lifecycle
29. Docker lifecycle
30. nodeSelector?
Pod Affinity & Antiaffinity?
31. Difference in PV & PVC in K8s
32. Docker volume - how to mount ?
33. In Terraform - what does mean by below?
Dynamic block
Data block
Resource block	
	
34. Integrate the ArgoCd with CICD pipeline
35. explain Helm chart - components 
36. Explain zero downtime deployments?
 Canary deployment
 Blue green deployment
37. Splunk query to check the cpu in CLI 
38. Production Incident handled by you end to end
39. IAM policy  & IAM role
40. TRUST policy & resource policy
41. Application unreachable- how do troubleshoot
42. What is provider aliases in Terraform
43. How do you pass the secrets securely in terraform configuration files?(Answer- use ephermal block)

44. how do you provision the same resources which is in the subscription A to  subscription B
subscription liases

45. how do you write in approvals in Azure DevOps pipelines YAML file

46. IF project B generates the artefacts and those artefacts i will have to use it in project A

47. how do you provision the same resources which is in the subscription to other subscription
48. how do you write in approvals in Azure DevOps pipelines YAML file?
49. azure subscription alias?
50.  how do you run ansible playbook for one host through command ?
51.  Which Ansible modules you used so far?
52.  what is ansible credentials module?
53.  🔹What is a Kubernetes Operator?
🔹What is a Custom Resource Definition (CRD)?
🔹What is a Custom Controller?
🔹Explain Operator architecture and how it works.
🔹What are API groups in Kubernetes?
🔹What is etcd?
🔹Explain the OSI model layers and their significance.
🔹What’s the difference between HTTP and HTTPS?
🔹What is DNS?
🔹What happens when you hit www.hashedin.com in a browser?
🔹What is recursive DNS?
🔹Explain Git lifecycle from cloning a repo to pushing code.
🔹What is Git architecture?
🔹What is a kernel? Is Linux an OS or a kernel?
🔹Write ten Linux commands and explain their usage.
🔹Difference between virtualization and containerization.
🔹Which Linux features help Docker work?
🔹What is Docker daemon?
🔹Explain Docker architecture & lifecycle.
🔹Write five Docker commands and explain them.
🔹Write a Jenkins pipeline that builds and pushes a Docker image.
🔹What are namespaces in Kubernetes?
🔹Write a Pod specification YAML file for an NGINX server.
🔹What is VPC in AWS?
🔹Difference between public and private subnets.
🔹What are NAT Gateway and Internet Gateway?
🔹Can a NAT Gateway be created inside a private subnet?
🔹How do instances in private subnets access the internet?
🔹Write and explain Terraform lifecycle commands.
🔹What is a state file in Terraform?
🔹How to manage state files in team collaboration?

💻 Round 3: Technical Interview – 2

Key Questions:
🔸Write a simple Dockerfile to create a Docker image.
🔸Explain Docker workflow.
🔸Why is it essential to make etcd highly available in Kubernetes?
🔸What happens when you hit a website URL?
🔸What is an SSL certificate?
🔸Explain the role of NAT in AWS.
🔸Difference between S3 buckets and EBS volumes.
🔸Amazon AMI vs Snapshot—what’s the difference?
🔸Explain remote state locking in Terraform.
🔸Difference between MySQL and MongoDB.
🔸What is a relational database?
🔸what’s a primary key in SQL?


1. Production DB migation is going on and in haflway migration failed,How do you design the to restore the DB or what steps would you take ?
2. How do you integrate Jira with Agentic AI tool?
3. How HPA works if traffic increases unexpectedly
   
4. What is trivy for image scanning & what it will detect?
• OS package vulnerabilities (CVEs): Finds known security flaws in operating system packages (such as Debian, Ubuntu, Alpine, or Red Hat) installed inside the image.
• Application dependency vulnerabilities: Scans language-specific libraries and packages (such as Node.js, Python, Go, or Java dependencies) for known CVEs and available fixes.
• Exposed secrets: Detects hardcoded sensitive data embedded in image layers or config files, such as API keys, tokens, passwords, and private keys.
• Misconfigurations: Flags insecure configuration settings or Infrastructure-as-Code (IaC) issues when scanning files or configurations.
• License issues: Identifies software licenses present in packages so you can check compliance risks. 

# KSB Tech - AWS, Gitlab, K8s, Terraform, Docker
1. What is SAST & DAST & its Tools ?
2. How do you remediate vulnerabilities found in Docker image & deployed in Production?
3. How do you remediate vulnerability in base image instead of creating entire image?
4. Which one would you prioritize? Remediating the vulnerability image which is in production or Developer wants Staging deployment which is also critical?
5. How do you approach & troublshoot & fix k8s OOM error?
6. How do you maintain Docker image ?
7. How do you deploy 50 microservices using single CICD workflow?
8. Need to install OS version & Verify on 100 EC2, how do you check?
9. How do you integarte security in CICD workflow?
10. How do you use Terraform for multiple environments? 
11. Deployment is succeded but application is not reachable, how do you troubleshoot?


# Deloitte - Team Lead
1. What is MCP server?
2. Difference between GenAI & Agentic AI?
3. How do you integrate Jira with Agentic AI?