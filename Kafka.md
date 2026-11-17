

# What is Apache Kafka?
> Kafka is a distributed event-streaming platform used for asynchronous communication between applications.

Important components:
- Producer: Sends messages.
- Consumer: Reads messages.
- Broker: Kafka server.
- Topic: Logical category of messages.
- Partition: Divides topics for parallel processing.
- Consumer Group: Allows multiple consumers to share processing.
- Offset: Tracks message consumption position.

# How would you troubleshoot Kafka consumer lag?
Consumer lag occurs when consumers cannot process messages as quickly as producers publish them.

I would check:
1. Consumer processing speed.
2. Consumer group health.
3. Partition distribution.
4. Broker CPU and memory.
5. Network connectivity.
6. Consumer application logs.
7. Message processing failures.


# What is API Gateway?
An API Gateway acts as an entry point between external clients and backend services.

Common responsibilities include:
- Authentication and authorization.
- Routing.
- Rate limiting.
- SSL termination.
- Request/response transformation.
- API monitoring.
- Traffic management.
Examples include AWS API Gateway,Azure API Management.


# An API is responding slowly. How do you troubleshoot?
I would check:
1. API response time and latency.
2. Application server CPU and memory.
3. Tomcat thread pool utilization.
4. Database query performance.
5. Downstream API dependencies.
6. Network latency.
7. Application logs and distributed traces.