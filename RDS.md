
https://www.finalroundai.com/blog/aws-rds-interview-questions
https://mihirpopat.medium.com/mastering-aws-rds-10-real-world-scenario-based-questions-7634e3224190

# DB Engines in AWS - 
Aurora MySQL compatible
Aurora PostgreSQL Compatible
1. MySQL
2. PostgreSQL
3. MariaDB
4. Oracle DB
5. Microsoft SQL Server
6. IBM /DB2

# Explain how you would choose between Amazon RDS, Amazon DynamoDB, and Amazon Redshift for a data-driven application?

Choosing between Amazon RDS, DynamoDB, and Redshift for a data-driven application depends on your specific needs:

- Amazon RDS (Relational Database Service): Supports popular relational databases like MySQL, PostgreSQL, Oracle, SQL Server, and MariaDB.
Amazon RDS is ideal for applications that require a traditional relational database with standard SQL support, transactions, and complex queries.

- Amazon DynamoDB: A managed NoSQL database service for fast and predictable performance with seamless scalability.
Amazon DynamoDB suits applications needing a highly scalable, NoSQL database with fast, predictable performance at any scale. It's great for flexible data models and rapid development.

- Amazon Redshift: A fully managed data warehouse service designed for high-performance analysis using SQL queries.
Amazon Redshift is best for analytical applications requiring complex queries over large datasets, offering fast query performance by using columnar storage and data warehousing technology.

- Amazon Aurora: A MySQL and PostgreSQL-compatible relational database with performance and availability similar to commercial databases at a lower cost.

- Amazon DocumentDB: A managed NoSQL document database compatible with MongoDB, providing scalability, performance, and ease of use.

- Amazon Neptune: A fully managed graph database service that supports popular graph models like Property Graph and RDF.

- Amazon ElastiCache: A managed, in-memory caching service that supports Redis and Memcached, improving the performance of web applications.

- Amazon Timestream: A fully managed, serverless time-series database for IoT and operational applications.


# You can set up your database on AWS in two ways.

1. DB hosted on EC2: 
You do not have to care about server maintenance, os installation and patches.
But you are still responsible for db software installation on EC2 , regular backups, configuring scaling and high availability.


2. AWS RDS:
This is an AWS managed relational database.You will be responsible for creating,maintaining and optimising db itself.It supports most of the popular db management systems.

"Amazon RDS is a managed relational database service that automates time-consuming administrative tasks such as backups, patching, and scaling. Unlike traditional database hosting, RDS offers enhanced scalability and reduced manual intervention, allowing you to focus more on application development."

Commercial: Oracle,SQL server
Open Source: MySQL,Postgre SQL,Mariadb
Cloud Native: Amazon Aura

AWS RDS is built off of compute and storage components.The compute part is called db instance on which db instance will run.

AWS RDS supports three instance families. 
 standard : m class
 memory optimised: r and x class
 burstable performance

Access Control:
Use private subents as db subnet groups.Access can be further restricted by NACL and SG.User access can be controlled by IAM policy.


DB Backups:
 Automatic Backup Setting
 Manual Snapshots

High Availablity:
AWS RDS offers multi AZs setup. It will create a copy of your primary db in another AZ.The data in primary is synchronously replicated to the standby copy.

FailOver:
When we create a RDS , AWS assigns a DNS name to the DB instance. AWS uses that DNS name to Failover to standby DB.


IOPS in AWS RDS stands for Input/Output Operations Per Second.
It measures how many read and write disk operations your database storage can perform every second.


# Difference between RDS and Dynamo DB?
| Amazon RDS                                   | Amazon DynamoDB                                                                 |
| -------------------------------------------- | ------------------------------------------------------------------------------- |
| Relational Database                          | NoSQL Key-Value/Document Database                                               |
| Stores data in tables with relationships     | Stores items in tables without joins                                            |
| Supports SQL                                 | Does not support traditional SQL (uses API/PartiQL for some queries)            |
| Fixed schema                                 | Flexible schema                                                                 |
| Vertical scaling (primarily) + read replicas | Horizontal scaling by design                                                    |
| ACID transactions                            | Supports ACID transactions, but designed for simple, high-scale access patterns |
| Best for relational workloads                | Best for massive scale and low latency                                          |


Q: Your application serves 20 million users and needs response times under 10 ms. Which database would you choose?
DynamoDB, because it is designed for massive horizontal scalability and consistently low latency.

Q: Your application performs complex joins across 15 tables.
RDS, because relational databases are optimized for joins and complex SQL.

Q: Which is easier to scale?
DynamoDB, because it scales horizontally and automatically partitions data. RDS can scale vertically and also supports read replicas, but scaling relational databases generally requires more planning.

Q: Which database supports foreign keys?
RDS

Q: Which supports JOIN operations?
RDS

Q: Which offers lower latency?
DynamoDB

Q: Which database would you use for session storage?
DynamoDB

Q: Which is better for financial transactions?
RDS, because financial systems typically require relational integrity, complex queries, and transactional consistency.

I choose RDS when the application depends on complex relationships and transactional queries, and DynamoDB when I need extremely high throughput, flexible schemas, and predictable low-latency access at large scale, such as for user sessions, shopping carts, or IoT data.