# Part 10: Kubernetes Gateway API, ALB, Route 53, and ACM

> This is a particularly important section because it connects application access with AWS networking.

---

## Q44. What is the Kubernetes Gateway API?

**🔑 Keywords:** Gateway API, GatewayClass, Gateway, HTTPRoute, role-oriented, traffic routing, modern Ingress replacement

**Beginner-Friendly Answer:**
The Kubernetes Gateway API is the modern evolution of Kubernetes Ingress. Old Ingress resources tried to do everything in one single file, which got messy. Gateway API splits the responsibility into three clean layers: the Cluster Admin chooses the controller (GatewayClass), the Infrastructure/DevOps team sets up where traffic enters and opens ports like 80/443 (Gateway), and application developers define their URL routing rules (HTTPRoute). In our architecture, it exposes our services to external web traffic.

**Detailed Answer:**
The Kubernetes Gateway API is a set of Kubernetes resources for configuring traffic routing.

It separates responsibilities across resources such as GatewayClass, Gateway, and HTTPRoute.

In our architecture, it provides a structured way to define external HTTP and HTTPS routing to application services.

### Cross-question: Why use Gateway API instead of Ingress?

Ingress is an established API for HTTP and HTTPS routing, but Gateway API provides a more expressive model with separate resources for infrastructure provisioning and route configuration.

Gateway API also supports richer traffic-management capabilities.

The correct choice depends on controller support, requirements, and operational complexity.

---

## Q45. What is a GatewayClass?

**🔑 Keywords:** controller specification, cluster-scoped resource, infrastructure provider, AWS Load Balancer Controller

**Beginner-Friendly Answer:**
A GatewayClass is like choosing the brand or engine that will build your load balancers. It tells Kubernetes: "When someone asks for a Gateway of this class, hand the job over to the AWS Load Balancer Controller." It defines which controller is responsible for translating the Gateway settings into actual cloud hardware or software.

**Detailed Answer:**
A GatewayClass defines a class of Gateways managed by a particular controller implementation.

It tells Kubernetes which controller is responsible for handling Gateways of that class.

In our AWS architecture, the AWS Load Balancer Controller is the controller intended to implement the relevant Gateway API functionality.

The exact supported API versions and features must match the controller version installed in the cluster.

### Cross-question: What happens if the GatewayClass is missing or unsupported?

The Gateway may not be reconciled by a suitable controller.

The Gateway's status and controller logs should be checked to determine whether the class exists, is accepted, and is supported by the installed controller.

---

## Q46. What is a Gateway?

**🔑 Keywords:** entry point, listeners, port 443, port 80, TLS termination, ALB provisioning, load balancer mapping

**Beginner-Friendly Answer:**
A Gateway describes a point where traffic enters the cluster from the outside world. It specifies the ports to listen on (e.g., port 80 for HTTP and port 443 for HTTPS) and any certificates needed for SSL/TLS. When you create this Gateway in Kubernetes, the AWS Load Balancer Controller sees it and automatically provisions a real AWS Application Load Balancer (ALB) in your VPC to match.

**Detailed Answer:**
A Gateway represents a point where traffic enters the infrastructure and defines listeners and other traffic-handling configuration.

For example, a Gateway might define an HTTPS listener on port 443.

The controller implementing the Gateway API can translate that configuration into infrastructure such as an AWS Application Load Balancer, depending on its capabilities and configuration.

### Cross-question: Does creating a Gateway automatically create an ALB?

Not universally. That depends on the Gateway controller.

In our architecture, the intention is for the AWS Load Balancer Controller to reconcile the supported Gateway resources and provision or configure the corresponding AWS load-balancing infrastructure.

We must verify that behavior against the installed controller version and its supported configuration.

---

## Q47. What is an HTTPRoute?

**🔑 Keywords:** HTTP routing, path-based routing, host-based routing, backendRef, Kubernetes Service target

**Beginner-Friendly Answer:**
An HTTPRoute defines the traffic rules: "If a user visits `/products`, send them to the Product Service. If they visit `/orders`, route them to the Order Service. Otherwise, send them to the React frontend." The HTTPRoute attaches to the Gateway, so any incoming request reaching the load balancer gets forwarded to the correct backend Service inside Kubernetes.

**Detailed Answer:**
An HTTPRoute defines HTTP routing rules.

For example, it can route requests matching a hostname or URL path to a Kubernetes Service.

In our application, the routing rules can direct requests to the frontend or appropriate application entry points, depending on how we expose the services.

### Cross-question: What is the relationship between Gateway and HTTPRoute?

The Gateway defines the traffic-entry point and listeners. HTTPRoute defines the HTTP routing rules.

An HTTPRoute must be attached to an appropriate Gateway and reference valid backend services for the routing configuration to work.

---

## Q48. What is the AWS Load Balancer Controller?

**🔑 Keywords:** Kubernetes operator, ALB provisioning, TargetGroupBinding, IAM roles for service accounts (IRSA), reconciliation

**Beginner-Friendly Answer:**
The AWS Load Balancer Controller is an in-cluster agent that bridges Kubernetes with AWS networking. When you declare a Gateway or Ingress in Kubernetes, this controller makes API calls to AWS to create the Application Load Balancer, configure target groups, register Pod IPs, and set up listeners. You don't have to manually touch the AWS console to manage load balancers.

**Detailed Answer:**
The AWS Load Balancer Controller is a Kubernetes controller that manages supported AWS load-balancing resources based on Kubernetes configuration.

In our architecture, it is intended to provision and configure an ALB from supported Gateway API resources.

This reduces the need to manually create and maintain every load-balancing rule.

### Cross-question: What if the ALB is not created?

Check:
1. Whether the controller Pods are healthy.
2. Whether the GatewayClass is supported and accepted.
3. Whether the Gateway and HTTPRoute statuses show errors.
4. Whether the controller has the required AWS permissions.
5. Whether the configured subnets and networking are valid.
6. Whether the controller logs show reconciliation errors.

> **💡 Tip:** This is a strong practical interview answer because it demonstrates systematic troubleshooting rather than guessing.

---

## Q49. What is Route 53?

**🔑 Keywords:** AWS DNS, domain registrar, hosted zones, A/CNAME records, ExternalDNS controller, automated record creation

**Beginner-Friendly Answer:**
Route 53 is Amazon's scalable Domain Name System (DNS) service. It connects user-friendly domain names (like `shop.mybrand.com`) to AWS resources like our Application Load Balancer. In our setup, ExternalDNS automatically talks to Route 53 whenever our services or routes are created, creating and updating DNS records without us having to do it by hand.

**Detailed Answer:**
Amazon Route 53 is an AWS DNS service.

In our architecture, it manages the domain records used to direct users to the application entry point.

For example, a record can direct the application domain to the ALB.

### Cross-question: What is ExternalDNS?

ExternalDNS is a controller that can create and update DNS records in supported DNS providers based on configured Kubernetes resources.

In our project, ExternalDNS is intended to automate relevant Route 53 records rather than requiring every record to be created manually.

### Cross-question: Is ExternalDNS a load balancer?

No. ExternalDNS manages DNS records. The ALB handles incoming application traffic.

---

## Q50. What is ACM?

**🔑 Keywords:** AWS Certificate Manager, SSL/TLS certificates, HTTPS termination, public certificates, automatic renewal, ALB listener

**Beginner-Friendly Answer:**
ACM (AWS Certificate Manager) is the service that gives you free, auto-renewing SSL/TLS certificates for your custom domain names. It enables HTTPS (the green padlock in your browser) so traffic between shoppers and your store is encrypted. In our project, ACM provides the certificate that gets attached to our ALB listener on port 443.

**Detailed Answer:**
AWS Certificate Manager, or ACM, manages supported TLS certificates for AWS services.

In our architecture, ACM provides the certificate intended for HTTPS termination at the ALB.

This allows the load balancer to establish HTTPS connections with clients.

### Cross-question: Does ACM encrypt traffic by itself?

No. ACM manages certificates, but the relevant AWS service must be configured to use the certificate for TLS.

For our architecture, the ALB listener must be configured to use the certificate.

### Cross-question: What is the difference between HTTP and HTTPS?

HTTP transfers application messages without TLS encryption.

HTTPS uses TLS to protect communication, authenticate the server through its certificate, and provide integrity protection.
