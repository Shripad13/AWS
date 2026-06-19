
Monitoring → Predefined dashboards and alerts.
Observability → About system behavior using telemetry data.
telemetry data - Data collected about the performance and behavior of a system.

# Black box Monitoring - Not Visible inside the application. Solarwinds, Zabbix are the Black box monitoring Tools. Usually COnventional tools are this one.

# White box Monitoring - Visible inside the Application. Prometheus is the white box monitoring tool.

Incident - Anything that causes an Outage/ unplanned outage is refrred as Incident.
Loss in Revenue.

Problem Management - A issue which has a underlying KNOWN issue referred as Problem.

RCA- Root Cause Analysis will have
When Incident arised?
Why it was the Issue?
What was the Issue?

> Infra Monitoring - CPU, Memory, Uptime, Service Status
> Application Performance Monitoring - Which component talking to which component, which component taking time to respond, which appln library have code issue, Also Tracing, which line of code is having issue.
> APM Tools - New Relic, Datadog, Dynatrace, Appdynamics

> Logs are stored on Centralized log aggregation tool - ELK

# What is a Metric?
There are 2 types of metrics
* Gauge - its a metric that represents a single numerical value that can arbitrarily go up & down.
* Counter - Its a cumulative metric, To represent the number of requests served, task completed or errors.


# Prometheus -
Prometheus is an open-source monitoring and alerting toolkit designed for reliability and scalability. It collects and stores metrics as time series data, allowing users to query and analyze the data to gain insights into the performance and health of their applications and infrastructure.

Prometheus Uses pull-based model via HTTP endpoints. Scrapes metrics from /metrics endpoint.

DEFAULT PORT  For Prometheus is 9090.
DEFAULT PORT  For Node Exporter is 9100.


# Node Exporter-
Node Exporter has to install on Server which needs to be monitored. It collects hardware and OS metrics such as CPU usage, memory usage, disk I/O, and network statistics.


/etc/prometheus/prometheus.yml is the main configuration file for Prometheus. 

prometheus.yml will have the configuration for Prometheus to scrape metrics from the Node Exporter. It will specify the targets (Node Exporter instances) and the scrape interval.

you tell Prometheus where to find them using scrape_configs

scrape interval - how often Prometheus should scrape the metrics from the targets. The default is 15 Seconds, but you can adjust it based on your needs.

evaluate interval - how often Prometheus should evaluate the rules defined in the configuration file. The default is 1 minute, but you can adjust it based on your needs.

rul_files - This section is used to specify the location of the rule files that Prometheus should use for alerting and recording rules. You can define your alerting rules in separate files and include them here.

scrape_configs - This section is used to define the targets that Prometheus should scrape for metrics. You can specify the targets using static configurations or use service discovery mechanisms to automatically discover targets in dynamic environments.

You can add multiple hosts in prometheus.yml file.

* Jobs - an endpoint you can scrape is called an instance, usually corresponding to a single process.
The configured job name that the target belongs to.

* Instance - A collection of instance with the same purpose, a process replicated for scalability or reliability for example.
<host>:<port> part of the targets URL that was scraped.

=~ : regex-match 
!~ : DO not regex-match 


Metrics specific to the Node Exporter can be accessed by navigating to http://<node_exporter_host>:9100/metrics in a web browser or using a tool like curl. This will display a list of all the metrics collected by the Node Exporter, along with their current values.

Metrics specific to the Node exporter are prefixed with node_.
node_cpu_seconds_total -   provides information about the total CPU time spent in various modes (user, system, idle, etc.),
node_exporter_build_info - provides information about the version and build details of the Node Exporter itself.

rate(node_cpu_seconds_total[5m]) - This query calculates the rate of change of the node_cpu_seconds_total metric over a 5-minute window. It gives you insights into how the CPU usage is changing over time, which can help identify trends or spikes in CPU usage.

* Prometheus query for CPU Average thats Idle & Utilized:
'avg by (instance_name) rate(node_cpu_seconds_total{mode="idle"}[5m]) * 100 ' :  Idle 

'100 - (avg by (instance_name) rate(node_cpu_seconds_total{mode="idle"}[5m]) * 100) ' : Utilized 

* Prometheus query for MEMORY Average thats Idle & Utilized:
' ceil (100 * ((node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes)) ' : Shows in % of Utilized MEM

ceil - for showing exact figures

rate(node_network_receive_bytes_total[5m]) - This query calculates the rate of change of the node_network_receive_bytes_total metric over a 5-minute window. It provides insights into the network traffic received by the node, helping to identify trends or spikes in network activity.

node_load15 - load avg in 15 seconds

node_filesystem_avail_bytes - filessystem space available in bytes.

node_memory_MemAvailable_bytes - available memory in bytes.

node_disk_io_time_seconds_total - total time spent on disk I/O operations in seconds.




PromQL - Prometheus Query Language used for querying time-series data.
Example - rate(http_requests_total[5m])

# How Promoetheus know whom it has to monitor?
prometheus.yml in scrape_configs section need to update the target nodes
Install node_exorter on Target nodes such as frontend, backend.

# What if you hae 100's of Target servers , hardcoding of IP in prometheus.yml is not possible?
I want prometheus to monitor only servers that has tag as "monitor: yes"


# Alert Manager -
Alert Manager is a tool to manage & send alerts.
Prometheus detects the problems but Alert Manager is the one that sends notifications to Email/slack/ Teams/ PagerDuty.

For Email we need SMTP server details & add it in config file.

> Creating a Prometheus is not a bigger job, but using prometheus is a Bigger job.


# Grafana -
Grafana is an open-source data visualization and monitoring platform that allows users to create interactive and customizable dashboards. It supports various data sources, including Prometheus, and provides a wide range of visualization options such as graphs, charts, and tables. 
Grafana is commonly used for monitoring applications, infrastructure, and business metrics, enabling users to gain insights and make informed decisions based on real-time data.

DEFAULT PORT  For Grafana is 3000.
Default username/pwd - admin/admin


# Data Sources in Grafana -
Grafana doesnt store data itself, it connects to various data sources to fetch the data for visualization. 

Some of the popular data sources supported by Grafana include:
1. Prometheus - for metrics
2. Elasticsearch - for logs
3. PostgresSQL - for relational database
4. Cloudwatch - for AWS monitoring
5. InfluxDB - for time-series data

You name a tool, it will extract data from many tools.
Grafana is not a only prometheus specfic tech stack.


You can Add Data Sources from Dashboard settings in Grafana UI. You need to provide the URL of the data source and the authentication details if required.

A Dashboard in Grafana is a collection of panels that display data from one or more data sources. Display different types of monitoring data like CPU usage, memory usage, network traffic, and application performance metrics. 
Dashboards can be customized with various visualization options, such as graphs, tables, and heatmaps, to provide insights into the health and performance of applications and infrastructure. Users can create multiple dashboards for different use cases and share them with team members for collaboration.

Create a Dashboard in Grafana by clicking on the "+" icon in the left-hand menu and selecting "Dashboard". 
You can then add panels to the dashboard by clicking on the "Add Panel" button and selecting the type of panel you want to create (e.g., graph, table, heatmap). 
In the metric browser Start typing the metric name - 
Click Run Query
CLick the Panel Title - Choose visualization type - Graph, Table, Gauge,Pie chart etc.

# Grafana grafana dashboard search- To get mnay details on dashboard
browse grafana dashboard search, (https://grafana.com/grafana/dashboards/)
click on Node Exporter Full
if you copy Dashboard or Download JSON for ReadyMade Dashboards

Grafana>> Dashboard>> Import 


1. How do you optimize a slow-loading Grafana dashboard?
 Reduce the number of panels, 
 optimize the underlying PromQL/Lucene queries, 
 use "Variables" to filter data at the source,  
 increase the scrape interval for non-critical data.

1. What is the difference between rate() and irate() in Prometheus?

rate() calculates the per-second average over a range (better for alerting),
irate() looks at the last two data points (better for volatile, fast-moving "zoom-in" graphs).


3. In Splunk/Kibana, how would you find the "Top 10" error-producing services in the last hour?
 I would use a "Terms Aggregation" on the service_name field, filtered by loglevel: ERROR, and sort by count descending.

# 4 Golden Signals/Metrics of Google SRE/ Observability - 
Traffic (logs)
Error   (logs)
Latency (logs)
Saturation (Monitoring tool) - CPU, Memory, Uptime, Service Status, Disk I/O

# three pillars of observability -
Logs
Metrics
Traces

RED Metrics:
Rate
Errors
Duration

USE Metrics:
Utilization
Saturation
Errors

# How is Kibana used with Elasticsearch?
Used for visualization, dashboards, index pattern management, and log analysis.

# Severity Levels -
Sev1 → Critical outage
Sev2 → High impact
Sev3 → Medium
Sev4 → Low


# Elasticsearch Architecture -
1. Explain the difference between a Primary Shard and a Replica Shard. What happens if a node holding a primary shard fails?
Primary shards are the original "buckets" where data is indexed. Replicas are copies. If a node with a primary shard fails, ES promotes a replica shard to primary to maintain availability. The cluster state turns "Yellow" until a new replica is created to meet the defined count.

2. How would you diagnose a "Circuit Breaking Exception" in an ES cluster?
Answer: This usually indicates JVM Heap pressure. I would check the indices.breaker.* stats, identify expensive queries (like heavy aggregations or large "terms" searches), and monitor the nodes/stats to see if garbage collection (GC) is struggling to keep up.

3. What is the "Split Brain" problem, and how do modern ES versions (7.x+) prevent it?Answer: Split-brain occurs when a cluster divides into two independent parts, both thinking they have a Master node. Modern ES uses a quorum-based voting system ($n/2 + 1$) and the cluster.initial_master_nodes setting to ensure only one master is elected.

4. How do you optimize an index for heavy write/logging workloads?

Answer: Increase the refresh_interval (e.g., to 30s), disable replicas during initial bulk loads, use multiple data paths, and ensure document IDs are generated by ES rather than provided manually to avoid lookup overhead.

5. What are "Hot-Warm-Cold" architectures?
   It’s a data tiering strategy. 
   Hot nodes use fast SSDs for active indexing; 
   Warm nodes hold less-frequently searched data on cheaper storage; 
   Cold nodes store long-term retention data, often in a searchable snapshot state to save costs.

A shard in Elasticsearch is a fundamental, self-contained subset of an index (a Lucene index) that allows data to be distributed across multiple nodes in a cluster

Index Lifecycle Management (ILM) in the ELK Stack  is a policy-driven feature that automates the management of time-based indices, such as logs and metrics. 
It streamlines data retention, optimization, and storage costs by automatically moving indices through five phases—Hot, Warm, Cold, Frozen, and Delete—based on age or size.

 I define policies that move data through phases: Hot (ingest), Warm (shrink/read-only), Cold (searchable snapshot), and Delete (automatic removal after X days).

6. How does Prometheus scrape metrics from a Kubernetes cluster?
Answer: It uses "Service Discovery." Prometheus queries the K8s API to find pods with specific annotations (e.g., prometheus.io/scrape: "true") and then pulls metrics from their /metrics endpoint via HTTP.


# What is the difference between "Logs" and "Events"?
Logs are a continuous stream of text data (e.g., Nginx access logs). 
Events are discrete, high-context occurrences (e.g., "User A changed their password"). Events are usually structured and more actionable.

# Why is Distributed Tracing important in a Microservices architecture?
In a monolith, a log tells you the error. 
In microservices, a request might touch 10 services. Tracing uses a Correlation ID (Trace ID) to follow a single request across service boundaries to find exactly where the latency or failure occurred.

# "Pull" vs "Push" monitoring.
 Pull (e.g., Prometheus) means the server reaches out to targets; it's easier to manage health but harder to scale across firewalls. 
 Push (e.g., Splunk, Dynatrace) means agents send data to the server; it's better for ephemeral (short-lived) jobs but can overwhelm the receiver.

 ### Scenario-Based: Strategic & Architecture

1. The "Data Explosion" Scenario:

Context: A new microservice was deployed, and your Elasticsearch indexing rate tripled overnight. Disk space is at 90%, and the cluster is slowing down.

Question: What are your immediate steps to save the cluster, and how do you prevent this in the future?

Key Answer Points: Immediate: Identify the "noisy" index and increase the refresh_interval or temporarily disable replicas. Long-term: Implement Index Lifecycle Management (ILM) and set up Quotas or "Rate Limiting" at the ingest layer (like Logstash or Fluentd).

2. The "Blind Spot" Scenario:

Context: The SRE team reports that an application is crashing, but the Grafana dashboards show "Healthy" (Green) and there are no logs in Kibana.

Question: Where is the breakdown in the observability pipeline likely happening?

Key Answer Points: Check the Log Shipper (Filebeat/Fluentbit) on the node; it might be stuck or OOM-killed. Check the Network (firewall/ACL) between the app and the collector. Finally, verify if the application is writing to stdout or a file that isn't being watched.

3. The "Cost vs. Visibility" Scenario:

Context: Management wants to reduce the cloud bill for observability by 30% without losing the ability to investigate incidents from last month.

Question: How would you re-architect the storage?

Key Answer Points: Implement Hot-Warm-Cold architecture. Move data older than 7 days to "Cold" storage (S3/Object storage) using Searchable Snapshots. Implement Log Sampling for high-volume, low-value logs (like 200 OK access logs).

4. The "Upgrade Anxiety" Scenario:

Context: You need to upgrade a production Elasticsearch cluster from 7.10 to 8.x with zero downtime.

Question: What is your step-by-step execution plan?

Key Answer Points: Run the Upgrade Assistant, check for deprecated settings, perform a Rolling Upgrade (one node at a time), disable shard allocation during node restarts to prevent unnecessary data shuffling, and ensure a full Snapshot is taken before starting.

5. The "Alert Fatigue" Scenario:

Context: Developers are ignoring Slack alerts because they receive 500 "CPU High" notifications a day, most of which resolve themselves in minutes.

Question: How do you fix the alerting strategy?

Key Answer Points: Switch from "Static Thresholds" to Percentiles or Rate of Change. Use Alert Grouping in Alertmanager. Implement a "Silence" window for known flapping services and ensure alerts are "Symptom-based" (User-facing error) rather than "Cause-based" (High CPU).

### Hands-on: Troubleshooting Scenarios

1. Scenario: The "Unassigned Shards" Mystery

Problem: You run GET _cluster/health and it returns status: red with 12 unassigned shards.

Troubleshooting Task: How do you find out why they aren't assigning?

Command/Solution: Use GET _cluster/allocation/explain. This API tells you exactly why a shard isn't moving (e.g., "node disk threshold exceeded" or "too many shards on this node").

2. Scenario: The "Prometheus Gap"

Problem: Your Grafana charts for a specific K8s namespace have "gaps" or missing data points every few minutes.

Troubleshooting Task: Where do you look first?

Command/Solution: Check Prometheus Scrape Intervals vs. App Response Time. If the app takes 12s to respond to a /metrics call but Prometheus has a 10s timeout, the scrape fails. Check up metric in Prometheus to see the scrape health.

3. Scenario: The "K8s Zombie Pod"

Problem: A pod is deleted in Kubernetes, but your monitoring agent still reports it as "Active" and "High Memory."

Troubleshooting Task: Is the agent lying, or is the pod still there?

Command/Solution: Check the Kubelet on the specific node. It might be a "Zombie Process" where the container exited but the PID is still in the process table. Use crictl ps or docker ps directly on the node to verify the actual state vs. what the K8s API thinks.

4. Scenario: Elasticsearch "Circuit Breaker" Tripping

Problem: Users are getting 429 Too Many Requests or CircuitBreakingException when running Kibana searches.

Troubleshooting Task: Identify if this is a RAM issue or a Query issue.

Command/Solution: Check GET _nodes/stats/jvm. Look at the Fielddata and Request breakers. If Fielddata is high, someone is running aggregations on un-optimized text fields. Solution: Clear cache or fix the index mapping.

5. Scenario: High "I/O Wait" on a Logging Node

Problem: Linux top shows 40% wa (I/O Wait), and log ingestion latency is climbing.

Troubleshooting Task: Is it a bad disk or too many writes?

Command/Solution: Use iostat -xz 1 to see disk utilization. Use iotop to find the specific process (Elasticsearch or Logstash) causing the writes. If the "Queue Depth" is high, the underlying storage (EBS/SAN) cannot handle the IOPS.


## Operational Excellence & ITSM

1. Question: You receive an alert for "Disk 90% full" on a production logging node. What is your immediate and long-term action?

Answer: Immediate: Check for orphaned logs, temporary files, or trigger an ES "Force Merge" / delete old indices via ILM (Index Lifecycle Management). Long-term: Update the Retention Policy or increase disk PVC size.

2. Question: How do you handle a "Change Management" process for a critical Elasticsearch upgrade?

Answer: I would first test in a staging environment, create a detailed Rollback Plan, perform a snapshot/backup, and then use a "Rolling Upgrade" strategy to ensure zero downtime for users.

3. Question: What is the difference between an "Incident" and a "Problem" in ITSM?

Answer: An Incident is an unplanned interruption (the site is down). A Problem is the underlying cause (the site keeps going down because of a memory leak). Solving the Problem prevents future Incidents.

4. Question: How do you ensure "High Availability" for a Grafana dashboarding service?

Answer: Deploy Grafana in a multi-replica mode behind a Load Balancer, and move the Grafana SQLite database to an external highly available database like PostgreSQL or MySQL.


5. Observability tools slow during peak hours.

Enable caching
Scale horizontally
Optimize queries
Archive cold data
Monitor JVM heap

7. Elasticsearch query latency increased drastically.

Check slow logs
Review shard count
Optimize query filters
Use index templates
Increase replicas

8. Prometheus server running out of memory.

Reduce retention period
Increase resources
Use remote storage
Optimize scrape intervals

9. Multiple teams blame infrastructure for application issue.

Gather metrics & logs evidence
Validate infra health
Share dashboards
Focus on data-driven discussion
Collaborate toward root cause

3. Change deployed without approval causes outage.

Rollback immediately
Restore service
Log change violation
Conduct CAB review
Improve governance

4. Recurring incident happening weekly.

Identify pattern
Perform deep RCA
Propose permanent fix
Automate prevention
Update monitoring

5. Hardware failure impacts Elasticsearch node.

Check cluster status
Confirm replica availability
Replace hardware
Rejoin node
Rebalance shards

1. You receive a Sev1 alert at 2 AM. What do you do?

Acknowledge alert
Join bridge call
Identify impacted services
Provide updates every 15–30 mins
Engage SMEs
Document timeline
Post-incident RCA

3. Distributed tracing shows latency spike in one microservice.

Identify slow spans
Check DB calls
Analyze external API calls
Verify CPU/memory usage
Root cause may be downstream dependency.

4. Logs are increasing 5x suddenly.
Investigate:
New deployment?
Debug logging enabled?
Traffic spike?

Solution:
Adjust log level
Apply rate limiting
Update ILM policy


# ## # Dynatrace # ## # 
Dynatrace Platoform architecture
1. Data Collector - collects from Agents, API's, Direct Integrations
Agents - install on systems
API's - pull data from 3rd party tools
Direct Integrations - pull data from 3rd party tools without agents on AWS, K8
2. Proxy - Called as ActiveGate, Sits between OneAgent & server cluster 
Streamlines communication, reduces load on OneAgent, and provides security by acting as a gateway for data transmission.
3. Server Cluster-
   Cluster of server for monitoring environment
   Can sit in Dynatrace or your organization environment (on-prem or cloud)

** Dynatrace Deployment scenarios - 
1. Dynatrace SaaS - Dynatrace hosts and manages the monitoring infrastructure for you. You simply install the OneAgent on your servers, and all data is sent to Dynatrace's cloud platform for processing and visualization. This option is easier to set up and maintain, but you have less control over the data and infrastructure.
2. Dynatrace Managed - You can deploy Dynatrace in your own environment (on-prem or cloud) and manage it yourself. You have full control over the infrastructure and data, but you are responsible for maintenance, updates, and scaling. 

# Licensing  & Billing Model -
Pricing Model - COnsumption based pricing
Pay based on 3 types of consumptiion units - host, Digital Experience Monitoring(DEM), (Davis) Data units
Subscription fee - Pay subscription for what your env consumes


# Dynatrace APM=
Monitors web, mobile, DB apps 
Monitors different programming languages - Java, .NET, Node.js, Python, PHP, Go, Ruby

1. DB Monitoring - 
Monitoring identifies errors, slow quries and bottlenecks
OneAgent installed on host automatic montioring
Colletcs DB metrics sucha as availablity, response time 
Understnads DB impatc on overall application performance

2. Code Level Montiroing with Dynatrace- 
3. Service Monitoring with Dynatrace - Microservices, Monoliths, Serverless

# Dynatrace Infrastructure Monitoring -
1. Host Monitoring - CPU, Memory, Disk, Network
2. Network Monitoring - Traffic, Errors, Latency
3. Log Monitoring - Centralized log management, log analytics, log correlation with metrics and traces

# Dynatrace DEM Defined
Web, Mobile, API's
Can be called EUM. UEM. EUEM

1. Real user Monitoring with Dynatrace - Monitors actual user interactions with web and mobile applications. Provides insights into user behavior, performance, and experience. Helps identify issues that impact end-users.