Load balancers distribute incoming client requests to computing resources such as application servers and databases. In each case, the load balancer returns the response from the computing resource to the appropriate client. Load balancers are effective at : 
- Preventing requests from going to unhealthy servers
- Preventing overloading resources
- Helping to eliminate single point of failure

Load balancers can be implemented with hardware or with software such as HAProxy.

Additional benefits include:
- SSL termination - Decrypt incoming requests and encrypt server responses so backend servers do not have to perform these potentially expensive operations.
	- Removes the need to install X.509 Certificates on each server
- Session Persistence - Issue cookies and route a specific client's requests to same instance if the web apps do not keep track of sessions.

To protect against failures, it's common to set up multiple load balancers either in [[Availability Patterns | active-passive]] or  [[Availability Patterns | active-active]] mode.

Load balancers can route traffic based on various metrics, including:
- Random
- Least loaded
- Session/Cookies
- Round robin or weighted round robin
- Layer 4
- Layer 7

#### Layer 4 load balancing
Layer 4 load balancers look at info at the [[OSI | transport layer]] to decide how to distribute requests( Transport layer is the 4th layer in the 7 layers of OSI model, Hence the name Layer 4 load balancing). Generally, this involves the source IP, destination IP address & ports in the header, but not the contents of the packet. Layer 4 load balancers forward network packets to and from the upstream server, performing Network Address Translation (NAT).

#### Layer 7 load balancing
Layer 7 load balancers look at the [[OSI | application layer]] to decide how to distribute the requests. This can involve contents of the header, message, and cookies. 
Layer 7 load balancers terminate network traffic, reads the message, makes a load-balancing decision, then opens a connection the selected server. For example, a layer 7 load balancer can direct video traffic to servers that host videos while directing more sensitive user billing traffic to security-hardened servers.

At the cost of flexibility, later 4 load balancing requires less time and computing resources than Layer 7, although the performance impact can be minimal on modern commodity hardware.

#### Horizontal Scaling
- Load balancers can also help with horizontal scaling, improving performance and availability. 

##### Disadvantages : Horizontal scaling
- Scaling horizontally introduces complexity and involves cloning servers
	- Servers should be stateless: they should not contain user related data like sessions or profile pictures
	- Sessions can be stored in a centralized data store such as a database or a persistent cache
- Down stream servers such as caches and databases need to handle more simultaneous connections as upstream servers scale out

#### Disadvantages - Load balancer
- The load balancer can become a performance bottleneck if it does not have enough resources or if it is not configured properly.
- Introducing a load balancer to help eliminate a single point of failure results in increased complexity.
- A single load balancer is a single point of failure, configuring multiple load balancers further increases complexity.

### Source(s) and further reading

-   [NGINX architecture](https://www.nginx.com/blog/inside-nginx-how-we-designed-for-performance-scale/)
-   [HAProxy architecture guide](http://www.haproxy.org/download/1.2/doc/architecture.txt)
-   [Scalability](http://www.lecloud.net/post/7295452622/scalability-for-dummies-part-1-clones)
-   [Wikipedia](https://en.wikipedia.org/wiki/Load_balancing_(computing))
-   [Layer 4 load balancing](https://www.nginx.com/resources/glossary/layer-4-load-balancing/)
-   [Layer 7 load balancing](https://www.nginx.com/resources/glossary/layer-7-load-balancing/)
-   [ELB listener config](http://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-listener-config.html)