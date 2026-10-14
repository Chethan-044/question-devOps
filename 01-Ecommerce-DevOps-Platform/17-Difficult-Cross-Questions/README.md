# Part 17: Difficult Cross-Questions Interviewers May Ask

> These questions are designed to expose whether someone understands the system beyond surface-level definitions.

---

## Q76. If Argo CD is responsible for deployment, why do we need Jenkins?

**🔑 Keywords:** separation of concerns, CI vs CD, image creation vs state reconciliation

**Beginner-Friendly Answer:**
Jenkins and Argo CD have completely different jobs. Jenkins handles the **Continuous Integration (CI)** — compiling code, running tests, scanning images with Trivy, and pushing the final image to GHCR. Argo CD does not build code or run unit tests; it only handles **Continuous Delivery (CD)** via GitOps, making sure that what's declared in Git is what runs in Kubernetes. Keeping CI and CD separate keeps your pipeline clean and secure.

**Detailed Answer:**
Jenkins performs CI activities such as building, testing, scanning, and publishing images. Argo CD reconciles Kubernetes resources with the desired state stored in Git.

They have different responsibilities in our architecture.

---

## Q77. If Kubernetes can restart failed Pods, why do we need monitoring?

**🔑 Keywords:** application errors vs container crashes, latency, business correctness, silent failures

**Beginner-Friendly Answer:**
Kubernetes only knows if a container literally crashes or exits with an error code. It has no idea if your app is returning HTTP 500 errors to customers, if database queries are taking 15 seconds instead of 50ms, or if payment transactions are silently failing. Monitoring with Prometheus and Grafana tracks real user experience, application latency, and business health.

**Detailed Answer:**
Kubernetes can respond to certain workload failures, but it cannot automatically explain every application error or guarantee business-level correctness.

Monitoring helps us detect problems such as high latency, error rates, resource exhaustion, and failed dependencies.

---

## Q78. If HPA scales Pods, why do we need Karpenter?

**🔑 Keywords:** Pod scaling vs Node scaling, capacity exhaustion, pending Pods, compute provisioning

**Beginner-Friendly Answer:**
HPA can request 100 new Pods, but if your physical or virtual EC2 servers are 100% full, those 100 Pods will just sit in "Pending" status and do nothing. You need Karpenter to provision additional EC2 servers so those pending Pods actually have a place to run. HPA adds the passengers; Karpenter provides more buses.

**Detailed Answer:**
HPA increases or decreases the number of Pods. Karpenter can provision additional nodes when the cluster needs more capacity.

More Pods do not help if there is nowhere to schedule them.

---

## Q79. If we have a load balancer, why do we need Kubernetes Services?

**🔑 Keywords:** external ingress vs internal discovery, ephemeral Pod IPs, service abstraction

**Beginner-Friendly Answer:**
An external load balancer (like AWS ALB) directs internet traffic into the cluster. But once inside, Pods need to talk to each other (like Order Service calling Product Service). Because Pod IPs change constantly, internal Kubernetes Services provide stable DNS names and internal load balancing between microservices.

**Detailed Answer:**
An external load balancer provides an entry point into the application. Kubernetes Services provide stable internal endpoints for accessing groups of Pods.

They operate at different parts of the networking architecture.

---

## Q80. If we use HTTPS, do we still need a security group?

**🔑 Keywords:** encryption in transit vs network access control, packet payload vs connection origin

**Beginner-Friendly Answer:**
Yes! HTTPS encrypts the contents of the message so eavesdroppers can't read it. A Security Group acts like a door: it controls *who* is allowed to connect in the first place (e.g., blocking malicious IP ranges or limiting SSH access). Encryption without access control means anyone can still attack your server over an encrypted connection.

**Detailed Answer:**
Yes. HTTPS protects HTTP communication using TLS, but security groups control which network connections are permitted.

Encryption does not replace network access control.

---

## Q81. If Terraform created a resource, why would we need to inspect it in AWS?

**🔑 Keywords:** configuration drift, runtime failures, provider abstractions, AWS-side errors

**Beginner-Friendly Answer:**
Terraform only tells AWS "please create this resource." But resources can fail after creation due to runtime issues (like an EC2 instance failing system status checks, a subnet running out of IPs, or IAM permission errors). Inspecting the AWS console or CloudWatch logs helps debug runtime behavior that Terraform cannot see.

**Detailed Answer:**
Terraform describes and manages infrastructure, but resources can fail to provision or encounter runtime problems.

We still need to inspect resource status, events, logs, permissions, and actual configuration when troubleshooting.

---

## Q82. If an application image has no known Trivy vulnerabilities, is it completely secure?

**🔑 Keywords:** zero-day vulnerabilities, application logic bugs, supply-chain risks, defense in depth

**Beginner-Friendly Answer:**
No. Trivy only knows about *already discovered* vulnerabilities listed in public databases. It cannot detect zero-day exploits (new bugs not yet documented), broken business logic in your code (like leaving an admin endpoint unauthenticated), or misconfigured permissions. Security requires multiple layers (defense in depth).

**Detailed Answer:**
No. A vulnerability scan can identify known issues within its detection scope, but it cannot prove that an application is completely secure.

We still need secure coding, access control, secrets management, runtime security, dependency management, and ongoing monitoring.

---

## Q83. If the frontend is working, does that mean the entire application is healthy?

**🔑 Keywords:** client-side rendering, decoupled architectures, backend dependency health, distributed tracing

**Beginner-Friendly Answer:**
No! The React frontend is just static HTML/CSS/JavaScript. It can load beautifully in your browser, but the moment you click "Checkout" or "View Products", the backend APIs might return 500 errors or time out. A healthy frontend does not guarantee healthy backend microservices or database connections.

**Detailed Answer:**
No. The frontend may load successfully while API services or the database are unavailable.

We should test each service and its dependencies, as well as complete business workflows such as order creation.

---

## Q84. If all Pods are Running, does that mean the application is working?

**🔑 Keywords:** Pod phase vs application health, readiness probes, deadlocks, dependency failures

**Beginner-Friendly Answer:**
No! "Running" simply means the container process has started and hasn't exited. The application could be frozen in a deadlock, failing to connect to PostgreSQL, or crashing on every API call. That is why readiness probes and monitoring metrics are essential — they verify that the app is actually serving requests correctly.

**Detailed Answer:**
No. Running is a Pod phase, not proof that the application is functioning correctly.

The application may return errors, fail readiness checks, or be unable to communicate with its dependencies.

We need readiness status, logs, service connectivity, application tests, and relevant metrics.

---

## Q85. What would you improve first if this project were going into production?

**🔑 Keywords:** production readiness, secret management (Vault/AWS SM), RDS Multi-AZ, CI/CD separation, least privilege, backup & restore

**Beginner-Friendly Answer:**
If taking this project to production, my top priorities would be:
1. **Move Database to Managed Service:** Replace containerized PostgreSQL with AWS RDS PostgreSQL Multi-AZ for automated backups and failover.
2. **Isolate CI Server:** Move Jenkins off the Bastion Host onto dedicated, autoscaled build agents with strict IAM roles.
3. **Secrets Management:** Use AWS Secrets Manager or HashiCorp Vault instead of plaintext Kubernetes secrets.
4. **Resilience & Testing:** Set up canary deployments with Argo Rollouts and automated synthetic monitoring.

**Detailed Answer:**
I would prioritize security, reliability, and operational visibility.

First, I would ensure credentials and registry access are handled securely, use least-privilege permissions, configure appropriate network policies, and protect administrative access.

Next, I would configure reliable health checks, resource requests and limits, autoscaling, monitoring, alerting, and tested backup and recovery procedures.

I would also verify that CI/CD produces traceable artifacts, that deployment and rollback procedures work, and that the architecture can recover from failures in critical dependencies.

Finally, I would evaluate the cost and complexity of the infrastructure to ensure that the design is appropriate for the application's actual requirements.
