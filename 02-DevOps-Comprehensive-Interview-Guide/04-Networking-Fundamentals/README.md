# Section 4: Networking Fundamentals

---

## Q24. What is an IP address?

**🔑 Keywords:** IPv4 (32-bit), IPv6 (128-bit), network interface identity, packets, routing endpoint

**Beginner-Friendly Answer:**
An IP (Internet Protocol) address is a unique numerical address given to any device or network interface connected to a computer network. Just like a physical postal address allows mail carriers to deliver letters to your house, an IP address allows routers to deliver data packets to the correct machine. IPv4 uses 32-bit numbers like `192.168.1.1`, while IPv6 uses longer 128-bit alphanumeric strings.

**Detailed Answer:**
An IP address identifies a network interface for communication over an IP network. IPv4 addresses are 32 bits, while IPv6 addresses are 128 bits.

---

## Q25. What is a subnet?

**🔑 Keywords:** network segmentation, IP address range, broadcast domain, routing boundaries, traffic isolation

**Beginner-Friendly Answer:**
A subnet (sub-network) is a subdivision of a larger IP network. Imagine a large office building: instead of having 1,000 employees all shouting in one giant open hall, you divide the building into separate floors and departments (subnets). This keeps local network traffic contained, improves security, and lets you apply different routing rules to different groups.

**Detailed Answer:**
A subnet is a smaller IP network created by dividing an IP address range. Subnetting helps organize networks, define routing boundaries, and control where resources are placed.

---

## Q26. What is CIDR notation?

**🔑 Keywords:** Classless Inter-Domain Routing, prefix length, `/24` (256 IPs), `/16` (65,536 IPs), AWS reserved addresses

**Beginner-Friendly Answer:**
CIDR (Classless Inter-Domain Routing) is a compact notation for describing an IP address range using a slash followed by a number (like `10.0.0.0/16`). The number after the slash tells you how many bits are fixed for the network. A `/24` gives you \(2^{32-24} = 256\) total addresses. In AWS VPCs, remember that AWS reserves 5 IP addresses in every subnet (network, router, DNS, future use, broadcast), so a `/24` has 251 usable IPs for EC2 instances.

**Detailed Answer:**
CIDR notation describes an IP network using an address and prefix length. For example, 10.0.0.0/16 represents a larger IPv4 address range than 10.0.1.0/24.

### Cross-question: How many IPv4 addresses are in a /24 range?

**Answer:**
There are \(2^{32-24}=256\) total IPv4 addresses in a /24 range. The number usable by specific AWS resources can differ because AWS reserves certain addresses in each subnet.

---

## Q27. What is DNS?

**🔑 Keywords:** Domain Name System, name-to-IP resolution, A records, CNAME records, troubleshooting connection failures

**Beginner-Friendly Answer:**
DNS (Domain Name System) is the internet's phonebook. Humans remember names like `shop.example.com`, but computers route traffic using IP addresses like `54.210.12.89`. DNS translates domain names into IP addresses. If DNS resolves properly but the website still won't load, DNS is not the culprit — the issue is likely downstream: firewall/security group rules, load balancer health checks, or crashing application containers.

**Detailed Answer:**
The Domain Name System translates domain names into records that clients use to locate services. For example, a DNS record can direct app.example.com to an application load balancer.

### Cross-question: What happens when DNS resolves correctly but the website does not load?

**Answer:**
DNS resolution is only one part of the connection. I would check routing, firewall and security-group rules, the load balancer listener, TLS configuration, target health, application endpoints, and service connectivity.

---

## Q28. What is the difference between TCP and UDP?

**🔑 Keywords:** TCP (connection-oriented, reliable, 3-way handshake, retransmission), UDP (connectionless, fast, datagrams), HTTP/1.1 vs HTTP/3 (QUIC)

**Beginner-Friendly Answer:**
- **TCP** is like a certified registered letter with tracking and delivery confirmation: it establishes a connection (3-way handshake), checks that every single packet arrived in the right order, and resends missing packets. Used by web pages, emails, and database queries.
- **UDP** is like a live radio broadcast: it streams data rapidly without checking if anyone missed a word. Used by online video games, VoIP calls, and modern HTTP/3 (via QUIC).

**Detailed Answer:**
TCP provides a reliable, ordered byte stream with connection management. UDP sends datagrams without TCP's delivery and ordering guarantees.

### Cross-question: Which is used by HTTP?

**Answer:**
HTTP/1.1 and HTTP/2 commonly use TCP. HTTP/3 uses QUIC over UDP.

---

## Q29. What is the difference between HTTP and HTTPS?

**🔑 Keywords:** TLS encryption, port 80 vs 443, encryption in transit, server certificate authentication, backend health

**Beginner-Friendly Answer:**
HTTP transmits web data in plain readable text: anyone sitting between you and the server (like on public Wi-Fi) can read passwords and session tokens. HTTPS wraps HTTP inside a TLS (Transport Layer Security) encrypted tunnel on port 443. Keep in mind: having a green HTTPS padlock only means your connection to the server is encrypted; it doesn't prove that the backend server code is free of bugs or malware!

**Detailed Answer:**
HTTPS is HTTP protected by TLS. TLS provides encryption, integrity protection, and server authentication when the certificate is properly validated.

### Cross-question: Does having a valid certificate mean the backend application is healthy?

**Answer:**
No. A valid certificate protects the connection but does not guarantee that the application or its dependencies are functioning.

---

## Q30. What is a reverse proxy?

**🔑 Keywords:** client-facing proxy, TLS termination, load balancing, path routing, Application Load Balancer, Nginx

**Beginner-Friendly Answer:**
A regular forward proxy hides the identity of the *client* (user). A **reverse proxy** sits in front of backend servers and hides the identity of the *servers*. Clients only talk to the reverse proxy (like AWS ALB or Nginx). The reverse proxy terminates TLS encryption, checks URL paths (e.g. sending `/users` to User Service and `/products` to Product Service), and balances traffic across multiple backend servers.

**Detailed Answer:**
A reverse proxy accepts requests from clients and forwards them to backend servers. It can provide routing, TLS termination, load balancing, and other traffic-management features.

### Cross-question: How does an Application Load Balancer fit this concept?

**Answer:**
An Application Load Balancer operates at the application layer and can route HTTP or HTTPS requests based on rules such as hostnames and paths.

---

## Q31. What is the difference between a public and a private subnet in AWS?

**🔑 Keywords:** Internet Gateway (IGW), NAT Gateway, route tables, public IP, outbound-only connectivity

**Beginner-Friendly Answer:**
- **Public Subnet:** Its route table has a direct route (`0.0.0.0/0 -> igw-xxxx`) to an Internet Gateway. Resources with public IPs can receive incoming traffic directly from the internet (e.g., Load Balancers, Bastion hosts).
- **Private Subnet:** Has no direct route to an Internet Gateway. To download software or reach external APIs, it routes outbound traffic through a NAT Gateway in a public subnet. Strangers on the internet cannot initiate connections to private subnet servers.

**Detailed Answer:**
A subnet is considered public when its route table provides a route to an Internet Gateway. A private subnet does not provide direct internet routing through an Internet Gateway for its resources. Private resources may use a NAT Gateway for outbound internet access.

### Cross-question: Does assigning a public IP automatically make an EC2 instance reachable from the internet?

**Answer:**
No. The subnet's routing, security groups, network ACLs, instance firewall, and service configuration must also permit the connection.
