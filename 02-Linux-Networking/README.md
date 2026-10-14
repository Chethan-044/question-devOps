# Part 2: Linux and Networking Fundamentals

> These questions are important because DevOps engineers need to understand what happens underneath their tools.

---

## Q5. What is Linux, and why is it important in DevOps?

**🔑 Keywords:** operating system, servers, containers, CLI, file permissions, processes, package management, systemd

**Beginner-Friendly Answer:**
Linux is like the engine under the hood of almost everything in DevOps. Most servers, containers, and cloud machines run Linux. As a DevOps engineer, you need to be comfortable with the Linux command line — checking what's running, reading logs, managing files, and installing software. In our project, Linux runs on the Bastion Host, inside containers, on Jenkins, and on Kubernetes nodes.

**Detailed Answer:**
Linux is an operating system widely used to run servers, containers, cloud workloads, and DevOps tools.

In our architecture, Linux is relevant to the Bastion Host, container environments, Jenkins, Kubernetes nodes, and many of the monitoring components.

A DevOps engineer should understand file permissions, processes, networking, package installation, services, logs, and shell commands.

### Cross-question: How do you check running processes?

```bash
ps aux
```

This displays processes running on a Linux system.

### Cross-question: How do you check disk usage?

```bash
df -h
```

This displays filesystem capacity and available space in a human-readable format.

### Cross-question: How do you check memory usage?

```bash
free -h
```

This displays memory and swap usage.

### Cross-question: How do you check logs for a Linux service?

For a systemd-managed service:

```bash
sudo journalctl -u jenkins -n 100
```

This displays recent logs for Jenkins.

The command assumes Jenkins is installed as a systemd service.

---

## Q6. What is SSH?

**🔑 Keywords:** Secure Shell, encrypted connection, remote access, private key, public key, port 22, authorized_keys

**Beginner-Friendly Answer:**
SSH (Secure Shell) is like a secure tunnel that lets you remotely control another computer through the terminal. Instead of physically sitting in front of a server, you use SSH to connect to it over the network. It encrypts everything so nobody can spy on your session. You use a private key (kept secret on your machine) and a public key (placed on the server) to prove your identity without typing a password every time.

**Detailed Answer:**
SSH stands for Secure Shell. It provides an encrypted connection for remotely accessing and administering a machine.

In our project, SSH is relevant to administering a Bastion Host.

For example:

```bash
ssh -i ~/.ssh/ecommerce-bastion-key ec2-user@SERVER_IP
```

Here, `-i` specifies the private key, `ec2-user` is the remote username for a typical Amazon Linux instance, and `SERVER_IP` is the server's reachable address.

Our Floci lab has a different connection arrangement: SSH may be exposed through a host port such as 2200, and the mock container may use root as the SSH user. That behavior is specific to the emulator and should not be confused with the normal login details of a real EC2 operating system.

### Cross-question: What is the difference between a public and private key?

The private key stays with the person or system initiating the connection. The corresponding public key is placed in the server's authorized keys.

During public-key authentication, SSH verifies that the client possesses the private key corresponding to an authorized public key.

You should never commit your private key to GitHub or put it in a container image.

---

## Q7. What is a port?

**🔑 Keywords:** network service identifier, TCP, port number, listening, well-known ports (22, 80, 443, 5432)

**Beginner-Friendly Answer:**
Think of an IP address as the address of a building, and a port as the door number inside that building. Each application "listens" on a specific port number. For example, SSH uses door 22, HTTPS uses door 443, and PostgreSQL uses door 5432. In our project, each microservice listens on its own port so they don't conflict with each other.

**Detailed Answer:**
A port identifies a network service on a machine.

For example, SSH normally uses TCP port 22, HTTPS uses TCP port 443, and PostgreSQL normally uses TCP port 5432.

In our application, the services use these ports:

| Component | Port |
|-----------|------|
| User Service | 8001 |
| Product Service | 8002 |
| Order Service | 8003 |
| Storage Service | 8004 |
| PostgreSQL | 5432 |

These are the ports configured for our application. They are not universal requirements for the corresponding technologies.

### Cross-question: What is the difference between a port and an IP address?

An IP address identifies a network endpoint. A port identifies a service endpoint on that machine.

For example, `10.0.1.10:8003` means port 8003 on the machine or network endpoint identified by `10.0.1.10`.

### Cross-question: What happens if the application is listening on port 8003 but the Service targets port 8002?

Requests may fail because Kubernetes forwards traffic to the wrong destination port.

We must verify the application's listening port, the Kubernetes Service's `targetPort`, and the container's configuration.

---

## Q8. What is DNS?

**🔑 Keywords:** Domain Name System, name resolution, domain to IP, A record, CNAME, Route 53, ExternalDNS

**Beginner-Friendly Answer:**
DNS is like the phonebook of the internet. Instead of remembering a long IP address (like 54.23.100.45), you just type a friendly name (like shop.example.com) and DNS translates that name into the actual IP address your browser needs to connect to. In our project, AWS Route 53 acts as our DNS phonebook, and ExternalDNS automatically updates it when things change in Kubernetes.

**Detailed Answer:**
DNS stands for Domain Name System. It translates domain names into information that clients use to locate services.

For example, a user can access an application using a domain name instead of remembering an IP address.

In our target architecture, Route 53 manages DNS records, and ExternalDNS can automatically create or update supported DNS records based on Kubernetes resources.

### Cross-question: What is the difference between DNS and HTTPS?

DNS helps a client locate a destination. HTTPS protects HTTP communication using TLS.

DNS does not encrypt application traffic.

### Cross-question: What is a DNS record?

A DNS record defines information associated with a domain name.

Common examples include:
- **A:** Maps a name to an IPv4 address.
- **AAAA:** Maps a name to an IPv6 address.
- **CNAME:** Aliases one DNS name to another.
- **NS:** Identifies authoritative name servers.
- **TXT:** Stores text information, often used for verification.

For an ALB-backed application, DNS can direct the application domain to the load balancer.
