# Section 16: Security Theory

---

## Q152. What is the principle of least privilege?

**🔑 Keywords:** least privilege, minimal necessary access, blast radius reduction, IAM roles, zero trust

**Beginner-Friendly Answer:**
The principle of least privilege means giving users, applications, and scripts ONLY the bare minimum permissions they actually need to perform their jobs — and absolutely nothing extra. If the User Service only needs to read user profiles, do not give it permission to delete databases or create AWS EC2 servers. If that service is ever compromised, the attacker can't do anything beyond what that service was specifically allowed to do.

**Detailed Answer:**
It means giving each user, service, or workload only the permissions needed to perform its task.

---

## Q153. What is Kubernetes RBAC?

**🔑 Keywords:** Role-Based Access Control, Role, ClusterRole, RoleBinding, ClusterRoleBinding, ServiceAccount

**Beginner-Friendly Answer:**
Kubernetes RBAC (Role-Based Access Control) controls who can do what inside the cluster. You define a **Role** (e.g. "can view Pods and read logs in the `dev` namespace"), and then use a **RoleBinding** to attach that Role to a human user or a ServiceAccount used by an automated tool. A `ClusterRole` applies cluster-wide across all namespaces.

**Detailed Answer:**
Role-Based Access Control determines which users, groups, and service accounts can perform specific actions on Kubernetes resources. Roles and ClusterRoles define permissions; RoleBindings and ClusterRoleBindings associate those permissions with identities.

---

## Q154. What is a Kubernetes NetworkPolicy?

**🔑 Keywords:** internal pod firewall, ingress / egress rules, CNI plugin requirement (Calico, Cilium), microsegmentation

**Beginner-Friendly Answer:**
By default in Kubernetes, all Pods can talk to all other Pods without restrictions. A **NetworkPolicy** acts like an internal firewall between Pods: you can create a rule saying: *"Only the Storage Service Pod is allowed to talk to PostgreSQL on port 5432; block all other Pods."* Note: NetworkPolicies only work if your Kubernetes cluster is using a network CNI plugin (like Calico or Cilium) that supports policy enforcement.

**Detailed Answer:**
A NetworkPolicy specifies permitted network traffic to or from selected Pods. It only has the intended effect when the cluster's networking implementation supports and enforces the relevant policies.

---

## Q155. What is the difference between authentication and authorization?

**🔑 Keywords:** authentication (Who are you? / identity verification) vs authorization (What are you allowed to do? / permissions)

**Beginner-Friendly Answer:**
- **Authentication (AuthN):** Proving *WHO* you are. (Showing your passport at airport security, or typing your username and password).
- **Authorization (AuthZ):** Determining *WHAT* you are allowed to do. (Having a ticket that allows you into Business Class vs Economy, or an IAM policy that allows you to read files but not delete them).

**Detailed Answer:**
Authentication verifies who or what an identity is. Authorization determines what that identity is allowed to do.

---

## Q156. What is secret rotation?

**🔑 Keywords:** credential lifecycle, automated rotation, zero-downtime secret rollover, blast radius mitigation

**Beginner-Friendly Answer:**
Secret rotation is the practice of regularly replacing passwords, database credentials, API tokens, and TLS certificates with new ones on a scheduled basis (e.g. every 30 or 90 days), or immediately after a suspected leak. When automated properly (e.g. via AWS Secrets Manager), the database and applications roll over to the new credentials seamlessly with zero downtime.

**Detailed Answer:**
Secret rotation means replacing credentials periodically or after an incident and updating dependent workloads and systems safely.

---

## Q157. What is the difference between encryption at rest and encryption in transit?

**🔑 Keywords:** encryption at rest (KMS, EBS volume encryption, database storage) vs encryption in transit (TLS, HTTPS, mTLS)

**Beginner-Friendly Answer:**
- **Encryption in Transit:** Scrambles data as it flies across the network wire (e.g., using TLS/HTTPS) so hackers eavesdropping on the network cannot read credit card numbers or passwords.
- **Encryption at Rest:** Scrambles data while it is stored on physical hard drives or S3 buckets (e.g., using AWS KMS AES-256). If someone physically steals the hard drive out of the AWS data center, they cannot read any files.

**Detailed Answer:**
Encryption at rest protects stored data. Encryption in transit protects data as it moves across a network, commonly through TLS.

---

## Q158. What is a software supply chain?

**🔑 Keywords:** source code, open-source dependencies, CI build servers, registries, image signing (Cosign), provenance

**Beginner-Friendly Answer:**
The software supply chain includes everything and everyone that touches your code before it reaches production: the developer's laptop, third-party libraries downloaded from `npm` or `pip`, the Jenkins CI build server, the Docker base images, and the container registry. If a hacker poisons an open-source library that you pull into your project, your entire production system can be compromised.

**Detailed Answer:**
The software supply chain includes source code, dependencies, build systems, artifacts, registries, and the processes used to deliver software. Compromise at any of these points can affect the final deployed application.

### Cross-question: How do you improve container supply-chain security?

**Answer:**
Use controlled builds, dependency and image scanning, protected credentials, restricted publishing permissions, traceable image versions, provenance or signing where appropriate, and deployment policies that enforce the organization's requirements.
