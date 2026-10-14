# Section 5: AWS Theory and Architecture

---

## Q32. What is AWS?

**🔑 Keywords:** Amazon Web Services, cloud computing, on-demand infrastructure, global infrastructure, VPC, EC2, EKS, RDS

**Beginner-Friendly Answer:**
AWS (Amazon Web Services) is the world's leading cloud computing platform. Instead of buying physical server racks, power supplies, and network switches in your own data center, AWS lets you rent virtual servers, databases, storage, and networking on-demand with pay-as-you-go pricing.

**Detailed Answer:**
Amazon Web Services is a cloud platform that provides computing, networking, storage, databases, security, monitoring, and other services.

---

## Q33. What is a Region?

**🔑 Keywords:** geographic location, data sovereignty, latency reduction, isolated regions, compliance

**Beginner-Friendly Answer:**
An AWS Region is a distinct physical geographical area around the world (e.g., `us-east-1` in North Virginia, `ap-south-1` in Mumbai). Each Region is completely independent and isolated from other Regions to provide maximum fault tolerance and satisfy local data sovereignty laws.

**Detailed Answer:**
A Region is a separate geographic area containing multiple Availability Zones. Resources are created in particular Regions unless a service operates globally or has different scope rules.

---

## Q34. What is an Availability Zone?

**🔑 Keywords:** AZ, isolated datacenters, high availability, low-latency private fiber, fault domains

**Beginner-Friendly Answer:**
Inside every AWS Region, there are multiple (typically 3 or more) Availability Zones (AZs). An AZ consists of one or more physical data centers with independent power, cooling, and networking, connected by high-speed fiber. Deploying your application across at least two AZs ensures that even if a flood or power failure takes down an entire data center building, your website keeps running from the other AZ.

**Detailed Answer:**
An Availability Zone is an isolated location within an AWS Region. Spreading workloads across Availability Zones can improve resilience against certain failures.

### Cross-question: Does deploying across two Availability Zones guarantee high availability?

**Answer:**
No. The application also needs healthy replicas, appropriate load balancing, resilient dependencies, and tested recovery procedures.

---

## Q35. What is Amazon VPC?

**🔑 Keywords:** Virtual Private Cloud, isolated virtual network, CIDR, subnets, route tables, network access control

**Beginner-Friendly Answer:**
Amazon VPC (Virtual Private Cloud) is your dedicated, logically isolated private network inside AWS. It's your private data center in the cloud: you decide the IP range (CIDR), create public and private subnets, configure gateways to the internet, and define firewalls around your servers.

**Detailed Answer:**
A Virtual Private Cloud provides a logically isolated network in AWS. It allows you to define IP ranges, subnets, routing, and network security.

---

## Q36. What is an Internet Gateway?

**🔑 Keywords:** IGW, VPC border router, default route (0.0.0.0/0), horizontal scaling, two-way internet gateway

**Beginner-Friendly Answer:**
An Internet Gateway (IGW) is the horizontally scaled, highly available gateway component that connects your VPC to the public internet. Attaching an IGW to your VPC and adding a default route in a subnet's route table allows resources in that subnet with public IPs to communicate with the outside world.

**Detailed Answer:**
An Internet Gateway allows resources in a VPC to communicate with the internet when their routing and public-address requirements are satisfied. It does not automatically make every resource publicly reachable.

---

## Q37. What is a NAT Gateway?

**🔑 Keywords:** Network Address Translation, Elastic IP, private subnets, outbound-only connectivity, patch/package downloads

**Beginner-Friendly Answer:**
A NAT (Network Address Translation) Gateway is a managed service placed in a public subnet that lets EC2 instances or EKS worker nodes in *private* subnets download software updates and contact external APIs without allowing incoming internet traffic to reach them. It acts like a secure one-way exit door.

**Detailed Answer:**
A NAT Gateway enables resources in private subnets to initiate supported outbound connections to external networks without accepting unsolicited inbound connections through that NAT Gateway.

### Cross-question: Why do private EKS nodes need outbound connectivity?

**Answer:**
They may need to download container images, reach external services, or access public AWS endpoints when suitable VPC endpoints or other private connectivity are not configured.

---

## Q38. What is a route table?

**🔑 Keywords:** routing rules, destination CIDR, target (IGW, NAT, VGW, peering), subnet association

**Beginner-Friendly Answer:**
A route table is like a set of road signs inside your VPC. Every time a packet leaves a server, the route table determines where it should go: "If the destination is within `10.0.0.0/16`, stay local inside the VPC. If the destination is anywhere else (`0.0.0.0/0`), send it to the Internet Gateway or NAT Gateway."

**Detailed Answer:**
A route table defines where network traffic should be directed. Each route specifies a destination network and a target, such as a local VPC route, Internet Gateway, NAT Gateway, or transit component.

---

## Q39. What is the difference between a security group and a network ACL?

**🔑 Keywords:** Security Group (stateful, instance/ENI level, allow rules only), Network ACL (stateless, subnet level, allow & deny rules, numbered evaluation)

**Beginner-Friendly Answer:**
- **Security Group:** A stateful virtual firewall attached to individual EC2 instances or network interfaces (ENIs). You only write "Allow" rules. Because it's stateful, if you allow inbound traffic on port 80, the return outbound traffic is automatically permitted.
- **Network ACL (NACL):** A stateless firewall that guards the entire subnet perimeter. It evaluates numbered rules in order and requires you to explicitly configure both inbound and outbound rules.

**Detailed Answer:**
A security group is a stateful virtual firewall associated with supported resources or network interfaces. A network ACL is a stateless subnet-level traffic filter.

### Cross-question: If a security group allows inbound traffic, is that enough?

**Answer:**
Not necessarily. Routing, network ACLs, operating-system firewalls, listener configuration, and application health can still prevent access.

---

## Q40. What is IAM?

**🔑 Keywords:** Identity and Access Management, users, groups, roles, policies, temporary STS credentials, least privilege

**Beginner-Friendly Answer:**
AWS IAM (Identity and Access Management) controls *who* can access your AWS account and *what* actions they can perform. Instead of creating permanent access keys with passwords (which can leak onto GitHub), you attach IAM Roles to EC2 instances and Kubernetes pods. IAM Roles use temporary, auto-rotating security tokens to perform actions safely.

**Detailed Answer:**
AWS Identity and Access Management controls authentication and authorization for AWS resources. IAM users, roles, policies, and identity federation help determine which actions an identity is allowed to perform.

### Cross-question: Why use an IAM role rather than hardcoding AWS access keys?

**Answer:**
Roles provide temporary credentials and can reduce the need to store long-lived access keys. Workloads should receive only the permissions necessary for their tasks.

---

## Q41. What is Amazon EKS?

**🔑 Keywords:** Elastic Kubernetes Service, managed control plane, high availability across AZs, worker nodes (managed, self-managed, Karpenter)

**Beginner-Friendly Answer:**
Amazon EKS (Elastic Kubernetes Service) is AWS's managed Kubernetes service. Running Kubernetes control planes yourself (API server, etcd, controller manager) is notoriously difficult. EKS manages and backs up the entire Kubernetes control plane across 3 Availability Zones automatically, letting you focus on running your containerized applications on worker nodes.

**Detailed Answer:**
Amazon Elastic Kubernetes Service is a managed Kubernetes service. AWS manages the Kubernetes control plane, while the customer remains responsible for workload configuration and important aspects of cluster operations, networking, security, and capacity.

### Cross-question: Does EKS manage all worker-node operations for you?

**Answer:**
Not in every setup. Responsibility depends on whether you use managed node groups, self-managed nodes, or another supported compute option.

---

## Q42. What is an Application Load Balancer?

**🔑 Keywords:** ALB, Layer 7 load balancer, HTTP/HTTPS, path-based routing, host-based routing, target groups, health checks

**Beginner-Friendly Answer:**
An Application Load Balancer (ALB) inspects application-layer (HTTP/HTTPS) traffic. It reads the domain name (e.g. `api.myshop.com`) and the URL path (e.g. `/orders` or `/products`), terminates TLS encryption, and smartly routes requests to the correct backend target groups based on those rules.

**Detailed Answer:**
An ALB is a Layer 7 load balancer that supports HTTP and HTTPS traffic. It can route requests based on hostnames, paths, and other supported rule conditions.

### Cross-question: What is the difference between an ALB and a Network Load Balancer?

**Answer:**
An ALB focuses on application-layer traffic such as HTTP and HTTPS. An NLB operates at Layer 4 and is designed for TCP, UDP, TLS, and related connection-level use cases.

---

## Q43. What is Route 53?

**🔑 Keywords:** AWS DNS service, domain registration, latency/failover routing, TTL caching, authoritative nameservers

**Beginner-Friendly Answer:**
Amazon Route 53 is AWS's global, highly available Domain Name System (DNS) web service. It connects user-friendly domain names (like `chethan-store.com`) to AWS resources like ALBs, CloudFront distributions, or S3 buckets, and offers advanced routing policies like latency-based or geo-location routing.

**Detailed Answer:**
Amazon Route 53 is an AWS DNS service that supports domain registration, DNS hosting, and routing policies.

### Cross-question: Why does a domain sometimes fail to resolve after changing DNS records?

**Answer:**
Resolvers may cache previous answers according to their TTLs. Incorrect records, incorrect authoritative nameservers, or DNS validation issues can also cause problems.

---

## Q44. What is AWS Certificate Manager (ACM)?

**🔑 Keywords:** ACM, SSL/TLS certificates, free public certificates, automatic renewal, ALB listener attachment

**Beginner-Friendly Answer:**
AWS Certificate Manager (ACM) provisions, manages, and automatically renews public SSL/TLS certificates for your domains for free. Once issued, you attach the certificate directly to your Application Load Balancer listener on port 443 to deliver encrypted HTTPS traffic to your shoppers.

**Detailed Answer:**
ACM manages supported TLS certificates used with AWS services. A certificate can be used to enable HTTPS on a supported endpoint when its validation and service configuration are correct.

### Cross-question: What is the difference between issuing a certificate and using it?

**Answer:**
Issuing a certificate establishes that it has been validated and issued. It still needs to be associated with the relevant listener or supported service, and DNS and routing must direct traffic to the correct endpoint.

---

## Q45. What is ExternalDNS?

**🔑 Keywords:** Kubernetes operator, automated Route 53 management, declarative DNS, CRDs

**Beginner-Friendly Answer:**
ExternalDNS is an in-cluster Kubernetes controller that synchronizes exposed Kubernetes Services and Gateways with DNS providers like AWS Route 53. Whenever you deploy a new service with a public hostname, ExternalDNS automatically makes the AWS API call to create or update the Route 53 record, eliminating manual DNS updates.

**Detailed Answer:**
ExternalDNS is a controller that can create and update DNS records in supported DNS providers based on configured Kubernetes resources and policies.

### Cross-question: Does ExternalDNS automatically make an application publicly accessible?

**Answer:**
No. DNS records only resolve names. The load balancer, listeners, routing, network access, and healthy application backends must also be configured.
