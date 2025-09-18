# Networking & Communication - System Design Notes

## Table of Contents
1. [HTTP/HTTPS & REST APIs](#httphttps--rest-apis)
2. [DNS, IP, TCP/UDP Basics](#dns-ip-tcpudp-basics)
3. [Sockets & WebSockets](#sockets--websockets)
4. [CDN (Content Delivery Network)](#cdn-content-delivery-network)

---

## HTTP/HTTPS & REST APIs

### HTTP Basics
- **Protocol**: Request-response communication between clients and servers
- **Stateless**: Each request is independent - server doesn't remember previous requests
- **Methods**: GET, POST, PUT, DELETE, PATCH (each has semantic meaning)
- **Status Codes**: 
  - 2xx (success)
  - 4xx (client error) 
  - 5xx (server error)

### HTTPS vs HTTP
- **HTTP**: Data travels in plain text (vulnerable to interception)
- **HTTPS**: HTTP + SSL/TLS encryption for secure data transmission
- **Benefits**: User data protection, SEO ranking, browser trust indicators

### REST API Design Principles
**REST (Representational State Transfer)** - Architectural style for designing APIs

**Core Principles:**
1. **Resource-based URLs**: `/users/123` (not `/getUser?id=123`)
2. **HTTP methods for actions**:
   - `GET /users` - retrieve all users
   - `POST /users` - create new user
   - `PUT /users/123` - update entire user
   - `PATCH /users/123` - partial update
   - `DELETE /users/123` - remove user
3. **Stateless**: Each request contains all needed information
4. **Consistent response format**: JSON is standard
5. **HTTP status codes**: Use appropriate codes (200, 201, 400, 404, 500)

### System Design Impact
- **Scalability**: Stateless nature allows easy horizontal scaling
- **Caching**: GET requests can be cached at multiple levels
- **Load Balancing**: Any server can handle any request (no session affinity needed)
- **API Versioning**: `/api/v1/users` vs `/api/v2/users`

---

## DNS, IP, TCP/UDP Basics

### DNS (Domain Name System)
**DNS = Internet's phonebook** - translates domain names into IP addresses

**DNS Resolution Process:**
1. Browser checks local cache
2. Queries DNS resolver (usually ISP)
3. DNS resolver queries: root servers → TLD servers → authoritative servers
4. Returns IP address
5. Browser connects to that IP

**DNS Record Types:**
- **A Record**: Domain → IPv4 address (`google.com → 142.250.191.14`)
- **AAAA Record**: Domain → IPv6 address
- **CNAME**: Alias (`www.google.com → google.com`)
- **MX**: Mail server records
- **TXT**: Text records (verification, SPF, etc.)

### IP (Internet Protocol)
**IP addresses = unique identifiers for network devices**

**IPv4 vs IPv6:**
- **IPv4**: 32-bit addresses (`192.168.1.1`) - ~4.3 billion addresses
- **IPv6**: 128-bit addresses (`2001:0db8:85a3::8a2e:0370:7334`) - virtually unlimited

**Public vs Private IP:**
- **Public IP**: Unique globally, assigned by ISP
- **Private IP**: Used within local networks (`192.168.x.x`, `10.x.x.x`)

### TCP vs UDP

| Feature | TCP | UDP |
|---------|-----|-----|
| **Reliability** | Guaranteed delivery & order | No guarantee |
| **Connection** | Connection-oriented | Connectionless |
| **Speed** | Slower (overhead) | Faster (minimal overhead) |
| **Error Checking** | Yes, retransmits lost packets | No |
| **Use Cases** | Web browsing, email, APIs | Video streaming, gaming, DNS |

### System Design Impact
- **DNS**: Use multiple DNS providers for redundancy
- **TCP for APIs**: REST APIs use HTTP over TCP for reliability
- **UDP for real-time**: Gaming, live streaming prioritize speed
- **Load Balancing**: DNS can distribute traffic geographically

---

## Sockets & WebSockets

### Traditional Sockets
**Sockets = endpoints for network communication**

**How Sockets Work:**
1. **Server Socket**: Listens on specific port (e.g., 8080)
2. **Client Socket**: Connects to server's IP:port
3. **Bidirectional Communication**: Both can send/receive
4. **Persistent Connection**: Stays open until closed

**Socket Types:**
- **TCP Sockets**: Reliable, ordered data (most common)
- **UDP Sockets**: Fast, unreliable data

### WebSockets
**WebSockets = full-duplex communication over HTTP**

**Why WebSockets Exist:**
- **HTTP Limitation**: Request-response only, client must initiate
- **Old Solutions**: Polling (inefficient), Long-polling (complex)
- **WebSocket Solution**: Persistent, bidirectional connection

**WebSocket Handshake:**
1. Client sends HTTP request with `Upgrade: websocket` header
2. Server responds with `101 Switching Protocols`
3. Connection "upgrades" from HTTP to WebSocket
4. Both sides can send messages anytime

**Communication Patterns:**
```
HTTP: Client → Request → Server → Response → Connection Closed
WebSocket: Client ↔ Persistent Connection ↔ Server
```

### Real-time Communication Options

**1. Server-Sent Events (SSE):**
- **Direction**: Server → Client only
- **Protocol**: Built on HTTP
- **Features**: Auto-reconnection, event types
- **Use Cases**: Live notifications, stock prices

**2. WebSockets:**
- **Direction**: Client ↔ Server (bidirectional)
- **Features**: Lower latency, more complex
- **Use Cases**: Chat apps, gaming, collaborative editing

**3. Polling vs Push:**
- **Polling**: Client repeatedly asks "any updates?" (inefficient)
- **Push**: Server sends updates when they happen (efficient)

### System Design Challenges
**Scalability Issues:**
- **Connection Management**: Each WebSocket consumes server resources
- **Load Balancing**: Need sticky sessions (user stays on same server)
- **Horizontal Scaling**: Need message broadcasting across servers

**Solutions:**
- **Message Brokers**: Redis Pub/Sub, RabbitMQ for cross-server communication
- **Connection Pools**: Limit concurrent connections per server
- **Clustering**: Tools like Socket.IO with Redis adapter

### Real-world Examples
- **Chat Apps (Slack/Discord)**: Instant message delivery
- **Collaborative Editing (Google Docs)**: Real-time text updates
- **Trading Platforms**: Live price updates
- **Gaming**: Real-time player interactions

---

## CDN (Content Delivery Network)

### What is a CDN?
**CDN = geographically distributed network of servers** that cache and deliver content from the closest location to users.

**Analogy**: Like having multiple warehouses worldwide instead of shipping everything from one central location.

### How CDNs Work

**Without CDN:**
```
User in Tokyo → 8000+ miles → Server in New York → 8000+ miles back
Result: High latency, slow loading
```

**With CDN:**
```
User in Tokyo → 50 miles → Tokyo CDN edge server → Fast response
Result: Low latency, fast loading
```

**CDN Process:**
1. **First Request**: User → CDN (cache miss) → Fetch from origin → Cache → Serve user
2. **Subsequent Requests**: User → CDN (cache hit) → Serve instantly

### Content Types

**Static Content (Perfect for CDN):**
- Images, CSS, JavaScript files
- Videos, audio files
- Documents, fonts
- Software downloads

**Dynamic Content (More Complex):**
- Personalized content
- API responses
- Real-time data

### CDN Benefits

**1. Performance:**
- **Reduced Latency**: Content from nearby servers
- **Faster Load Times**: Cached content = no origin roundtrip
- **Bandwidth Offloading**: Origin server handles less traffic

**2. Reliability:**
- **Redundancy**: Multiple servers, failover capability
- **DDoS Protection**: Distributed architecture absorbs attacks
- **Origin Protection**: CDN handles traffic spikes

**3. Cost Savings:**
- **Reduced Bandwidth**: Less traffic to origin server
- **Server Resources**: Origin handles fewer requests

### CDN Strategies

**1. Cache Control Headers:**
```http
Cache-Control: max-age=3600  // Cache for 1 hour
Cache-Control: no-cache      // Always validate with origin
Cache-Control: public        // Can be cached by CDN and browsers
```

**2. Cache Invalidation Methods:**
- **TTL (Time To Live)**: Automatic expiration
- **Purge/Invalidate**: Manual cache clearing
- **Versioning**: `style-v1.2.css` → `style-v1.3.css`

**3. CDN Architecture:**
```
User → CDN Edge Server → Regional Cache → Origin Server
```

### Real-world Examples

**Netflix:**
- Videos cached at thousands of edge locations
- 95%+ traffic served from CDN
- Custom CDN infrastructure for massive scale

**E-commerce:**
- Product images via CDN (fast loading)
- CSS/JS files cached globally
- Dynamic content (cart, user data) bypasses CDN

**News Websites:**
- Articles cached at edge servers
- Media files distributed globally
- Breaking news might bypass cache for freshness

### CDN Trade-offs

**Pros:**
- Dramatically improved performance
- Reduced server load
- Better global user experience
- Built-in redundancy

**Cons:**
- **Cost**: CDN services aren't free
- **Complexity**: Cache invalidation, configuration
- **Stale Data**: Cached content might be outdated
- **Vendor Lock-in**: Switching providers can be complex

### Popular CDN Providers
- **CloudFlare**: Global network, security features
- **AWS CloudFront**: Integrated with AWS ecosystem
- **Google Cloud CDN**: Google's global infrastructure
- **Azure CDN**: Microsoft's offering

---

## Key Takeaways

### When to Use What:

**HTTP/HTTPS + REST:**
- Standard web APIs
- CRUD operations
- Stateless interactions

**TCP:**
- Reliable data transfer needed
- APIs, file transfers, email

**UDP:**
- Speed over reliability
- Gaming, live streaming, DNS

**WebSockets:**
- Real-time bidirectional communication
- Chat, collaborative tools, live updates

**CDN:**
- Global audience
- Static content delivery
- Performance optimization

### System Design Principles:
1. **Choose the right protocol** for your use case
2. **Consider latency and reliability** trade-offs
3. **Plan for scale** from the beginning
4. **Use CDNs** for global performance
5. **Implement proper caching** strategies

---

*These notes cover the fundamental networking and communication concepts essential for system design. Each technology has its place in building scalable, performant systems.*

