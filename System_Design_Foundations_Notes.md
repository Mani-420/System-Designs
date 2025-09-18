# System Design Foundations - Quick Reference Notes

## Table of Contents
1. [Client-Server Model](#1-client-server-model)
2. [Monolithic vs. Microservices Architecture](#2-monolithic-vs-microservices-architecture)
3. [Horizontal vs. Vertical Scaling](#3-horizontal-vs-vertical-scaling)
4. [CAP Theorem](#4-cap-theorem)
5. [Latency vs. Throughput vs. Bandwidth](#5-latency-vs-throughput-vs-bandwidth)
6. [Load Balancing Basics](#6-load-balancing-basics)

---

## 1. Client-Server Model

### Core Concept
- **Client**: Initiates requests (browsers, mobile apps)
- **Server**: Responds to requests (web servers, API servers)
- **Communication**: Request → Processing → Response

### HTTP Methods
- **GET**: Retrieve data (idempotent, cacheable)
- **POST**: Create new resources
- **PUT**: Update existing resources (idempotent)
- **DELETE**: Remove resources (idempotent)
- **PATCH**: Partial updates

### Response Codes
- **2xx**: Success (200 OK, 201 Created)
- **3xx**: Redirection (301 Moved Permanently)
- **4xx**: Client Error (400 Bad Request, 404 Not Found)
- **5xx**: Server Error (500 Internal Server Error)

### RESTful API Principles
- Stateless communication
- Resource-based URLs (`/api/users/123`)
- Standard HTTP methods
- JSON/XML data formats

### Benefits
- Separation of concerns
- Scalability
- Maintainability
- Reusability

### Challenges
- Network dependency
- Latency
- Security concerns

---

## 2. Monolithic vs. Microservices Architecture

### Monolithic Architecture
**Definition**: Single, unified application where all components are tightly coupled.

**Characteristics:**
- Single codebase
- Single deployment unit
- Shared database
- All components in same process
- Single technology stack

**Advantages:**
- Simpler development
- Better performance (no network calls)
- Easier debugging
- ACID transactions
- Lower complexity

**Best For:**
- Small to medium applications
- Limited DevOps experience
- Tightly coupled components
- Rapid prototyping

### Microservices Architecture
**Definition**: Application broken into small, independent services.

**Characteristics:**
- Multiple independent services
- Each service has its own database
- Independent deployment
- Different technologies allowed
- API communication

**Advantages:**
- Independent scaling
- Technology diversity
- Team autonomy
- Fault isolation
- Continuous deployment

**Best For:**
- Large, complex applications
- Multiple development teams
- High scalability requirements
- Long-term projects

### Comparison Table
| Aspect | Monolithic | Microservices |
|--------|------------|---------------|
| Development | Single team, single codebase | Multiple teams, separate codebases |
| Deployment | Deploy entire app | Deploy services independently |
| Scaling | Scale entire application | Scale individual services |
| Technology | Single stack | Multiple technologies |
| Database | Shared database | Database per service |
| Testing | End-to-end testing | Service-level testing |

### Migration Strategy
1. **Strangler Fig Pattern**: Gradually replace components
2. **Database Decomposition**: Split shared database
3. **API Gateway**: Introduce service communication
4. **Service Extraction**: Extract one service at a time

---

## 3. Horizontal vs. Vertical Scaling

### Vertical Scaling (Scale Up)
**Definition**: Increase power of existing hardware/software.

**Examples:**
- Add more CPU cores
- Increase RAM memory
- Upgrade to faster storage
- Use more powerful processors

**Advantages:**
- Simplicity (no code changes)
- Immediate results
- No network overhead
- Easier management
- Better for CPU-bound tasks

**Limitations:**
- Hardware limits
- Single point of failure
- Expensive
- Requires downtime

### Horizontal Scaling (Scale Out)
**Definition**: Add more machines/instances to the system.

**Examples:**
- Add more servers
- Deploy multiple instances
- Use load balancers
- Add database replicas
- Multiple data centers

**Advantages:**
- Unlimited growth
- Cost effective
- High availability
- Flexibility
- Better for I/O-bound tasks

**Challenges:**
- Complexity
- Data consistency issues
- Network latency
- Coordination overhead

### Comparison Table
| Aspect | Vertical Scaling | Horizontal Scaling |
|--------|------------------|-------------------|
| Complexity | Simple | Complex |
| Cost | Expensive hardware | More servers, cheaper units |
| Limits | Physical hardware limits | Limited by architecture |
| Downtime | Requires restart | No downtime |
| Fault Tolerance | Single point of failure | Better fault tolerance |
| Performance | Better for CPU-intensive | Better for I/O-intensive |

### When to Use Each
**Vertical Scaling:**
- Small to medium applications
- CPU-intensive workloads
- Shared memory requirements
- Budget for expensive hardware
- Legacy applications

**Horizontal Scaling:**
- Large-scale applications
- High-traffic websites
- Variable load
- High availability needs
- Cloud-native applications

### Hybrid Approach
Many systems use both:
- Application Layer: Horizontal scaling
- Database Layer: Vertical scaling
- Cache Layer: Horizontal scaling
- Storage Layer: Horizontal scaling

---

## 4. CAP Theorem

### Core Statement
**"In a distributed system, you can only guarantee two out of three: Consistency, Availability, and Partition Tolerance."**

### The Three Properties

#### C - Consistency
- All nodes see the same data at the same time
- Every read receives the most recent write or an error
- Data remains synchronized across all nodes

#### A - Availability
- System remains operational and responsive
- Every request receives a response (not an error)
- System continues working even if some nodes fail

#### P - Partition Tolerance
- System continues operating despite network failures
- Network partitions don't cause system failure
- System can handle network splits between nodes

### CAP Combinations

#### CA (Consistency + Availability)
- **Example**: Traditional relational databases
- **Trade-off**: Not partition-tolerant
- **Reality**: Only works in single data center
- **Problem**: Network failure = entire system failure

#### CP (Consistency + Partition Tolerance)
- **Example**: MongoDB, HBase, Redis
- **Trade-off**: Not always available
- **Behavior**: Rejects writes during partition to maintain consistency
- **Use Case**: Data accuracy is critical

#### AP (Availability + Partition Tolerance)
- **Example**: Cassandra, DynamoDB, CouchDB
- **Trade-off**: Not always consistent
- **Behavior**: Accepts writes during partition but may serve stale data
- **Use Case**: System uptime is more important

### Real-World Examples

**CP System - MongoDB:**
- During network partition: Primary continues, secondaries become read-only
- Prioritizes consistency over availability

**AP System - Cassandra:**
- During network partition: All nodes continue accepting requests
- Prioritizes availability over consistency

**CA System - Traditional Database:**
- Single data center setup
- Perfect consistency and availability
- Not fault-tolerant across locations

### When to Choose Each

**Choose CP when:**
- Data accuracy is critical (banking, financial)
- You can tolerate some downtime
- Examples: Payment systems, inventory management

**Choose AP when:**
- System uptime is more important
- Temporary inconsistencies are acceptable
- Examples: Social media feeds, content delivery

**Choose CA when:**
- Single data center
- Network partitions unlikely
- Examples: Internal enterprise systems

### Modern Approaches
- **Eventual Consistency**: System becomes consistent over time
- **Strong Eventual Consistency**: Guarantees convergence
- **BASE vs ACID**: Relaxed consistency for better availability

---

## 5. Latency vs. Throughput vs. Bandwidth

### Definitions

#### Latency
- **Definition**: Time for a single operation to complete
- **Measurement**: Milliseconds, seconds
- **Types**: Round-trip time, one-way latency, processing latency
- **Examples**: Web page load (2.5s), Database query (50ms), API call (200ms)

#### Throughput
- **Definition**: Number of operations completed per unit of time
- **Measurement**: Operations per second (ops/sec, requests/sec)
- **Types**: RPS, TPS, data throughput
- **Examples**: Web server (1000 req/s), Database (5000 queries/s)

#### Bandwidth
- **Definition**: Maximum data transmitted over network connection
- **Measurement**: Data per time (Mbps, Gbps, MB/s)
- **Types**: Network capacity, pipe size, theoretical maximum
- **Examples**: Home internet (100 Mbps), Data center (10 Gbps)

### The Relationship
**Highway Analogy:**
- **Bandwidth** = Number of lanes (capacity)
- **Throughput** = Cars passing per minute (actual usage)
- **Latency** = Time to travel from A to B (speed)

### Performance Bottlenecks

#### Latency Bottlenecks
**Causes:**
- Slow database queries
- Network round-trip time
- CPU processing time
- Disk I/O operations

**Solutions:**
- Database optimization
- Caching
- CDN usage
- Faster hardware

#### Throughput Bottlenecks
**Causes:**
- Limited server capacity
- Database connection limits
- Memory constraints
- CPU limitations

**Solutions:**
- Horizontal scaling
- Connection pooling
- Load balancing
- Performance optimization

#### Bandwidth Bottlenecks
**Causes:**
- Network capacity limits
- Large data transfers
- Multiple concurrent users
- Inefficient data formats

**Solutions:**
- Data compression
- Content optimization
- Network upgrades
- Data pagination

### Optimization Strategies

#### Reducing Latency
1. **Caching**: Store frequently accessed data in memory
2. **CDN**: Serve content from locations closer to users
3. **Database Optimization**: Indexes, query optimization
4. **Connection Pooling**: Reuse database connections
5. **Async Processing**: Non-blocking operations

#### Increasing Throughput
1. **Horizontal Scaling**: Add more servers
2. **Load Balancing**: Distribute requests across servers
3. **Connection Pooling**: Manage database connections efficiently
4. **Batch Processing**: Process multiple operations together
5. **Queue Systems**: Handle requests asynchronously

#### Optimizing Bandwidth
1. **Data Compression**: Reduce data size (gzip, brotli)
2. **Image Optimization**: Compress images, use modern formats
3. **Pagination**: Load data in chunks
4. **Lazy Loading**: Load content when needed
5. **Content Delivery**: Use CDNs for static content

### Key Takeaways
- **Latency** = How fast individual operations are
- **Throughput** = How many operations you can handle
- **Bandwidth** = How much data your network can carry
- **They're related but different**: High bandwidth doesn't guarantee low latency
- **Optimize based on requirements**: What's most critical for your use case?

---

## 6. Load Balancing Basics

### Definition
**Load Balancing**: Process of distributing network traffic across multiple servers to optimize resource utilization, maximize throughput, minimize response time, and avoid overloading any single server.

### Why Load Balancing?
- **Scalability**: Handle more users by adding servers
- **High Availability**: If one server fails, others continue
- **Performance**: Distribute load to prevent bottlenecks
- **Reliability**: Redundancy ensures system continues operating

### Load Balancer Types

#### Hardware Load Balancers
- Physical devices dedicated to load balancing
- High performance and reliability
- Expensive and less flexible
- Examples: F5 BIG-IP, Citrix NetScaler

#### Software Load Balancers
- Software applications running on servers
- More flexible and cost-effective
- Can run on commodity hardware
- Examples: NGINX, HAProxy, Apache HTTP Server

#### Cloud Load Balancers
- Managed services provided by cloud providers
- Easy to set up and scale
- Pay-as-you-use pricing
- Examples: AWS ELB, Google Cloud Load Balancer, Azure Load Balancer

### Load Balancing Algorithms

#### 1. Round Robin
- **How**: Distributes requests sequentially to each server
- **Example**: Server1 → Server2 → Server3 → Server1
- **Pros**: Simple, fair distribution
- **Cons**: Doesn't consider server capacity
- **Best for**: Servers with similar capacity

#### 2. Weighted Round Robin
- **How**: Round robin with different weights for each server
- **Example**: Server1 (weight 3) → Server2 (weight 1)
- **Pros**: Considers server capacity
- **Cons**: Still doesn't consider current load
- **Best for**: Servers with different capacities

#### 3. Least Connections
- **How**: Routes to server with fewest active connections
- **Example**: Server1 (5 connections) → Server2 (2 connections) → Route to Server2
- **Pros**: Considers current server load
- **Cons**: Doesn't consider server capacity
- **Best for**: Long-lived connections

#### 4. Least Response Time
- **How**: Routes to server with lowest response time
- **Example**: Server1 (100ms) → Server2 (50ms) → Route to Server2
- **Pros**: Considers actual performance
- **Cons**: Requires monitoring response times
- **Best for**: Performance-critical applications

#### 5. IP Hash
- **How**: Uses client IP to determine which server to use
- **Example**: Same client always goes to same server
- **Pros**: Session persistence
- **Cons**: Uneven distribution if IPs are clustered
- **Best for**: Applications requiring session affinity

### Load Balancer Architectures

#### Layer 4 Load Balancing (Transport Layer)
- **What**: Routes based on IP and port
- **Protocols**: TCP, UDP
- **Speed**: Fast (no application layer processing)
- **Use cases**: Simple traffic distribution

#### Layer 7 Load Balancing (Application Layer)
- **What**: Routes based on HTTP headers, URLs, content
- **Protocols**: HTTP, HTTPS
- **Speed**: Slower (processes application data)
- **Use cases**: Content-based routing, SSL termination

### Load Balancer Features

#### Health Checks
- **Purpose**: Monitor server health
- **How**: Regularly ping servers to check if responding
- **Actions**: Remove unhealthy servers from rotation
- **Example**: Check every 30 seconds, remove if 3 consecutive failures

#### SSL Termination
- **Purpose**: Handle SSL/TLS encryption
- **How**: Load balancer decrypts HTTPS, forwards HTTP to servers
- **Benefits**: Reduces server load, centralized certificate management
- **Example**: Client → HTTPS → Load Balancer → HTTP → Server

#### Session Persistence (Sticky Sessions)
- **Purpose**: Keep user sessions on same server
- **How**: Route same user to same server
- **Methods**: IP hash, cookie-based, URL-based
- **Example**: User login on Server1, all subsequent requests go to Server1

#### Auto Scaling Integration
- **Purpose**: Automatically add/remove servers based on load
- **How**: Monitor metrics, trigger scaling events
- **Benefits**: Cost optimization, automatic capacity management
- **Example**: Add servers when CPU > 80%, remove when CPU < 30%

### Common Load Balancing Patterns

#### Active-Passive (Failover)
- **Setup**: One active server, one standby server
- **Behavior**: Standby takes over if active fails
- **Use case**: High availability for critical systems
- **Example**: Database primary-replica setup

#### Active-Active
- **Setup**: Multiple servers handling traffic simultaneously
- **Behavior**: All servers active, load distributed
- **Use case**: High performance and availability
- **Example**: Web server cluster

#### Geographic Load Balancing
- **Setup**: Servers in different locations
- **Behavior**: Route users to nearest server
- **Use case**: Global applications
- **Example**: CDN with multiple edge locations

### Key Metrics to Monitor
**Performance Metrics:**
- Response time per server
- Requests per second
- Error rates
- Connection counts

**Health Metrics:**
- Server availability
- Health check results
- Server resource utilization
- Network latency

### Common Challenges and Solutions

#### Challenge 1: Session Management
- **Problem**: User sessions stored on specific server
- **Solution**: Use sticky sessions or external session store

#### Challenge 2: Database Bottlenecks
- **Problem**: All servers hit same database
- **Solution**: Database read replicas, connection pooling

#### Challenge 3: Uneven Load Distribution
- **Problem**: Some servers get more traffic
- **Solution**: Use weighted algorithms, monitor server capacity

#### Challenge 4: SSL Certificate Management
- **Problem**: Managing certificates on multiple servers
- **Solution**: SSL termination at load balancer

### Best Practices
1. **Design for Failure**: Assume servers will fail
2. **Monitor Everything**: Track performance metrics, set up alerting
3. **Test Your Setup**: Simulate failures, test under high load
4. **Security Considerations**: Use HTTPS, implement rate limiting

### Key Takeaways
1. **Load balancing distributes traffic** across multiple servers
2. **Choose the right algorithm** based on your needs
3. **Health checks are essential** for reliability
4. **Monitor performance** to ensure optimal operation
5. **Plan for failures** - servers will go down
6. **Layer 4 vs Layer 7** - choose based on requirements

---

## Quick Reference Summary

### Architecture Decisions
- **Start with Monolithic** → **Evolve to Microservices** as you grow
- **Vertical Scaling** for quick wins → **Horizontal Scaling** for long-term
- **Choose CAP properties** based on service requirements
- **Optimize Latency/Throughput/Bandwidth** based on use case
- **Implement Load Balancing** for scalability and availability

### Key Questions to Ask
1. **What's the acceptable response time?** (Latency)
2. **How many users will use the system?** (Throughput)
3. **How much data will be transferred?** (Bandwidth)
4. **What happens if this data is inconsistent?** (CAP)
5. **How will you handle server failures?** (Load Balancing)

### Next Steps
These foundations prepare you for:
- Advanced system design patterns
- Real-world system case studies
- Distributed systems concepts
- Performance optimization techniques
- Scalability strategies

---

*Keep these notes handy for quick reference and review regularly to maintain your system design knowledge!*

