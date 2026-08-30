
1. The "Silent Killer": Database Connection Pool Exhaustion
The Scenario: During a marketing flash sale, the primary API began returning 500 errors. Monitoring showed CPU and Memory were fine, but response latency spiked to 30 seconds before timing out.

Rapid Troubleshooting: You noticed a "Thread Starvation" pattern in the logs. While the DB was healthy, the application couldn't get a connection.

Quick Mitigation: Instead of waiting to tune the code, you implemented a temporary rate-limit at the Load Balancer (NGINX/Envoy) to drop 20% of non-critical traffic. This allowed the connection pool to breathe and kept the checkout service alive for the remaining 80% of users.

Teamwork & Reliability: You coordinated with the Backend team to identify a "leaky" transaction in a new loyalty points feature. Post-mitigation, you led the effort to implement exponential backoff on the client side to prevent future thundering herds.

2. The "Regional Brownout": DNS/CDN Latency Spike
The Scenario: Users in Western Europe reported that the web app was "hanging" on a white screen. Global health checks were green, but localized synthetics showed 10-second delays fetching static assets.

Rapid Troubleshooting: You used mtr and dig to trace the path and realized a specific CDN Edge POP (Point of Presence) was routing traffic through a congested backbone provider.

Quick Mitigation: Under pressure, you performed a DNS Failover, updating the CNAME records to point static traffic to a secondary CDN provider (e.g., switching from Cloudflare to Akamai or AWS CloudFront). This bypassed the regional congestion in minutes.

Responsibility: You didn't wait for the CDN provider's support ticket; you prioritized user uptime. Later, you automated this "Circuit Breaker" pattern for DNS so it could trigger automatically based on regional latency thresholds.

3. The "Resource Quota Wall": Kubernetes OOMKills
The Scenario: Following a Friday afternoon deployment, a critical microservice began "flapping"—restarting every 5 minutes. This caused a domino effect, as the service was a dependency for the login flow.

Rapid Troubleshooting: You checked kubectl describe pod and saw Reason: OOMKilled. However, the memory limit was already set to what should have been a safe margin.

Quick Mitigation: Rather than rolling back immediately (which might have lost critical new data migrations), you manually patched the Deployment to double the memory limits and added a temporary priorityClassName to ensure these pods weren't evicted from nodes. This stabilized the login flow in under 4 minutes.

Root Cause Analysis (RCA): After the fire was out, you discovered a memory leak in a new telemetry library. You worked with the Dev team to profile the heap and provided the fix, while also setting up "Vertical Pod Autoscaler" (VPA) in recommendation mode to catch these trends earlier.



# Scenario: The "Silent Killer" (Database Connection Pool Exhaustion)
Situation:
"During a high-traffic flash sale, our primary checkout API began throwing 500 errors. Monitoring showed that while the Database CPU was low (around 15%), the application response times spiked from 200ms to over 30 seconds. We were losing thousands of dollars in transactions every minute."

Task:
"My task was to stabilize the environment immediately to allow successful checkouts, identify why the application couldn't talk to a perfectly healthy database, and prevent a total system collapse."

Action (The "SRE" Muscle):

Rapid Diagnosis: I checked the application logs and identified SQLTransientConnectionException. I realized the DB wasn't slow; the app simply couldn't get a 'seat at the table' because all connections were hung.

Quick Mitigation: Instead of trying to patch code during the peak, I implemented a 30% rate-limit at the Edge Gateway. This felt counter-intuitive to the business, but by dropping 30% of requests, the remaining 70% of users could actually complete their purchases instead of 100% of users seeing timeouts.

Teamwork: I hopped on a bridge with the Lead Dev. We identified that a new 'Related Products' query didn't have a timeout set, causing threads to hang indefinitely. We manually killed those long-running sessions in the DB to flush the pool.

Result:
"The error rate dropped to near zero within 2 minutes of the rate-limit being applied. Once the 'Related Products' feature was toggled off via a feature flag, we removed the rate limit. Later, I led a post-mortem where we implemented standardized connection timeouts across all microservices and added 'Pool Saturation' alerts to our Grafana dashboards so we’d see this coming next time."

1. The "Resource Quota Wall" (Kubernetes OOMKills)
Focus: Rapid mitigation, understanding container orchestration, and preventing regression.

Situation: Thirty minutes after a Friday afternoon deployment, a critical authentication microservice began "flapping." Pods were crashing and restarting every 3 to 5 minutes, causing a 40% drop in successful logins.

Task: I needed to stop the restart loop immediately to restore login functionality without performing a full rollback, as the deployment included a non-backward-compatible database schema migration.

Action: * Diagnosis: I ran kubectl describe pod and identified the Last State: Terminated with Reason: OOMKilled.

Mitigation: Rather than waiting for a code fix, I hot-patched the Kubernetes Deployment to double the memory limits and added a priorityClassName to ensure these pods weren't evicted from nodes during the surge.

Teamwork: I coordinated with the developers to identify a memory leak in a new telemetry library.

Result: The service stabilized within 4 minutes of the resource adjustment. Post-incident, I implemented Vertical Pod Autoscaler (VPA) in "Recommendation Mode" to provide better visibility into actual usage vs. limits, preventing "blind" limit setting in the future.

2. The "Regional Brownout" (CDN/Network Latency)
Focus: Global traffic management, vendor reliability, and high-pressure decision making.

Situation: Users in Western Europe reported that the web application was "hanging" on a white screen. Our global health checks were green because the origin server was fine, but localized synthetic tests showed 10-second delays fetching static assets (JS/CSS).

Task: I had to bypass a regional network degradation that was outside of our direct infrastructure control (a third-party CDN issue).

Action: * Diagnosis: Using mtr and curl -v, I traced the latency to a specific CDN Edge POP (Point of Presence) that was routing traffic through a congested backbone provider.

Mitigation: I performed an Emergency DNS Failover, updating our CNAME records to point static traffic to our secondary CDN provider. This effectively "routed around" the regional congestion.

Responsibility: I communicated the status to the stakeholders via the status page and stayed on the line with the CDN provider to confirm the root cause.

Result: Global latency returned to normal levels within the DNS TTL window (approx. 5 minutes). We later automated this "Circuit Breaker" pattern for our DNS to trigger automatically if regional latency exceeds a 2-second threshold.

3. The "Stateful Mess" (Disk Space Exhaustion on a Legacy DB)
Focus: Data integrity, cross-team communication, and quick thinking to avoid data loss.

Situation: A legacy logging database started failing as it hit 98% disk utilization. If it hit 100%, the database would have corrupted its write-ahead logs (WAL), leading to potential data loss and a multi-hour recovery process.

Task: I had to clear space immediately without deleting critical business data or taking the DB offline for a volume expansion (which required a restart).

Action: * Quick Mitigation: I identified non-critical "Audit" tables from three years ago that hadn't been accessed in months. I used TRUNCATE instead of DELETE on a temp table to free up blocks immediately, then compressed older log segments at the OS level to gain a 15% buffer.

Teamwork: I worked with the Data Engineering team to verify which tables could be safely archived to S3/Cold storage.

Long-term Fix: I set up a Log Rotation and S3 Archival script (using logrotate and aws s3 sync) to ensure the disk never exceeded 80% again.

Result: We avoided a database crash and potential data corruption. The "quick win" of compressing files saved us the 4-hour window needed for a full volume migration.







1) Sev‑1 Outage After Release: Misconfigured Feature Flag Caused API 500s
S (Situation)
We rolled out a minor release for our customer-facing API. Within minutes, error rates spiked to ~18% 5xx, checkout failures rose, and support began receiving calls. The release was tied to a new feature flag for a pricing service integration.
T (Task)
As the release manager on‑call, I had to restore service quickly, identify the root cause, and ensure no data corruption or unintended billing.
A (Action)

Activated incident response: set Sev‑1 bridge, assigned roles (commander, comms, scribe).
Used Grafana/New Relic to confirm 5xx spike; drilled into Kibana/Splunk traces to isolate failing endpoints.
Correlated spike with the deployment window in Jenkins and compared config diffs in Git.
Found feature flag enabled in prod with default=ON due to an environment variable mismatch.
Executed a fast mitigation: toggled the flag OFF in prod via the feature flag service; performed a canary health check.
Cleared retry queues, validated idempotency on impacted requests, and checked billing consistency via backfill scripts.
Opened a Change record and Problem ticket in ServiceNow, and documented a post-incident review (PIR).

R (Result)

MTTR: 21 minutes from detection to recovery; error rate returned to baseline (<0.5%).
Zero data loss; 37 impacted transactions were replayed successfully.
Implemented a hotfix to enforce default=OFF for all unreleased features in prod.
Rolled out flag drift detection in CI to fail builds if prod config differs from approved templates.

What I’d improve next time

Add pre-prod chaos toggle tests for critical flags.
Enforce two-person review for production flag changes.
Include feature flag state in the release checklist and automated change validation.

Tools mentioned: Jenkins, Git, Grafana, New Relic, Kibana/Splunk, ServiceNow, LaunchDarkly/Unleash (feature flags), Jira.

2)  Database Lock Contention Post-Deployment: Elevated Latency & Timeouts
S (Situation)
After deploying a schema change and a new reporting job, we observed p95 latency jumping from 220ms to 1.8s, and intermittent gateway timeouts on the orders service during peak traffic.
T (Task)
Diagnose and reduce latency without rolling back the entire release, if possible, since other fixes were bundled.
A (Action)

Used APM traces (New Relic/Datadog) to identify hotspots; saw increased waits on ROW-level locks in the orders table.
Queried pg_stat_activity/performance_schema (DB-specific) to confirm lock queues and blocking sessions.
Reviewed the migration script: a missing index on a new column used by the reporting job’s JOIN caused sequential scans and prolonged locks.
Mitigation:

Paused the reporting job via job scheduler (Airflow/Quartz) to relieve pressure.
Created the missing index concurrently to avoid long locks.
Tuned the reporting query: LIMIT + pagination, reduced transaction scope, and read replicas for analytics.


Post-fix: Re-enabled the job with rate limits and scheduled it off-peak.
Captured learnings in DB migration runbook and pre-release DB checklist.

R (Result)

p95 latency back to ~230ms within 35 minutes; timeouts eliminated.
Reporting job runtime reduced by 62%; no customer-facing downtime required.
Instituted “migrations with explain plan” policy and index presence checks in CI.

What I’d improve next time

Mandate read-only replicas for heavy analytics.
Add pre-prod load tests including scheduled jobs, not just API traffic.
Require EXPLAIN (ANALYZE, BUFFERS) in migration PRs for queries touching hot paths.

Tools mentioned: New Relic/Datadog, Postgres/MySQL introspection, Airflow/Quartz, Git/Jenkins, Load testing (k6/JMeter), Jira/ServiceNow.

3) CI/CD Pipeline Failure Blocking Hotfix Rollout
S (Situation)
A critical hotfix needed immediate deployment, but the Jenkins pipeline failed on the “validate artifacts” stage. The failure blocked all prod deployments during a live incident.
T (Task)
Unblock the pipeline, ensure artifact integrity, and ship the hotfix safely.
A (Action)

Inspected the failed stage logs: checksum verification against our artifact repository (Nexus/Artifactory) was mismatched.
Compared SBOM/manifest and confirmed the artifact was correct locally; issue traced to a corrupted proxy cache in the artifact repo.
Short-term bypass: switched the pipeline to pull from a trusted mirror (approved DR repo), and re-ran the pipeline with pinned versions.
Added an extra verification step: signature validation using Cosign and policy checks with OPA/Conftest before deploy.
Coordinated change approval on the incident bridge and executed a progressive rollout (10% → 50% → 100%) with automated health gates in canary.
Post-incident: Rebuilt the repo cache, added health checks for repo integrity, and implemented pipeline circuit breakers that suggest the mirror automatically when primary integrity checks fail.

R (Result)

Hotfix deployed in 28 minutes after initial failure; reduced customer impact window by ~40%.
No integrity regressions; pipeline stability improved, decreasing false failures by ~70% over the next sprint.
Improved DR readiness and supply-chain verification posture.

What I’d improve next time

Scheduled artifact repository cache validation and checksum audits.
Maintain active-active mirrors for critical components.
Add runbooks for “pipeline down, hotfix needed” scenarios.

Tools mentioned: Jenkins, Nexus/Artifactory, Cosign, OPA/Conftest, Canary/feature gates, ServiceNow/Jira, Slack/Teams incident bridge.

How to Present These in an Interview (Quick Tips)

Lead with impact: error rate, MTTR, latency improvements, customer outcomes.
Name the tools: recruiters listen for real-world tooling (Grafana, Splunk, Jenkins, LaunchDarkly, Datadog/New Relic).
Show process maturity: incident roles, PIRs, checklists, automations.
Close with prevention: policies, tests, monitoring, and guardrails you implemented.

#
#######################################################################################

1️⃣ High CPU Usage Causing Application Slowness

Situation:
In our production environment, users suddenly started reporting that the application was extremely slow. Monitoring dashboards showed that one of our application servers had CPU usage consistently above 95%.

Task:
As part of the support team, my responsibility was to identify the root cause quickly and restore system performance because the issue was impacting multiple customers.

Action:

First, I checked application logs and monitoring tools to identify abnormal behavior.
I noticed a specific API endpoint receiving an unusually high number of requests.
Using query monitoring, I found a database query without proper indexing, which was causing full table scans.
I immediately worked with the DBA team to add the required index.
As a temporary mitigation, we also scaled the application instances to distribute the load.

Result:

CPU utilization dropped from 95% to around 40%.
Application response time improved significantly.
Later we added query optimization and monitoring alerts to prevent similar issues in the future.

2️⃣ Payment Failure Due to Third-Party API Timeout

Situation:
During a peak sales period, users started reporting that payments were failing intermittently on our platform.

Task:
Our task was to identify whether the issue was internal or related to an external payment provider and ensure transactions were not lost.

Action:

I checked application logs and error responses and noticed repeated timeout errors from the payment gateway API.
I verified network latency and confirmed the issue was coming from the third-party payment provider.
To reduce impact, we implemented a retry mechanism with exponential backoff.
We also enabled a fallback payment gateway while coordinating with the provider's support team.

Result:

Payment success rate increased from 70% back to 98%.
Revenue loss during the incident was minimized.
Later we implemented circuit breaker patterns and better API monitoring to handle external dependency failures.

3️⃣ Deployment Failure After Production Release

Situation:
After a scheduled production deployment, the application started returning 500 Internal Server Errors for several endpoints.

Task:
My responsibility was to quickly identify the issue and restore service availability since the release had just gone live.

Action:

I checked the deployment logs and application logs in the monitoring tool.
The errors pointed to a missing environment configuration variable required by the new feature.
Since the feature depended on this configuration, we immediately rolled back the deployment to the previous stable version.

Then we updated the configuration in the environment and performed a proper deployment validation in staging before redeploying.

Result:

Service was restored within 15 minutes through rollback.
After fixing the configuration issue, the deployment was completed successfully.
We improved the deployment checklist and automated configuration validation in the CI/CD pipeline.

✅ Interview Tip:
End each production incident with what you improved afterward (monitoring, alerts, automation). Interviewers love hearing about preventive actions, not just fixes.



1️⃣ Kubernetes Pod CrashLoopBackOff (Memory Leak)
Situation
During a peak traffic period, one of our microservices running in Kubernetes started going into CrashLoopBackOff, causing multiple API failures.

Task
As the on-call engineer, I needed to identify the root cause quickly and restore service availability.

Action
Checked pod status using kubectl get pods and saw repeated restarts.
Used kubectl describe pod and logs to analyze the issue.
Found OutOfMemoryError in the logs indicating a memory leak.
Verified metrics from monitoring (Prometheus/Grafana) showing memory usage continuously increasing.
Increased the memory limit temporarily to stabilize the service.
Later worked with developers to fix the memory leak in the code and redeployed the service.

Result
Service stability restored within 10 minutes.
Added memory usage alerts and autoscaling rules to prevent similar issues.

# Real incidents carry much more weight than textbook answers.
For example:
"We had a Kubernetes deployment where pods kept restarting due to a memory leak. We identified OOMKilled events, correlated metrics with application logs, rolled back the release, and later fixed the memory management issue."

2️⃣ Microservice Communication Failure (Service Discovery Issue)
Situation
Users reported that some features were failing because the Order Service could not communicate with the Inventory Service.
Task
Investigate and restore inter-service communication in the microservices architecture.

Action
Checked application logs and saw connection refused errors.
Verified Kubernetes services and endpoints.
Found that the Inventory service pods were running but not registered properly in service discovery due to a failed readiness probe.

Fixed the readiness probe configuration and restarted the deployment.
Verified traffic routing through the service mesh.

Result
Communication between services restored within 20 minutes.
Updated health check configuration and improved deployment validation tests.

3️⃣ Kubernetes Node Failure (Cluster Resource Issue)
Situation
Several pods across multiple services suddenly went down, impacting multiple features in production.

Task
Identify whether the issue was application-level or infrastructure-level.

Action
Checked cluster status using kubectl get nodes.
Noticed one worker node was in NotReady state.
Investigated node logs and discovered disk pressure caused by excessive container logs.
Drained the node and rescheduled pods to healthy nodes.
Cleared unused container logs and implemented log rotation.

Result

Pods rescheduled automatically and services recovered.
Added log retention and disk monitoring alerts to avoid future node failures.

4️⃣ CI/CD Pipeline Deployment Broke Production
Situation

After a CI/CD deployment, the application started failing health checks and traffic was not being routed to new pods.

Task
Restore service quickly and determine why the deployment failed.

Action
Checked deployment status and logs.
Found that a database migration script failed, but the application version had already been deployed.
Initiated rollback to the previous stable version.
Fixed the migration script and re-tested in staging.
Updated pipeline to run migrations before application deployment.

Result
Production restored within 15 minutes.
CI/CD pipeline updated to prevent similar issues.

5️⃣ Cloud Load Balancer Misconfiguration
Situation
Users reported intermittent 502/504 gateway errors after a new service release.

Task
Investigate the root cause and stabilize traffic routing.

Action
Checked application logs but saw no internal errors.
Investigated cloud load balancer metrics.
Found incorrect health check path configured for the new service.
Updated health check configuration and verified backend instance health.

Result
Error rate dropped immediately.
Implemented automated validation in the deployment pipeline for load balancer health checks.
DevOps Production Issues (Common Interview Questions)

These are very frequently asked scenarios.

🔹 1. High latency in production
Steps:
Check monitoring dashboards (CPU, memory, latency).
Analyze logs.
Identify bottleneck (DB query, network, API dependency).
Scale resources or optimize queries.

🔹 2. CI/CD pipeline failing

Steps:
Check pipeline logs.
Identify failing stage (build, test, deploy).
Verify dependencies or environment variables.
Fix configuration and rerun pipeline.

🔹 3. Application not starting after deployment
Steps:
Check container logs
Verify environment variables
Check DB connectivity
Validate configuration files
Real Incident Examples Asked in FAANG Interviews
Companies like Amazon, Google, Meta, Netflix often ask these types:

1️⃣ “Production system suddenly becomes slow. What do you do?”

Expected approach:
Check monitoring metrics
Identify affected services
Check logs and traces
Identify bottleneck (CPU, DB, network)
Mitigate by scaling or rolling back

2️⃣ “Database suddenly stops responding”

Steps:
Check DB CPU/memory usage
Identify long-running queries
Kill problematic queries
Scale read replicas or add indexes

3️⃣ “Kubernetes pods keep restarting”

Steps:
Check pod logs
Check events (kubectl describe)
Check resource limits
Identify crash cause (config, memory, dependency)
How to Answer: “Tell me about a Sev1 Production Issue”

A Sev1 incident means critical outage affecting many users.

Interviewers expect structured thinking and calm incident handling.

Best Answer Structure
1️⃣ Situation
Explain what happened.
Example:
We experienced a Sev1 production incident where the main checkout service went down during peak traffic.

2️⃣ Impact
Explain business impact.
Example:
Users were unable to complete purchases, which directly affected revenue.

3️⃣ Actions Taken
Explain troubleshooting steps:
Checked monitoring dashboards
Investigated logs
Identified root cause
Applied mitigation (rollback/scale/restart)

4️⃣ Resolution
Example:
We rolled back the deployment and restored the service within 20 minutes.

5️⃣ Prevention
Always end with improvement.
Example:
After the incident, we added better monitoring alerts and automated rollback mechanisms.
Example Short Interview Answer

In one Sev1 incident, our checkout microservice went down due to a failed deployment configuration. I was part of the on-call team and quickly checked monitoring dashboards and application logs. We identified that a missing environment variable caused the service to crash. To restore service quickly, we rolled back to the previous stable version. Once the system stabilized, we fixed the configuration and redeployed after proper validation. The issue was resolved within 15 minutes, and afterward we added configuration validation checks in the CI/CD pipeline to prevent similar incidents.

✅

10 Real Production Incidents Used in Big Tech Interviews
1️⃣ Sudden Traffic Spike (Autoscaling Failure)

Scenario:
A marketing campaign suddenly increases traffic by 10x, and the application starts returning 503 errors.

What interviewer expects:
Steps:
Check load balancer metrics
Verify autoscaling status
Check CPU / memory utilization
Scale instances manually if autoscaling fails
Add autoscaling rules
Root cause examples
Autoscaling threshold too high
Instance launch delay
Database bottleneck

2️⃣ Database Suddenly Slow

Scenario:
Users report pages taking 10+ seconds to load.

Debugging steps:
Check DB CPU usage
Identify slow queries
Look for table locks
Analyze recent schema changes
Possible root cause
Missing index
Long-running query
Deadlocks

3️⃣ Kubernetes Pods Restarting

Scenario:
Multiple pods show CrashLoopBackOff.

Steps:
kubectl describe pod
Check container logs
Verify environment variables
Check resource limits
Common causes
Memory limit exceeded
Missing config
Dependency failure

4️⃣ External API Dependency Failure

Scenario:
A microservice depends on a third-party API that suddenly becomes slow.

Expected solution:
Implement retry mechanism
Add circuit breaker
Add timeouts
Use fallback service

5️⃣ Memory Leak in Production

Scenario:
Memory usage slowly increases until the service crashes.
Debugging approach:
Monitor memory metrics
Check heap dumps
Identify object retention
Fix code and redeploy

6️⃣ Cache Failure (Redis / Memcached)

Scenario:
Cache cluster goes down and database load spikes.
Solution approach:
Verify cache cluster health
Restart cache nodes
Temporarily scale database
Add cache redundancy

7️⃣ Deployment Broke Production

Scenario:
After a deployment, all APIs return 500 errors.

Approach:
Check logs
Identify failing component
Rollback deployment
Fix issue in staging
Redeploy

8️⃣ Disk Full on Server

Scenario:
Application suddenly stops writing logs.

Debugging:
Check disk usage
Identify large files
Remove unnecessary logs
Implement log rotation

9️⃣ Load Balancer Returning 502 Errors

Scenario:
Users receive intermittent 502/504 errors.
Debugging:
Check backend instance health
Verify health check path
Check timeout settings

🔟 Network Latency Between Microservices

Scenario:
Service-to-service communication becomes slow.

Steps:
Check network latency metrics
Verify DNS resolution
Check service mesh logs
Identify network bottleneck
Most Common Production Debugging Questions (with Answers)

These are asked very frequently.

1️⃣ “How do you debug a slow production system?”
Best answer structure:
Check monitoring metrics
CPU
memory
network
Identify affected services
Analyze logs and traces
Check database queries
Scale resources or fix bottleneck

2️⃣ “Application is returning 500 errors. What do you do?”

Steps:
Check application logs
Identify stack trace
Check recent deployments
Verify environment variables
Rollback if needed

3️⃣ “Website is down. How will you troubleshoot?”

Approach:
Check DNS resolution
Check load balancer health
Check application servers
Check database connectivity
Check logs

4️⃣ “CPU usage suddenly becomes 100%”

Steps:
Identify process using CPU
Analyze logs and threads
Check recent code changes
Scale instances if needed

5️⃣ “Users cannot login”

Possible checks:
Authentication service health
Database connectivity
Token expiration
Third-party auth provider

6️⃣ “Kubernetes pods not receiving traffic”

Steps:

Check service endpoints
Verify readiness probes
Check ingress configuration
Check load balancer routing

7️⃣ “CI/CD deployment failed”

Steps:
Check pipeline logs
Identify failing stage
Fix configuration issue
Re-run pipeline

8️⃣ “Database connection pool exhausted”
Steps:
Check connection usage
Identify long-running queries
Increase pool size
Fix connection leaks

9️⃣ “High latency in API response”
Steps:
Trace request path
Identify slow service
Check database queries
Optimize caching

🔟 “Service running but users cannot access it”

Steps:
Verify DNS
Check firewall rules
Check load balancer
Verify application port
Golden Rule Big Tech Interviewers Look For
They expect structured debugging thinking:

1️⃣ Identify impact
2️⃣ Check monitoring metrics
3️⃣ Analyze logs
4️⃣ Isolate failing component
5️⃣ Apply mitigation (rollback/scale)
6️⃣ Fix root cause

✅ 


# How would you do automatic failover incase of regional/Zonal failure in AWS?
Explain your failover architecture using Route 53, health checks, automatic traffic shift and multi-region deployment. For example:

To handle zonal failures, I deploy the application across multiple Availability Zones behind an ALB and Auto Scaling Group. If one AZ fails, the ALB health checks remove unhealthy instances and traffic automatically shifts to healthy AZs.

For regional failures, I deploy the application in two AWS regions. Route 53 is configured with a failover routing policy and health checks on the primary region. When the health check fails, Route 53 automatically redirects DNS traffic to the secondary region. For data, I use Aurora Global Database or DynamoDB Global Tables for cross-region replication. I keep DNS TTL low, around 30–60 seconds, to reduce failover time. This provides automatic recovery from both zonal and regional outages with minimal downtime and data loss.

# How would you do automatic failover incase of Database failure in AWS?

Application
     |
   RDS
(Primary DB)
     |
Standby DB
(Multi-AZ)

Enable RDS Multi-AZ
AWS maintains a synchronous standby in another AZ.
Application connects using a single RDS endpoint.


For database instance failures, I enable RDS Multi-AZ deployment. AWS automatically maintains a synchronous standby in another Availability Zone. If the primary database becomes unavailable, AWS automatically promotes the standby and updates the database endpoint, minimizing downtime without requiring application changes.

Primary DB Crash
      ↓
AWS detects failure
      ↓
Standby promoted automatically
      ↓
DNS endpoint updated
      ↓
Application reconnects


5. Connection Failover at Application Layer

A common interview follow-up:

# "How does the application reconnect?"
Best practice:

Application
     |
Connection Pool
     |
Database Endpoint

Use:

Connection pooling
Retry logic
Exponential backoff
DNS refresh support

# What benefits provides by kubernetes so many organizations are moving to kubernetes?
1. Container Orchestration: Kubernetes automates the deployment, scaling, and management of container applications, making it easier to run and manage applications in a consistent environment.
2. Scalability: Kubernetes can automatically scale applications up or down based on demand, ensuring optimal
3. Resource Utilization: Kubernetes efficiently manages resources by scheduling containers based on their resource requirements and availability, leading to better utilization of infrastructure.
4. High Availability: Kubernetes provides features like self-healing, automatic restarts, and replication to ensure applications remain available even in the face of failures.
5. Portability: Kubernetes abstracts away the underlying infrastructure, allowing applications to run consistently across different environments, whether on-premises, in the cloud, or in hybrid setups.
6. Ecosystem and Community: Kubernetes has a large and active community, providing a rich ecosystemof tools, extensions, and integrations that enhance its functionality and make it easier to adopt.
7. DevOps and CI/CD Integration: Kubernetes integrates well with DevOps practices and CI/CD pipelines, enabling faster development cycles and continuous deployment of applications.
8. Microservices Architecture: Kubernetes is well-suited for managing microservices-based applications, allowing for better organization, scaling, and maintenance of complex applications.
9. Security: Kubernetes provides features like role-based access control (RBAC), secrets management,    and network policies to enhance the security of applications and infrastructure.
10. Cost Efficiency: By optimizing resource utilization and enabling efficient scaling, Kubernetes can help reduce infrastructure costs while maintaining performance and availability.
11. Cloud Agnostics : Kubernetes can run on any cloud provider or on-premises, giving organizations the flexibility to choose their infrastructure without being locked into a specific vendor.
12. Automation: Kubernetes automates many operational tasks, such as rollouts, rollbacks,   and self-healing, reducing the manual effort required to manage applications and infrastructure. 



## If application throwing 500 error for clients, what could be the issues & debug steps?

It’s a generic error, so the real issue is almost always in your backend stack. The key is to systematically isolate where it’s breaking

1.   Application code failure
Unhandled exceptions (null pointer, index error, etc.) or can be bug

2. Database issues
DB down / unreachable
Connection pool exhausted
Slow or failed queries
Wrong credentials

3. Permissions / file issues

4. Dependency/service failure   
   External API down
Cache (Redis/Memcached) unavailable
Internal microservice failing

5. Resource exhaustion
CPU spike , Memory full (OOM kill) / Disk full

🛠️ Step-by-step debugging approach
1️⃣ Reproduce and confirm - Use curl coammand
2. Check application logs - with tail command
3. Check Webserver logs
4. Check System resources  - top, free -m, df -h
5. Check Database connectivity - mysql -u user -p -h host
6. Check Recent Deployments - git log, Jenkins

# In canary deployment , some users are facing issue accessing the application but few users are accessible the application without any issue
Because a canary deployment splits traffic between your old version (Stable) and your new version (Canary), the users facing issues are almost certainly being routed to the new canary instances.

Most common casues:
1. Sticky Sessions (Session Persistence)The Issue: If your application relies on session data stored in memory (like a user login), users routed to the new canary server might lose their session if it doesn't share a database with the old servers.The Fix: Ensure your load balancer uses sticky sessions during the deployment, or use a distributed session store like Redis.
   
2. Database Schema MismatchesThe Issue: The new canary version might require a new database column or table that the old version doesn't support, or vice versa (breaking backward compatibility).The Fix: Ensure all database changes are backward compatible. Use the expand/contract (parallel design) pattern for database migrations.
3. Caching and CDN IssuesThe Issue: Frontend assets (JS, CSS) might be cached. Users routed to the canary backend might be requesting new assets that their browser (or a CDN) hasn't loaded yet, causing UI crashes.The Fix: Implement strict cache busting (unique file hashes) for every build. 

4. Hardcoded Environment VariablesThe Issue: The canary environment might be missing specific API keys, environment variables, or connection strings that the stable environment has.The Fix: Check the logs of the canary pods/servers specifically for 404, 500, or Connection Refused errors.