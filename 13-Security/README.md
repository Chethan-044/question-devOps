# Part 13: Security and Production Readiness

---

## Q59. What is the principle of least privilege?

**🔑 Keywords:** minimum necessary permissions, IAM, RBAC, blast radius reduction, secret scoping, zero trust

**Beginner-Friendly Answer:**
The principle of least privilege means giving every user, service, or script ONLY the minimum keys and access they actually need to do their job — and nothing more. If a service only needs to read product details from the database, don't give it permission to delete tables or manage AWS servers. If that service ever gets hacked, the hacker can't do anything beyond what that specific service was allowed to do, keeping the "blast radius" tiny.

**Detailed Answer:**
Least privilege means giving a user, application, or system only the permissions necessary to perform its job.

For example, the AWS Load Balancer Controller should have the permissions it needs to manage its supported resources, rather than unrestricted administrator access.

Similarly, Jenkins credentials and Kubernetes service accounts should have narrowly scoped permissions.

### Cross-question: How would you secure Jenkins?

I would restrict access to the Jenkins UI, use managed credentials rather than hardcoded secrets, limit permissions, keep plugins and the server updated, and use appropriate isolation for build agents.

I would also review how Jenkins accesses GHCR and any deployment-related systems.

---

## Q60. What is RBAC in Kubernetes?

**🔑 Keywords:** Role-Based Access Control, Role, ClusterRole, RoleBinding, ClusterRoleBinding, ServiceAccount, authorization

**Beginner-Friendly Answer:**
RBAC (Role-Based Access Control) is how Kubernetes decides who is allowed to do what. A developer might have a Role that lets them "view Pod logs in the staging namespace," while an automated deployment tool gets a Role to "update Deployments." Nobody gets admin access unless absolutely required. A `Role` applies to a single namespace, while a `ClusterRole` applies cluster-wide.

**Detailed Answer:**
RBAC stands for Role-Based Access Control.

It determines which identities can perform which actions on Kubernetes resources.

A Role grants permissions within a namespace, while a ClusterRole can define permissions at cluster scope or be used in supported bindings.

### Cross-question: Why not give every service account cluster-admin access?

That would give workloads excessive permissions and increase the impact of a compromised application or misconfigured component.

Each workload should receive only the permissions it requires.

---

## Q61. What is a Kubernetes NetworkPolicy?

**🔑 Keywords:** Pod firewall, ingress/egress filtering, CNI plugin requirement (Calico, Cilium), network isolation, microsegmentation

**Beginner-Friendly Answer:**
By default in Kubernetes, any Pod can talk to any other Pod across the entire cluster. That means if a hacker breaches the React frontend, they could potentially connect directly to the PostgreSQL database! A NetworkPolicy acts like an internal firewall between Pods: you can create a rule saying "Only the Storage Service Pod is allowed to send traffic to PostgreSQL on port 5432; block all other connections."

**Detailed Answer:**
A NetworkPolicy defines rules governing network traffic to and from selected Pods, when the installed network implementation enforces those policies.

In our application, we could restrict PostgreSQL access to the Storage Service and limit other service-to-service communication according to requirements.

### Cross-question: Does creating a NetworkPolicy guarantee that traffic is restricted?

Not necessarily. The cluster's networking implementation must support and enforce NetworkPolicies.

We must verify the behavior of the selected CNI and the actual policy rules.

---

## Q62. Why do containers need resource requests and limits?

**🔑 Keywords:** CPU/memory requests (scheduling), limits (containment), noisy neighbors, OOMKilled, CPU throttling, QoS classes

**Beginner-Friendly Answer:**
Think of requests as a "reservation" and limits as a "speed limit". A **request** tells Kubernetes: "Please guarantee that this container gets at least 250m CPU and 512MB RAM when placing it on a node." A **limit** says: "Never let this container consume more than 1 CPU or 1GB RAM." Without limits, one buggy service with a memory leak can eat all the server's RAM and crash other critical services running on the same machine (the "noisy neighbor" problem).

**Detailed Answer:**
Resource requests tell Kubernetes how much CPU and memory a container needs for scheduling and resource-allocation decisions.

Limits define maximum resource consumption for supported resources.

Requests help the scheduler place workloads. CPU limits can throttle a container, while exceeding a memory limit can lead to an out-of-memory termination.

### Cross-question: What happens if memory limits are too low?

The container may be terminated with an OOM-related reason, causing application interruptions or repeated restarts.

Resource values should be based on observed workload requirements and adjusted through testing.
