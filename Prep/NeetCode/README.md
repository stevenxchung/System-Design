# NeetCode System Design

System design notes from NeetCode courses

## 1. Background

- System design interviews typically focus on a smaller feature or subset of a product and aims to test understanding of distributed systems as well as communication
- Always scope out requirements:
  - **Functional**: define what the software system or application should do in terms of specific functionalities, features, and behavior. They describe the intended behavior of the system and outline the expected outputs or results
  - **Non-functional**: define the qualities or characteristics of the software system, specifying how it should behave and perform. They focus on aspects beyond the core functionalities, addressing areas such as performance, security, reliability, usability, scalability, and maintainability

### Computer Architecture

- Disks are **persistent**, data lives whether computer is in on/off state
- **RAM (random access memory)** is non-persistent and can be used to quickly read/write data (microseconds) vs on disk (milliseconds)
- CPU is intermediary between RAM and disk and reads/writes data from either
- CPUs also have a cache (L1, L2, and L3) which is even faster to read/write from vs RAM or disk but has limited memory (up to ~10 MB)
- **Moore's Law** is an observation which suggests that the number of transistors in a CPU doubles every two years. However, in recent years this trend has begun to slow

### Application Architecture

- **Vertical scaling** is when components within the same computer is being upgraded but has limitations in terms of hardware and availability
- **Horizontal scaling** is the preferred approach of creating more servers to evenly distribute requests and ensure availability
- **Load balancers** evenly distributes incoming requests to groups of servers
- **Logging** services provide a way to view application state history and activity. These are usually written to another external server for reliability
- **Metric** services on the other hand, provide a view into the server environment (e.g., CPU usage, network traffic, etc.)

### Design Requirements

- System design can be boiled down to three points: moving, storing, and transforming data or **ETL (extract, transform, load)**
- **Availability** is the percentage of time the system is up and running for a given period of time
- **Reliability** is a system's ability to perform its intended operation without failure or errors over a given period of time
- **Fault-tolerance** is how well the system can detect and heal itself from a problem (switching to another server). To achieve fault-tolerance requires a redundant server to be on standby. In most cases, systems use **active-active redundancy** where more than one server is active
- **Throughput** is the amount of data or operations a system can handle over some period of time
- **Latency** is the delay between the client and server during a request/response

## 2. Networking

### Networking Basics

- A **network** is a collection of interconnected devices, such as computers, servers, routers, switches, and other networking equipment, that are linked together to facilitate communication and the sharing of resource
- An **IP address** is a distinct numeric identifier (IPv4 - 32-bit and IPv6 - 128-bit) for every device connected to a network
- **TCP packets** include an IP header (e.g., source and destination IP), TCP header (e.g., for sequencing), and application data
- **Public networks** are accessible by internet where a public IP is granted by an ISP (Internet Service Provider) whereas a **private network** is only accessible within a LAN (local area network)
- **Dynamic IPs** are assigned to a device each time a connection is established with a network whereas a **static IP** requires manual configuration
- **Ports** are numeric identifiers (16-bit integer, 0-65535) used to distinguish between multiple applications or services running on a single device

### TCP and UDP

- TCP utilizes a **3-way handshake** (SYN - synchronize, SYN-ACK - synchronize acknowledgement, and ACK - acknowledgement) between devices to establish a connection and ensure lost packets are reliably delivered. However, this has the drawback of having more overhead
- **UDP (User Datagram Protocol)** on the other hand, has faster data transfer as it does not attempt to resend lost packets or reorder them. A common use case for UDP is online gaming or video streaming

### DNS

- **DNS (Domain Name System)** is a decentralized _phone book_ which converts readable website names into numerical IPs. Coordination, security, and operation of domain names is managed by ICANN (Internet Corporation for Assigned Names and Numbers)
- **Domain Name Registrars** (e.g., Google Domains, Route53, GoDaddy, etc.) maintain the DNS records and ensure domains are registered properly
- An **A record** is a DNS record which associates a domain name with an IPv4 address
- There are different types of protocols for URLs (uniform resource locators):
  - HTTP/HTTPS (Hypertext Transfer Protocol) is the most common web protocol that governs communication between web browsers and servers and acts as a layer on top of TCP
  - FTP (File Transfer Protocol) - used for accessing files and directories on a remote server
  - SSH (Secure Shell) - used for establishing secure remote connections to another server
- A domain consists of three parts: subdomain (e.g., `chat` in `https://chat.openai.com/`), domain (e.g., `openai.com` in `https://chat.openai.com/`), and top-level domain (e.g., `.com`)
- A _path_ is denoted after a `/` and signifies a specific location or route within the website where particular content or resources are located

## 3. APIs

### HTTP

- **Client** refers to an application or system that access a service made available by a server. The client can be a web browser, an email software, an app on your phone, or any other software that needs to access some service
- **Server** refers to a computer, device, or software the providers resources, data, services, or functionality to the client or other servers on a network
- **RPC (remote procedure call)** provides the ability for a program to perform functions on another machine, making it a simple and efficient solution for task management in distributed systems, where programs operate across multiple computers
- HTTP requests/responses can include a method (GET, POST, PUT, DELETE), URL, headers, and body (for POST and PUT). Regarding HTTP methods:
  - **GET** methods are idempotent (no change of server state) so the act of retrieving the resource itself should not cause any modifications (no _side effects_)
  - **POST** methods will send data to the server to create a new resource
  - **PUT** methods are used to update a resource and is also idempotent (identical requests give the same result)
  - **DELETE** methods will delete the specified data and is also idempotent
- Common HTTP status codes include:
  - 200: OK
  - 201: Created
  - 400: Bad Request
  - 401: Unauthorized
- **SSL/TLS (Secure Sockets Layer/Transport Security Layer)** are cryptographic protocols used to provide secure communication over a computer network. HTTPS is a combination of HTTP with TLS

### WebSockets

- **WebSockets** is a communication protocol that provides full-duplex communication channels over a single TCP connection. Both the client and the server can send data to each other simultaneously without the need for a request-response cycle once the initial connection has been established
- The introduction of HTTP/2 allows for **multiplexing** - multiple requests in parallel can be initiated over a single TCP connection. However, this is not a perfect replacement to WebSocket

### API Paradigms

- The three most popular API paradigms are:

  - **REST (Representational State Transfer)** which utilizes HTTP for communication between client and server
    - Pros: REST APIs require a client-server architecture to enable independent development. Additionally, REST APIs are stateless - servers should not retain any details on previous client requests (no session or cookie management)
    - Cons: over/under-fetching data is possible as applications become more complex
  - **GraphQL** addresses the over/under-fetching limitations of REST APIs through **queries** and **mutations** (retrieving data and modifying data on the server respectively)
    - Pros: operates through a single HTTP POST endpoint and enables clients to specify required data through a strongly-typed schema
    - Cons: requires additional setup, tooling, and properly defining the schema in advance. Security could also be an issue since GraphQL enables clients to query any data they need
  - **gRPC (Google Remote Procedure Call)** is a framework for executing RPCs which allows a program to execute a procedure on another computer or shared network. Used for server-server communication, it is most similar to WebSockets and providers bi-directional communication or multiplexing
    - Pros: sends data using **protobufs (Protocol Buffers)** a language-neutral, platform-neutral extensible mechanism for serializing structured data to reduce payload and increase performance and efficiency
    - Cons: does not support error codes provided by HTTP so server messages must be customized, requires additional setup and properly defining API contracts using protobufs

- **JSON (JavaScript Object Notation)** is a widely used data format in web services where data is structured as key-value pairs

### API Design

- APIs provide **CRUD (Create, Read, Update, Delete)** functions which are equivalent to GET, POST, PUT, and DELETE HTTP/REST methods
- It is important to consider backward compatibility when designing endpoint such that disruptions are avoided or minimized (e.g., adding an optional instead of a required parameter to an endpoint)
- Incorporating `limit` and `offset` query parameters enables clients to specify a specific range or slice of records to retrieve
- **API versioning** is used to document a significant change to an API such that developers can be able to make changes accordingly

## 4. Caching Basics

### Caching

- **Caching** is taking a copy of the original data and bringing it closer to wherever a service is to increase throughput (e.g., CPU L1, L2, and L3 cache). To see comparison of typical latency numbers see [Latency Numbers You Should Know](https://blog.bytebytego.com/p/ep22-latency-numbers-you-should-know)
- There are several common caching strategies:
  - **Write-around** updates the primary data source first and defers writes to the cache (eventual consistency)
  - **Write-through** updates the cache and primary data source to ensure consistency
  - **Write-back** updates the cache but defers writes on the primary data source (eventual consistency)
- The most common eviction policies for caching are:
  - **FIFO (first-in, first-out)** evicts the oldest record from the cache first
  - **LRU (least recently used)** evicts the least read record in the cache first
  - **LFU (least frequently used)** evicts the least frequently read record in the cache first

### CDNs

- **CDNs (Content Delivery Networks)** brings static content closer to users and increases the reliability and availability of our system due to the distributed network
- To distribute and deliver content CDNs can use different strategies:
  - **Pull CDNs** are initiated by user request and content is pulled from the origin server by the CDN and stored on the CDN edge servers
  - **Push CDNs** have content pushed to the CDN edge servers in advance by the origin server and requires more management by the content provider

## 5. Proxies

### Proxies and Load Balancers

- **Proxies** are servers which can act as a server between a client and a destination server (e.g., a VPN takes requests from a client and routes them to a destination server),
- There are two common flavors of proxies:
  - **Forward proxy** hides client information and provides communication with a destination server
  - **Reverse proxy** hides destination server information and provides communication with a client (e.g., a CDN, load balancer, etc.)
- **Load balancers** act as a reverse proxy to route/distribute requests evenly to a number of servers and can handle high throughput
- There are a number of load balancing strategies:
  - **Round robin** (also a weighted version) cycles through servers
  - **Least connections** routes based on the number of connections to a server
  - **Location-based** routes based on IP and geo-location, could be implemented by a layer 4 load balancer
  - **Hashing** utilizes a hashing algorithm to determine the target server. This can help ensure session persistence since clients can be routed to the same server but could run into issues with consistent hashing if servers are removed or shuffled
- There are different types of load balancers by layer:
  - **Transport layer (layer 4)** is faster but less robust (typically provides routing only)
  - **Application layer (layer 7)** is slower but more flexible (ability to provider application data)

### Consistent Hashing

- **Consistent hashing** aims to provide a balanced distribution of data while minimizing the impact of adding or removing nodes from the system by mapping hashed values to the nearest node in circular space (clockwise). Benefits of consistent hashing include:
  - Load balancing: data is evenly distributed
  - Scalability: nodes can be added or removed without impacting existing distribution
  - Fault tolerance: only a portion of the data needs to be remapped

## 6. Storage

### SQL

- **SQL (Standard Query Language)** provides a way to access and modify a **RDBMS (relational database management system)** which provides persistent storage. The DB tables and schemas must be created/specified in advance
- **B+ Tree** is a balancing search tree data structure that is commonly used in computer science and database systems for efficient storage and retrieval of large amounts of data. It is an extension of the original B-tree data structure and has the following properties:
  - Balanced tree
  - Multiple keys per node
  - Sorted order
  - Lead node chaining
  - Range queries
  - Disk-based storage
- **Foreign-key constraints** may be added to a table such that there is a relation established between two or more tables
- **Primary keys** uniquely identifies every row in a table
- Majority of RDBMS are **ACID** compliant to maintain data integrity, consistency, and reliability:
  - **Atomic**: DB transactions cannot be split and are all-or-nothing commits
  - **Consistent**: to ensure valid state for reads/writes based on constraints
  - **Isolated**: DB transactions are serialized even if they are concurrent
  - **Durable**: persistent storage, DB transactions are stored when committed

### NoSQL

- **NoSQL (Not Only SQL)** are non-relational stores which consists of many different flavors and have the ability to scale much more readily than traditional RDBMS. NoSQK DBs are more flexible than traditional RDBMS since they do not require a schema to be specified in advance
- Common NoSQL DBs include:
  - **Document stores** are key-value documents or collections of JSON objects which may be nested within a larger JSON
  - **Wide-column DBs** are optimized for writes and are best for when reads and updates do not occur often (e.g., Cassandra, BigTable, etc.). This type of DB is well-suited for handling time-series data and large-scale analytics
  - **Graph DBs** designed to store and process graph-like structures with nodes, edges, and properties
- ACID is not a standard guarantee for NoSQL databases as they typically exhibit **eventual consistency** where reading a replica DB could return stale data after a write but eventually converges with the leader DB

### Replication and Sharding

- **Replication** is the process of copying data from a primary DB to a replica DB and can be done synchronously (strong read consistency) or asynchronously (eventual read consistency), two most common replication strategies include:
  - **Leader-follower** where a replica DB (follower) converges with data on the leader DB over time. Leader DBs permit read/write access but replica DBs are read only (e.g., read replicas)
  - **Leader-leader** where two leader DBs converges with each other over time. Can be useful to scale up reads and writes but could be more complicated to have consistent leader DBs
- **Sharding** is a data partitioning technique to horizontally divide and distribute data across multiple nodes or shards. A large dataset is split into smaller, more manageable subsets called shards, and each shard is stored on a separate database server or node. Types of sharding include:
  - **Range-based** data is split based on a range of values
  - **Hash-based** data is split based on a hash function (consistent hashing is useful here!)
  - **Key-value based** data is split based on specific keys are attributes that are relevant to the application
- A **shard key** is a partition key or hash key, is a field or attribute used to determine how data is distributed and assigned to different shards in a sharded database system. It is a crucial component of sharding strategies and helps in the efficient partitioning of data across multiple nodes
- Note that sharding means that ACID compliance is lost for traditional RDBMS in favor of scalability whereas NoSQL databases readily support sharding/partitioning

### CAP Theorem

- **CAP Theorem** states that in a distributed computer system, it is impossible to simultaneously guarantee all of the following properties:
  - **Consistency (C)**: every read operation receives the most recent write or an error. Consistency ensures that all nodes in a distributed system agree on the state of the data at any given time
  - **Availability (A)**: every request receives a response, without any errors, as long as at least one node in the system is functioning correctly. Availability ensures that the system remains operational even in the presence of failures
  - **Partition tolerance (P)**: the system continues to operate and provide consistent and available services even in the presence of network partitions or communication failures that may split the system into multiple disjoint subsets of nodes
- According to the CAP theorem, in the event of a network partition or failure, a distributed system must choose between maintaining _consistency_ or _availability_. It is not possible to have both simultaneously
- **PACELC** is an extension of the CAP theorem and factors in behavior of distributed systems during network partition and defines three consistency models (as noted by CAP Theorem above) and defines one more property:
  - **Latency (L)**: refers to the response time or delay in a distributed system. It considers the trade-off between consistency and the time it takes to propagate updates across the system
- Generally, under PACELC, given network partitioning (P) in a distributed computer system, one has to choose between availability (A) and consistency (C) (as per the CAP theorem), but else (E), even when the system is running normally in the absence of partitions, one has to choose between latency (L) and consistency (C)

### Object Storage

- **Object storage** or **BLOB (Binary Larger Object) Storage** is a data storage architecture that manages data as objects rather than traditional file hierarchies or block addresses. It is designed to store and retrieve large volumes of unstructured data, such as files, documents, images, videos, and other multimedia content
- Object storage is _flat_ (mapped to unique HTTP/HTTPS namespace) and provides a more flexible and scalable approach to data storage, enabling efficient management of large volumes of unstructured data without the limitations imposed by hierarchical file systems

## 7. Big Data

## Message Queues

- **Message queues** (e.g., RabbitMQ, Kafka, GCP Pub/Sub) provide the following benefits to a system:
  - Decoupling of services
  - Allow services to scale separately
  - Asynchronous processing of messages and data
  - Built-in persistent storage (stored in queue **topics** or groups)
- **Pub/Sub (Publisher/Subscriber) Queues** are a particular type of message queue that allows publishers to add messages to various topics and fan-out messages to subscribers
- However, message queues do have some drawbacks:
  - Increased complexity to a system's architecture
  - Increased latency (asynchronous delivery of messages)
  - Message ordering challenges
  - Potential message loss
  - Operational overhead

## MapReduce

- **MapReduce** is a programming model and processing framework designed for large-scale distributed data processing. It is widely used for processing and analyzing big data across a cluster of machines. MapReduce simplifies the parallel processing of data by dividing a task into two distinct phases:
  - **Map**: divides input data into smaller chunks and processes in parallel with multiple worker nodes. Each worker node applies a mapping function to the input data, transforming it into a set of key-value pairs
  - **Reduce**: key-value pairs generated by the Map phase are grouped based on their keys, and a reducing function is applied to each group
- MapReduce framework automatically handles parallelization, fault tolerance, and data distribution across the cluster. It ensures that the Map and Reduce phases are executed efficiently and that the intermediate data is properly shuffled and distributed among the worker nodes
- MapReduce has been widely adopted in various big data processing systems, such as Apache Hadoop and Apache Spark and allows companies to efficiently analyze and extract insights from massive datasets, enabling tasks like data transformations, filtering, aggregations, and more, across distributed computing resources
- Two primary strategies of processing and handling data:
  - **Batching**: involves processing data in larger batches at discrete intervals, resulting in higher latency but efficient resource utilization
  - **Streaming**: processes data in near real-time as it arrives, offering low latency but potentially requiring more resources to handle continuous data processing
