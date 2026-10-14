# Part 15: Questions About Decisions in Your Architecture

> These questions are especially valuable because interviewers often challenge design choices.

---

## Q71. Why did you choose GHCR instead of Amazon ECR?

**🔑 Keywords:** GitHub Container Registry, GitHub ecosystem, cost, authentication, Amazon ECR trade-offs, developer workflow

**Beginner-Friendly Answer:**
We picked GHCR (GitHub Container Registry) because our code is already hosted on GitHub, so having our container images right alongside our code keeps authentication and repository management in one place. Amazon ECR is also a fantastic choice, especially if your whole infrastructure is inside AWS. In an interview, explain that the choice was driven by ease of integration with our GitHub setup, but acknowledge that ECR is equally valid in AWS-centric production setups.

**Detailed Answer:**
We selected GHCR because it integrates with GitHub and meets our requirement for storing container images separately from the Kubernetes cluster.

Jenkins can authenticate to GHCR, publish versioned images, and allow the cluster to pull them with suitable credentials.

Amazon ECR would also be a valid choice, especially for an AWS-centric architecture.

The decision depends on access management, cost, integration, operational requirements, and the team's preferred tooling.

---

## Q72. Why did you choose Jenkins on a Bastion Host?

**🔑 Keywords:** learning architecture vs production, bastion dual-role, single point of failure (SPOF), cost optimization, hands-on Linux administration

**Beginner-Friendly Answer:**
In our hands-on learning setup, placing Jenkins on the Bastion Host allowed us to practice managing a Linux server, setting up security groups, and operating a CI server on a single machine without inflating cloud costs. However, in an interview, you should immediately clarify: "In a real production environment, I would NEVER run Jenkins on the Bastion. A Bastion should be a locked-down jump box with minimal software, while Jenkins should be isolated on its own server or run on Kubernetes with ephemeral build agents."

**Detailed Answer:**
For this learning architecture, we placed Jenkins on a Bastion Host to combine controlled administrative access with CI-server operation.

This lets us practise administering a Linux host and operating a CI server as part of the project.

However, a Bastion is primarily an administrative access point. In a production environment, I would carefully evaluate whether Jenkins should be separated from the Bastion because Jenkins has a different security profile and operational workload.

### Follow-up: Is this the ideal production architecture?

Not automatically. It is a learning-oriented design that demonstrates the concepts. A production design should consider Jenkins isolation, availability, credential boundaries, network access, backups, and build-agent security.

---

## Q73. Why did you choose Terraform instead of CloudFormation?

**🔑 Keywords:** multi-cloud, state management, provider ecosystem, declarative syntax, industry standard, vendor neutrality

**Beginner-Friendly Answer:**
While AWS CloudFormation is great if you ONLY use AWS, Terraform is cloud-agnostic and supports hundreds of providers (AWS, GitHub, Kubernetes, Datadog, etc.). Learning Terraform gives you skills that transfer anywhere, allows you to manage non-AWS resources in the same code repo, and supports running against local emulators like Floci or LocalStack for fast, low-cost local testing.

**Detailed Answer:**
We chose Terraform because it provides a consistent Infrastructure as Code workflow and supports multiple providers.

It allows us to define networking, compute, and other infrastructure in reusable configuration files.

CloudFormation is also a valid option for AWS infrastructure. The choice depends on existing standards, provider requirements, team expertise, and operational preferences.

---

## Q74. Why use both Route 53 and ExternalDNS?

**🔑 Keywords:** DNS provider vs automated controller, Kubernetes CRDs, seamless domain routing, GitOps DNS management

**Beginner-Friendly Answer:**
They aren't competitors; they work together as a team! Route 53 is the actual DNS server hosting your domain records. ExternalDNS is a Kubernetes controller that watches your HTTPRoutes/Services and automatically calls Route 53's API to create or update DNS records whenever a new service is deployed. Route 53 stores the records; ExternalDNS saves you from creating them manually.

**Detailed Answer:**
Route 53 is the DNS service that stores and serves DNS records.

ExternalDNS automates DNS-record management by observing configured resources and creating or updating corresponding records in a supported DNS provider.

They are not competing alternatives: Route 53 provides DNS, while ExternalDNS automates record management.

---

## Q75. Why use both Prometheus/Grafana and Elasticsearch/Kibana?

**🔑 Keywords:** metrics vs logs, complementary observability, numbers vs text, root-cause analysis, latency vs error traces

**Beginner-Friendly Answer:**
Metrics and logs answer two different questions:
- **Prometheus + Grafana** tracks **metrics** (numbers): "My Order Service error rate suddenly jumped to 15% at 2:05 PM" — it tells you *THAT* something is wrong.
- **Elasticsearch + Kibana** tracks **logs** (words/events): "NullPointerException at line 42: database connection timeout" — it tells you *WHY* it went wrong.
You need both to effectively troubleshoot distributed microservices.

**Detailed Answer:**
Prometheus and Grafana primarily support metrics collection and visualization.

Elasticsearch and Kibana support log indexing, search, and analysis.

Metrics help identify that a system is unhealthy, while logs often help explain why it is unhealthy.

Using both gives us complementary ways to observe application behavior.
