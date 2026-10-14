# Part 1: Understand Your Project

> **🔑 Keywords to Remember:** Microservices, React, FastAPI, Go, Spring Boot, Node.js, PostgreSQL, Docker, Terraform, Jenkins, GHCR, Argo CD, EKS, Gateway API, HPA, Karpenter, Prometheus, Grafana, ELK

---

## Q1. Tell me about your project.

**🔑 Keywords:** microservices, e-commerce, containerized, DevOps architecture, CI/CD, GitOps, EKS, observability

**Beginner-Friendly Answer:**
Think of this project as an online shopping application that is broken into small, independent pieces (called microservices) instead of one big application. Each piece does one job — one handles users, another handles products, and another handles orders. We package each piece inside a container (using Docker) so it runs the same way everywhere. Then we use a bunch of DevOps tools to automatically build, test, deploy, and monitor everything. The goal is to make the whole system automated, scalable, and easy to manage.

**Detailed Answer:**
I am building a microservices-based e-commerce application and designing a production-oriented DevOps architecture around it.

The application has a React frontend and three business microservices: a User Service developed using Python FastAPI, a Product Service developed using Go, and an Order Service developed using Java Spring Boot.

We also have a separate Storage Service developed using Node.js and Express, which communicates with PostgreSQL and manages database operations.

The application is containerized using Docker. The DevOps architecture uses Terraform for infrastructure provisioning, Jenkins for continuous integration, GitHub Container Registry (GHCR) for storing container images, Argo CD for GitOps-based deployment, and Kubernetes on Amazon EKS as the target orchestration platform.

For external access, the architecture uses Kubernetes Gateway API with the AWS Load Balancer Controller to provision an Application Load Balancer. Route 53, ACM, and ExternalDNS support DNS and HTTPS configuration.

The broader design also includes HPA-based application scaling, Karpenter-based node provisioning, Prometheus and Grafana for monitoring, and the Elasticsearch, Beats, and Kibana ecosystem for log collection and analysis.

The goal is to build an automated, scalable, observable, and maintainable deployment platform.

> **⚠️ Important:** Explain that components you've tested locally are implemented, while components still being developed are part of your planned architecture.

---

## Cross-question 1: Why did you choose microservices instead of a monolithic application?

**🔑 Keywords:** independent deployment, separate scaling, different tech stacks, loose coupling, added complexity

**Beginner-Friendly Answer:**
Imagine a monolith as one giant box where everything is packed together — if you want to change one small thing, you have to rebuild and redeploy the entire box. With microservices, each function (users, products, orders) is a separate small box. You can update, deploy, or scale each box independently. However, this means more moving parts to manage.

**Detailed Answer:**
In a monolithic application, multiple business functions are packaged and deployed together. In microservices, each business function runs as a separate service.

For example, in my application, user management, product management, and order processing are separate services. We can update the Product Service without necessarily rebuilding and redeploying the User Service.

Microservices also let us choose different technologies for different services and scale individual services according to their workloads.

However, microservices introduce additional complexity, including network communication, service discovery, monitoring, and distributed failure handling.

### Follow-up: Is microservices always better than a monolith?

No. A monolith is often simpler for small applications. Microservices become useful when independent deployment, team ownership, scaling, or different technology requirements justify their additional complexity.

---

## Cross-question 2: Why did you use different programming languages?

**🔑 Keywords:** polyglot architecture, best tool for job, API communication, independent runtimes

**Beginner-Friendly Answer:**
We used different programming languages (React, Python, Go, Java, Node.js) to show that microservices can talk to each other through APIs regardless of the language they're built in. Each service has its own runtime and dependencies inside its own container. But remember — using different languages adds more maintenance work, so in real projects, you should have a clear reason for each choice.

**Detailed Answer:**
We used React for the frontend, Python for the User Service, Go for the Product Service, Java for the Order Service, and Node.js for the Storage Service.

This demonstrates how services implemented using different technology stacks can communicate through APIs.

Each service has its own runtime, dependencies, and container image.

However, using different languages is not automatically better. It introduces additional maintenance and operational complexity, so the choice should have a clear purpose.

---

## Cross-question 3: Explain the complete application architecture.

**🔑 Keywords:** React frontend, API communication, User/Product/Order services, Storage Service, PostgreSQL, central persistence, Kubernetes target

**Beginner-Friendly Answer:**
The user interacts with a React website (frontend). Behind the scenes, there are three services handling users, products, and orders. When these services need to save or fetch data, they talk to a Storage Service, which is the only service that directly communicates with the PostgreSQL database. This separation keeps things organized. In the cloud, all of this runs on Kubernetes, with infrastructure managed by Terraform and deployments handled through GitOps.

**Detailed Answer:**
The React frontend provides the user interface. It communicates with the User Service, Product Service, and Order Service through APIs.

The User Service handles user-related operations, and the Product Service handles product-related operations. The Order Service coordinates order creation by validating the user, retrieving the product, calculating the order total, and requesting order persistence.

The Storage Service provides a separate API for database operations. It communicates with PostgreSQL, which stores users, products, and orders.

Separating database access into a Storage Service gives the application a central place for persistence operations, although it also creates an additional service dependency and potential bottleneck.

In the target cloud architecture, the application containers will run on Kubernetes, with infrastructure managed by Terraform and application deployments managed through a GitOps workflow.

---

## Cross-question 4: What happens when a customer places an order?

**🔑 Keywords:** API request, validate user, retrieve product, calculate total, persist order, Storage Service, PostgreSQL

**Beginner-Friendly Answer:**
When a customer clicks "Place Order," the frontend sends a request to the Order Service. The Order Service then checks with the User Service ("Is this a valid user?") and the Product Service ("Does this product exist and what's the price?"). Once verified, it calculates the total (e.g., 2 items × ₹1,200 = ₹2,400), sends the order to the Storage Service, which saves it in the PostgreSQL database, and returns a confirmation.

**Detailed Answer:**
When a customer places an order, the frontend sends an API request to the Order Service.

The Order Service validates the user by calling the User Service and retrieves the product details from the Product Service. It calculates the total price using the product price and requested quantity.

Once validation succeeds, it sends the order data to the Storage Service, which saves the order in PostgreSQL.

The Order Service then returns the result to the frontend.

For example, if a customer orders two wireless mice at ₹1,200 each, the order total is ₹2,400.

In a production system, this workflow also needs safeguards against duplicate requests, inconsistent state, unavailable downstream services, and concurrent updates.

### Follow-up: What happens if the Storage Service goes down?

The Order Service may be unable to persist new orders. We should use appropriate timeouts, error handling, monitoring, and retries where safe.

We must not repeatedly retry a non-idempotent order-creation request without protection, because that could create duplicate orders.
