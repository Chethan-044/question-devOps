# Section 1: Project Overview and Architecture

> These are some of the first questions an interviewer may ask. Practise these answers before moving to individual tools.

---

## Q1. Tell me about your project.

**🔑 Keywords:** production-oriented, GitOps-driven, microservices, Terraform IaC, Jenkins CI, Docker containers, EKS orchestration, Argo CD, Gateway API, autoscaling, observability

**Beginner-Friendly Answer:**
My project is a modern online shopping platform built with microservices. Instead of one huge program, it is split into independent services (Frontend, User, Product, Order, and Storage). We package each service using Docker, build infrastructure with Terraform, test and scan images using Jenkins, deploy declaratively to AWS EKS using Argo CD (GitOps), route traffic with Gateway API and Application Load Balancer, and monitor everything with Prometheus, Grafana, and the ELK stack.

**Detailed Answer:**
My project is a production-oriented, GitOps-driven microservices application deployed using DevOps tools and AWS infrastructure. The application is divided into independent services, each responsible for a particular function. I use Terraform for infrastructure provisioning, Jenkins for continuous integration, Docker for containerization, Kubernetes on Amazon EKS for orchestration, and Argo CD for GitOps-based deployment. The architecture also includes application routing, DNS, TLS certificates, autoscaling, security scanning, and monitoring.

### Cross-question: Why did you choose this project?

**Answer:**
I wanted to understand how a real application moves from source code to deployment, how cloud infrastructure is provisioned, how services communicate, and how the application is monitored and maintained.

---

## Q2. What is the difference between a traditional application and a microservices application?

**🔑 Keywords:** monolith vs microservices, independent deployment, isolated scaling, service boundary, architectural complexity

**Beginner-Friendly Answer:**
A traditional monolith is like an all-in-one printer/scanner/fax machine: if one part jams, the whole machine stops working. A microservices application is like having a separate printer, separate scanner, and separate computer: each does its own job and can be fixed, upgraded, or scaled up independently without affecting the others. However, microservices require extra networking and coordination to work together smoothly.

**Detailed Answer:**
A traditional monolithic application usually packages many features into one application. Microservices divide the application into smaller services that can be developed, deployed, and scaled independently.

### Cross-question: Are microservices always better than monolithic applications?

**Answer:**
No. Microservices introduce networking, deployment, monitoring, and data-consistency challenges. A monolith can be simpler and more suitable for a small application.

---

## Q3. What are the services in your application?

**🔑 Keywords:** React frontend, Python FastAPI (User), Go (Product), Java Spring Boot (Order), Node.js (Storage), PostgreSQL database, polyglot architecture

**Beginner-Friendly Answer:**
Our e-commerce store is divided into 5 distinct services:
1. **React frontend:** What the shopper interacts with in the browser.
2. **User Service (Python FastAPI):** Handles user registration and authentication.
3. **Product Service (Go):** Fast catalog search and product details.
4. **Order Service (Java Spring Boot):** Coordinates order creation and calculations.
5. **Storage Service (Node.js/Express):** Direct interface to the PostgreSQL database.
6. **PostgreSQL:** Relational database where all records are safely stored.

**Detailed Answer:**
The application contains:
- React frontend: Provides the user interface.
- Python FastAPI User Service: Handles user-related functionality.
- Go Product Service: Handles product-related functionality.
- Java Spring Boot Order Service: Handles order-related functionality.
- Node.js Storage Service: Provides storage-related APIs used by the business services.
- PostgreSQL: Stores relational application data.

### Cross-question: Why use different programming languages?

**Answer:**
Different services can use different languages when there is a technical reason or an existing team preference. However, this also increases operational complexity because the team must maintain different runtimes, dependencies, build processes, and debugging practices.

---

## Q4. Why do the business services communicate with the Storage Service?

**🔑 Keywords:** centralized data access, database abstraction, API decoupling, single point of failure (SPOF) trade-off

**Beginner-Friendly Answer:**
Instead of having Python, Go, and Java microservices all connect directly to PostgreSQL with their own database drivers, credentials, and connection pools, we routed all database operations through a single Storage Service. This keeps database queries organized in one central place. The trade-off is that if the Storage Service slows down, it can become a bottleneck for everyone else.

**Detailed Answer:**
The Storage Service centralizes the storage-related operations exposed to the other services. Instead of every business service independently implementing those operations, they can use the Storage Service's APIs.

### Cross-question: Is a single Storage Service a perfect microservices design?

**Answer:**
Not necessarily. It can become a bottleneck or a single point of failure. In some architectures, each service owns its own database or data boundary. The design depends on the application's consistency, scaling, and ownership requirements.

---

## Q5. What is the difference between CI and CD?

**🔑 Keywords:** Continuous Integration, Continuous Delivery, Continuous Deployment, build automation, automated testing, artifact publishing, deployment automation

**Beginner-Friendly Answer:**
**CI (Continuous Integration)** is about building, testing, and verifying code as soon as developers commit it, ensuring bugs are caught immediately. **CD (Continuous Delivery/Deployment)** takes those verified artifacts and safely releases them into your environments (like staging or production). CI prepares and tests the package; CD delivers and installs it.

**Detailed Answer:**
Continuous Integration (CI) automates activities such as code validation, testing, building, and security scanning. Continuous Delivery or Deployment (CD) takes the validated software towards release and deployment.

### Cross-question: Where does Jenkins fit in your project?

**Answer:**
Jenkins handles the CI pipeline: it obtains the source code, runs the required checks, builds container images, scans them, and publishes approved images to the configured registry. Argo CD manages Kubernetes deployment through GitOps.

---

## Q6. Why are Jenkins and Argo CD both required?

**🔑 Keywords:** separation of concerns, CI vs CD, image creation vs desired-state reconciliation, GitOps controller

**Beginner-Friendly Answer:**
Jenkins is great at doing computational build tasks: running compilers, executing test suites, scanning with Trivy, and pushing Docker images to a registry. Argo CD lives *inside* the Kubernetes cluster and continuously monitors Git, making sure the running cluster always matches the desired state declared in Git. Jenkins builds the ship; Argo CD pilots it.

**Detailed Answer:**
They have different responsibilities. Jenkins focuses on building and validating application artifacts. Argo CD focuses on keeping the Kubernetes environment aligned with the desired configuration stored in Git.

### Cross-question: Can Jenkins deploy directly to Kubernetes?

**Answer:**
Yes. Jenkins can deploy using tools such as kubectl or Helm. In this project, Argo CD provides the GitOps deployment model, which separates CI from the ongoing reconciliation of the cluster's desired state.

---

## Q7. What happens when a developer pushes new code?

**🔑 Keywords:** webhook trigger, automated pipeline, Trivy scan, GHCR publication, GitOps manifest update, Argo CD sync, pod rollout

**Beginner-Friendly Answer:**
1. A developer pushes code to GitHub.
2. A webhook triggers Jenkins, which runs tests, builds a Docker image, and scans it with Trivy.
3. If clean, Jenkins pushes the versioned image to GHCR.
4. The deployment repository is updated with the new image tag.
5. Argo CD detects the update in Git and reconciles the Kubernetes cluster with a rolling update.

**Detailed Answer:**
The CI pipeline is triggered, the code is validated and built, the image is scanned, and the approved image is published to the container registry. The deployment configuration is then updated according to the project's release process. Argo CD detects the desired-state change and reconciles the Kubernetes application.

### Cross-question: What happens if the build succeeds but deployment fails?

**Answer:**
CI success does not guarantee deployment success. I would check the image reference, Kubernetes events, pod status, configuration, secrets, probes, resource availability, service connectivity, and Argo CD sync status.

---

## Q8. What makes this a production-oriented project?

**🔑 Keywords:** Infrastructure as Code, declarative GitOps, security scanning, TLS encryption, autoscaling (HPA/Karpenter), centralized observability

**Beginner-Friendly Answer:**
It's not just running code on localhost. It implements real-world production engineering practices: infrastructure is automated using Terraform; deployments are versioned via GitOps; container images are scanned for vulnerabilities; traffic is encrypted over HTTPS with auto-renewing certificates; the cluster autoscales under load; and metrics and logs are centralized for debugging.

**Detailed Answer:**
It incorporates infrastructure as code, repeatable CI, container image scanning, declarative deployments, service routing, HTTPS, autoscaling, observability, and security controls. These practices improve consistency and make the application easier to operate.

### Cross-question: Does using these tools automatically make an application production-ready?

**Answer:**
No. Production readiness also requires tested recovery procedures, backups, access control, reliable alerting, capacity planning, cost control, secure secret management, and operational testing.
