# Section 13: Gateway API, AWS Load Balancer Controller, and Application Routing

---

## Q123. What is Kubernetes Gateway API?

**🔑 Keywords:** next-gen Ingress, role-oriented API, `GatewayClass`, `Gateway`, `HTTPRoute`, multi-tenancy, traffic routing

**Beginner-Friendly Answer:**
The Kubernetes Gateway API is the modern successor to traditional Ingress. It splits the job of managing networking into distinct roles: cluster infrastructure operators define the entry points and ports (`GatewayClass` and `Gateway`), while application developers define URL path rules (`HTTPRoute`). It is much more powerful and expressive than old Ingress resources.

**Detailed Answer:**
Gateway API is a Kubernetes API for expressing network traffic routing. It separates infrastructure-level gateway configuration from application-level route configuration more clearly than many traditional Ingress-only setups.

---

## Q124. What is a GatewayClass?

**🔑 Keywords:** cluster-scoped CRD, controller mapping, AWS Load Balancer Controller, infrastructure definition

**Beginner-Friendly Answer:**
A `GatewayClass` defines what type of load balancer will implement your gateways. It links the Gateway API to a specific controller running in the cluster — in our AWS architecture, it tells Kubernetes that gateways belonging to this class are managed by the **AWS Load Balancer Controller**.

**Detailed Answer:**
A GatewayClass identifies a class of gateways implemented by a particular controller. It connects the Gateway API configuration to the controller responsible for implementing it.

---

## Q125. What is a Gateway?

**🔑 Keywords:** traffic entry point, listeners, port 80/443, TLS termination, ALB mapping, IP allocation

**Beginner-Friendly Answer:**
A `Gateway` resource defines where traffic enters your cluster. It describes the listeners (e.g. port 80 for HTTP, port 443 for HTTPS), hostnames, and TLS certificates. The AWS Load Balancer Controller reads this `Gateway` and automatically provisions an AWS Application Load Balancer in your VPC.

**Detailed Answer:**
A Gateway describes a network entry point and its listeners, including relevant protocol, port, hostname, and TLS configuration.

---

## Q126. What is an HTTPRoute?

**🔑 Keywords:** HTTP routing rules, path matching (`/orders`, `/products`), header matching, `backendRefs`, Service endpoints

**Beginner-Friendly Answer:**
An `HTTPRoute` defines the routing rules for web requests: "If the URL path starts with `/products`, forward traffic to the Product Service. If it starts with `/orders`, forward traffic to the Order Service." The HTTPRoute attaches to the Gateway to direct incoming requests to the right backend microservice.

**Detailed Answer:**
An HTTPRoute defines how HTTP requests should be matched and routed, using supported conditions such as hostnames, paths, headers, and backend references.

---

## Q127. What is the relationship between Gateway and HTTPRoute?

**🔑 Keywords:** parent-child attachment, entry point vs routing rules, role separation (DevOps vs Developer)

**Beginner-Friendly Answer:**
Think of the `Gateway` as the front door of a shopping mall (defining the door, the address, and the security guard), and the `HTTPRoute` as the hallway directory inside the mall telling shoppers: "Clothing stores are on floor 1, Food court is on floor 2." The `HTTPRoute` must attach to the `Gateway` to direct incoming visitors to the right shop (Service).

**Detailed Answer:**
The Gateway defines the entry point and listeners. An HTTPRoute defines request-matching and backend-routing rules and must be attached according to the Gateway's route-attachment policies.

---

## Q128. What is the AWS Load Balancer Controller?

**🔑 Keywords:** Kubernetes operator, AWS ALB provisioning, TargetGroupBinding, IAM roles for service accounts (IRSA)

**Beginner-Friendly Answer:**
The AWS Load Balancer Controller is an operator running inside your EKS cluster that bridges Kubernetes with AWS networking. When you create Gateway API or Ingress resources, this controller automatically calls AWS APIs to create the Application Load Balancer (ALB), configure target groups, register Pod IPs, and set up listener rules.

**Detailed Answer:**
It is a Kubernetes controller that manages supported AWS load-balancing resources based on configured Kubernetes resources and controller capabilities.

### Cross-question: Does creating a Gateway manifest guarantee that an ALB will be provisioned?

**Answer:**
No. The controller must support the requested configuration, have the necessary permissions, and be correctly installed. The GatewayClass, Gateway, route attachment, subnet discovery, and other AWS prerequisites must also be correct.

---

## Q129. What is a TargetGroupConfiguration?

**🔑 Keywords:** custom resource (CRD), ALB target group attributes, health check configuration, deregistration delay

**Beginner-Friendly Answer:**
In the AWS Load Balancer Controller, a `TargetGroupConfiguration` (or `TargetGroupBinding`) is a Custom Resource used to fine-tune how the AWS ALB connects to Kubernetes Pods — such as configuring health check intervals, timeout durations, and deregistration delays.

**Detailed Answer:**
In an AWS Load Balancer Controller setup that supports this custom resource, it is used to configure supported target-group properties for the relevant load-balancing integration. The exact behavior depends on the installed controller version and its supported API.

---

## Q130. What is the difference between an Ingress resource and a Gateway API resource?

**🔑 Keywords:** Ingress (monolithic, vendor-specific annotations) vs Gateway API (modular, role-oriented, vendor-neutral standard)

**Beginner-Friendly Answer:**
The traditional `Ingress` resource tried to shove everything into one single file, forcing teams to rely on messy, non-standard vendor annotations (like `alb.ingress.kubernetes.io/...`). `Gateway API` is modular and role-oriented: it cleanly separates cluster administration from application routing with first-class, standard Kubernetes specifications.

**Detailed Answer:**
Ingress is an established Kubernetes API for HTTP and HTTPS routing, while Gateway API provides a more expressive, role-oriented set of resources for traffic management. Support for individual features depends on the controller implementing the API.

---

## Q131. What is a target group?

**🔑 Keywords:** backend targets, IP mode vs instance mode, health check endpoints, HTTP status codes

**Beginner-Friendly Answer:**
A target group tells the AWS Application Load Balancer *where* to forward incoming traffic. In EKS, the target group typically registers the direct IP addresses of your running Pods. The ALB continuously pings their health check endpoints; if a Pod fails health checks, the ALB immediately stops routing traffic to that target.

**Detailed Answer:**
An ALB target group defines the backend targets that receive traffic and their health-check configuration. The targets must be registered and healthy for the load balancer to send normal traffic to them.

---

## Q132. Why can an ALB exist while the application remains inaccessible?

**🔑 Keywords:** unhealthy targets, security group blocking, missing route table, DNS misconfiguration, failing readiness probes

**Beginner-Friendly Answer:**
Just because the AWS ALB hardware exists in AWS doesn't mean your app is accessible! Common causes:
1. All targets in the target group are marked `Unhealthy` (failing health checks).
2. The Security Group on the ALB doesn't allow inbound traffic on port 80/443.
3. The worker nodes' security group blocks traffic coming from the ALB.
4. Route 53 DNS records point to the wrong address or haven't propagated.

**Detailed Answer:**
The ALB may have unhealthy targets, incorrect listener rules, a wrong target port, invalid route configuration, or a backend service with no endpoints. DNS, TLS, security groups, subnet routing, and application health may also be responsible.

---

## Q133. What is TLS termination?

**🔑 Keywords:** SSL/TLS offloading, decryption at the load balancer, certificate management, end-to-end vs edge encryption

**Beginner-Friendly Answer:**
TLS termination means the load balancer decrypts incoming HTTPS traffic from the user on port 443. Once decrypted at the ALB, traffic can be sent to internal microservices over plain HTTP inside the secure private VPC network. This frees backend microservices from the heavy CPU work of handling SSL certificates and decryption.

**Detailed Answer:**
TLS termination is where an encrypted connection is decrypted, commonly at a load balancer. Depending on the design, traffic from the load balancer to the backend may use HTTP or another TLS connection.

### Cross-question: Does TLS termination at the ALB mean traffic is encrypted all the way to the Pod?

**Answer:**
Not necessarily. Encryption between the ALB and backend depends on the configured target protocol and the rest of the network design.
