# Part 3: AWS Fundamentals

> AWS services form the cloud foundation of the project architecture.

---

## Q9. What is AWS?

**🔑 Keywords:** cloud platform, computing, storage, networking, VPC, EC2, EKS, ALB, Route 53, ACM, NAT Gateway, pay-as-you-go

**Beginner-Friendly Answer:**
AWS (Amazon Web Services) is like renting a massive data center on demand. Instead of buying and managing your own servers, you use AWS services — virtual machines (EC2), managed Kubernetes (EKS), load balancers (ALB), DNS (Route 53), and more. You only pay for what you use. In our project, AWS provides the cloud infrastructure where our application runs.

**Detailed Answer:**
AWS is a cloud platform that provides services for computing, storage, networking, databases, security, and application deployment.

In our architecture, we use or plan to use AWS services such as VPC, EC2, EKS, ALB, Route 53, ACM, and NAT Gateway.

These services allow us to build infrastructure without managing every physical server ourselves.

### Cross-question: Why not deploy everything on one EC2 instance?

A single EC2 instance can host an application, but it creates limitations around availability, independent scaling, deployment isolation, and infrastructure management.

EKS provides Kubernetes orchestration for containerized workloads, while services such as ALB distribute incoming traffic.

However, EKS is not automatically cheaper or simpler. For a small application, a single server or managed container service may be more appropriate.

---

## Q10. What is a VPC?

**🔑 Keywords:** Virtual Private Cloud, isolated network, CIDR block, 10.0.0.0/16, subnets, routing, network control

**Beginner-Friendly Answer:**
A VPC (Virtual Private Cloud) is your own private network inside AWS. Think of it as building a walled campus — you decide which buildings (subnets) go where, who can enter (security groups), and how traffic flows (route tables). Our project's VPC uses the address range 10.0.0.0/16, which gives us plenty of IP addresses to organize our resources.

**Detailed Answer:**
A Virtual Private Cloud is a logically isolated network in AWS where we place cloud resources.

Our project VPC uses the CIDR block:

```
10.0.0.0/16
```

That provides a private IPv4 address range for the network.

We divided it into public and private subnets so that resources can have different network exposure and routing arrangements.

### Cross-question: Why do we need a VPC?

A VPC provides control over IP addressing, routing, subnets, and network security.

It lets us separate resources and control how they communicate with other networks and the internet.

### Cross-question: What is a CIDR block?

CIDR is a notation for describing an IP address range.

For example:

```
10.0.0.0/16
```

represents the range 10.0.0.0 through 10.0.255.255 in a conventional IPv4 subnet calculation.

A /24 contains 256 IPv4 addresses in total.

---

## Q11. What is the difference between public and private subnets?

**🔑 Keywords:** Internet Gateway route, public IP, NAT Gateway, inbound/outbound traffic, network exposure, routing table

**Beginner-Friendly Answer:**
A public subnet is like a building with a front door facing the street — anyone can walk up to it (if allowed). A private subnet is like a building hidden behind the campus wall — it can reach the outside world through a special exit (NAT Gateway), but strangers can't walk in directly. In our project, the Bastion Host sits in a public subnet, while the actual application workloads run in private subnets for security.

**Detailed Answer:**
A public subnet has a route to an Internet Gateway for internet-bound traffic. A resource also needs a suitable public address and appropriate security rules to be reachable from the internet.

A private subnet normally has no direct route for unsolicited inbound internet traffic. It can use a NAT Gateway for outbound IPv4 internet access.

In our project:

| Subnet | CIDR | Intended role |
|--------|------|---------------|
| Public subnet 1 | 10.0.101.0/24 | Bastion and public-facing networking |
| Public subnet 2 | 10.0.102.0/24 | Additional public-facing networking |
| Private subnet 1 | 10.0.1.0/24 | Private workloads |
| Private subnet 2 | 10.0.2.0/24 | Private workloads |

The exact placement of EKS control-plane-related resources and worker nodes depends on the EKS networking configuration.

### Cross-question: Is a subnet public simply because it has a public IP?

No. The subnet's routing configuration matters. A subnet normally has a route to an Internet Gateway to be considered public.

A public IP alone does not guarantee internet reachability; routing and security rules must also allow the traffic.

---

## Q12. What is an Internet Gateway?

**🔑 Keywords:** IGW, VPC to internet, default route, 0.0.0.0/0, public subnet routing

**Beginner-Friendly Answer:**
An Internet Gateway (IGW) is the main gate connecting your VPC to the internet. Without it, nothing inside your VPC can reach the outside world (and vice versa). In our project, the public subnets have a route that says "for any internet-bound traffic, go through the Internet Gateway."

**Detailed Answer:**
An Internet Gateway connects a VPC to the internet when routing and resource configuration permit it.

In our project, the public route table has a default route:

```
Destination: 0.0.0.0/0
Target: Internet Gateway
```

The route means that traffic destined for IPv4 addresses outside more-specific routes is sent to the Internet Gateway.

### Cross-question: Can a private EC2 instance use the Internet Gateway directly?

A typical private instance without a public address does not use the Internet Gateway directly for ordinary outbound IPv4 internet access. It generally uses a NAT Gateway in a public subnet.

---

## Q13. What is a NAT Gateway?

**🔑 Keywords:** Network Address Translation, outbound internet, private subnet, Elastic IP, no inbound, one-way door

**Beginner-Friendly Answer:**
A NAT Gateway is like a one-way door for private subnets. Resources inside private subnets can send requests out to the internet (like downloading updates), but strangers on the internet can't send requests back in through that door. The NAT Gateway sits in a public subnet and needs an Elastic IP (a fixed public IP) to communicate with the outside world.

**Detailed Answer:**
A NAT Gateway allows resources in private subnets to initiate outbound IPv4 connections to external destinations without accepting unsolicited inbound internet connections through that NAT Gateway.

For example, a private workload may need to download packages or contact an external API.

In our architecture, we configured a NAT Gateway in a public subnet and a default route from the private route table to the NAT Gateway.

### Cross-question: Why does a NAT Gateway need an Elastic IP?

For a public NAT Gateway, an Elastic IP provides its public IPv4 address for internet communication.

### Cross-question: Does NAT replace a security group?

No. NAT is part of the networking and address-translation path. Security groups control permitted traffic to and from associated resources.

### Cross-question: What if the NAT Gateway fails?

Resources depending on it may lose outbound internet connectivity. A production design must consider availability, routing, and the cost of deploying NAT across multiple Availability Zones.

---

## Q14. What is a Security Group?

**🔑 Keywords:** virtual firewall, stateful, inbound/outbound rules, allow rules only, network interface, least privilege

**Beginner-Friendly Answer:**
A Security Group is like a bouncer at the door of your server. It checks every incoming and outgoing connection against a list of rules you define. The key thing is it's "stateful" — if you let someone in, the bouncer automatically lets their response go back out. You can only write "allow" rules (not "deny"), so anything not explicitly allowed is blocked by default.

**Detailed Answer:**
A Security Group is a stateful virtual firewall associated with resources such as EC2 instances and certain network interfaces.

It controls allowed inbound and outbound traffic.

For the Bastion, we need an SSH rule. For a production system, SSH access should normally be restricted to approved source IP addresses or replaced with a more controlled administration method.

### Cross-question: What does stateful mean?

If a connection is permitted by a security group, return traffic for that connection is automatically permitted, subject to the security group's stateful behavior.

### Cross-question: What is the difference between a Security Group and a Network ACL?

A Security Group is stateful and is associated with network interfaces.

A Network ACL is stateless and applies at the subnet level. Its inbound and outbound rules are evaluated separately.
