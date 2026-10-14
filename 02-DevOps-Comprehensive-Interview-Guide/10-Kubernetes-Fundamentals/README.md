# Section 10: Kubernetes Fundamentals

---

## Q90. What is Kubernetes?

**🔑 Keywords:** container orchestration, automated scheduling, self-healing, horizontal scaling, declarative management, service discovery

**Beginner-Friendly Answer:**
Kubernetes (often called K8s) is the leading platform for managing containerized applications at scale. Instead of you manually starting containers, restarting them when they crash, or balancing web traffic, Kubernetes does all of it automatically. You simply tell Kubernetes: "Keep 3 copies of my Product Service running," and it ensures that desired state is maintained 24/7.

**Detailed Answer:**
Kubernetes is a container-orchestration platform that manages the deployment, scheduling, scaling, and recovery of containerized workloads.

---

## Q91. What is a Kubernetes cluster?

**🔑 Keywords:** Control Plane (API server, etcd, scheduler, controller manager), Worker Nodes (kubelet, kube-proxy, container runtime)

**Beginner-Friendly Answer:**
A Kubernetes cluster is a group of connected servers working together as one giant computer. It is split into two halves:
1. **Control Plane (The Brain):** Decides what runs where, stores cluster state in `etcd`, and handles API requests.
2. **Worker Nodes (The Muscle):** The physical or virtual servers (EC2 instances) that actually run your application containers.

**Detailed Answer:**
A cluster consists of a control plane and worker compute resources. The control plane manages cluster state and scheduling, while worker nodes run workloads.

---

## Q92. What is a Pod?

**🔑 Keywords:** atomic deployable unit, shared network namespace, localhost loopback, shared volumes, single/multi-container pod

**Beginner-Friendly Answer:**
A Pod is the smallest deployable object in Kubernetes. You never run a container directly on a node; you wrap it in a Pod. Containers inside the same Pod share the exact same IP address and storage volumes and can talk to each other via `localhost`. In 95% of cases, you have one container per Pod.

**Detailed Answer:**
A Pod is the smallest deployable unit in Kubernetes. It contains one or more containers that share certain resources, such as network identity and configured volumes.

### Cross-question: Why do we usually deploy a Deployment instead of a standalone Pod?

**Answer:**
A Deployment manages ReplicaSets and helps maintain the desired number of interchangeable Pods. It supports rollout and rollback workflows.

---

## Q93. What is a Deployment?

**🔑 Keywords:** declarative updates, ReplicaSets, rolling updates, rollbacks, self-healing, stateless applications

**Beginner-Friendly Answer:**
If a Pod is an individual worker, a **Deployment** is the manager. You declare: "I want 4 replicas of the Order Service." The Deployment automatically creates a ReplicaSet to launch those 4 Pods. If a node crashes and a Pod dies, the Deployment notices and launches a replacement. When you push a new software version, the Deployment replaces old Pods with new ones one-by-one with zero downtime.

**Detailed Answer:**
A Deployment manages a set of replicated Pods through ReplicaSets and supports declarative updates to application workloads.

---

## Q94. What is a ReplicaSet?

**🔑 Keywords:** pod count maintenance, label selector, scaling controller, immutable pod templates

**Beginner-Friendly Answer:**
A ReplicaSet is a lower-level controller whose only job is to guarantee that a specific number of identical Pods (matching a specific label selector) are running at any given moment. Deployments create and manage ReplicaSets under the hood, so you rarely need to touch ReplicaSets directly.

**Detailed Answer:**
A ReplicaSet ensures that a specified number of matching Pods are running. Deployments usually manage ReplicaSets to support application updates.

---

## Q95. What is a Kubernetes Service?

**🔑 Keywords:** stable virtual IP (ClusterIP), internal load balancer, label selector, CoreDNS service discovery

**Beginner-Friendly Answer:**
Pods are born and die constantly, and every time a Pod restarts, it gets a new random internal IP address. A **Service** provides a stable, permanent IP address and DNS name (like `http://user-service:8001`) that stays the same forever. When other microservices send requests to that name, the Service distributes the traffic across all currently healthy Pods.

**Detailed Answer:**
A Service provides a stable network endpoint for a set of Pods selected by labels. It allows clients to reach workloads without depending on individual Pod IP addresses.

---

## Q96. Explain ClusterIP, NodePort, and LoadBalancer Services.

**🔑 Keywords:** ClusterIP (internal default), NodePort (opens static port on every node: 30000-32767), LoadBalancer (cloud provider load balancer integration)

**Beginner-Friendly Answer:**
- **`ClusterIP` (Default):** Accessible *only* from inside the cluster. Perfect for internal databases and microservices that shouldn't be exposed to the internet.
- **`NodePort`:** Opens a high port (between 30000-32767) on the external IP of *every* cluster node.
- **`LoadBalancer`:** Asks your cloud provider (like AWS) to provision an external load balancer (like an ALB or NLB) that forwards traffic directly into your Service.

**Detailed Answer:**
- ClusterIP exposes a Service internally within the cluster by default.
- NodePort exposes a port on cluster nodes.
- LoadBalancer requests or integrates with an external load-balancing implementation supported by the environment.

### Cross-question: Is a NodePort automatically reachable from the public internet?

**Answer:**
No. External routing, node reachability, firewall rules, and the cluster's networking setup determine whether it is accessible.

---

## Q97. What is a ConfigMap?

**🔑 Keywords:** configuration decoupling, key-value data, environment variables, config volume mounts, twelve-factor app

**Beginner-Friendly Answer:**
A ConfigMap lets you decouple non-confidential configuration settings (like server port, API URLs, log level) from your container images. You can update a ConfigMap in Kubernetes without rebuilding your Docker image or modifying source code.

**Detailed Answer:**
A ConfigMap stores non-confidential configuration that can be supplied to Pods through environment variables, command arguments, or mounted files.

---

## Q98. What is a Secret?

**🔑 Keywords:** sensitive credentials, base64 encoding, encryption at rest (KMS), RBAC permissions, external secret stores (Vault / AWS SM)

**Beginner-Friendly Answer:**
A Kubernetes Secret is used to store sensitive data like passwords, API keys, and TLS certificates. They are mounted into containers as files or environment variables. Remember: by default, Kubernetes Secrets are merely base64-encoded, not encrypted! For real production security, you must enable KMS encryption at rest in etcd or use external secret tools like AWS Secrets Manager.

**Detailed Answer:**
A Kubernetes Secret stores data intended to be confidential, such as credentials or tokens. A Secret is not automatically secure simply because it uses that resource type; encryption at rest, RBAC, external secret management, and careful handling remain important.

---

## Q99. What are readiness, liveness, and startup probes?

**🔑 Keywords:** health checks, readiness (service endpoint routing), liveness (container restart), startup (slow initialization grace period)

**Beginner-Friendly Answer:**
- **Readiness Probe:** Checks: "Are you ready to receive traffic?" If it fails, Kubernetes stops sending user requests to this Pod until it recovers.
- **Liveness Probe:** Checks: "Are you still alive or deadlocked?" If it fails, Kubernetes kills the container and restarts it.
- **Startup Probe:** Gives slow-starting applications (like Java Spring Boot) time to initialize before readiness and liveness checks begin firing.

**Detailed Answer:**
- A readiness probe determines whether a container should receive normal traffic.
- A liveness probe helps Kubernetes determine whether a container should be restarted.
- A startup probe allows an application time to start before the other probes' normal checks take effect.

### Cross-question: What can happen if a liveness probe is too aggressive?

**Answer:**
A slow but healthy application may repeatedly restart. Probe timing and failure thresholds must reflect actual startup and response behavior.

---

## Q100. What are requests and limits?

**🔑 Keywords:** resource management, CPU requests (guaranteed / scheduling), CPU limits (throttling), memory limits (OOMKilled)

**Beginner-Friendly Answer:**
- **Requests:** The guaranteed minimum CPU and RAM a container needs to run. The Kubernetes scheduler uses requests to find a node with enough space.
- **Limits:** The hard maximum ceiling a container is allowed to consume. If a container uses more CPU than its limit, it gets throttled (slowed down). If it exceeds its memory limit, it gets killed immediately by the Linux kernel with an **OOMKilled** (Out Of Memory) error.

**Detailed Answer:**
Resource requests inform Kubernetes scheduling about the resources a container needs. Limits constrain resource consumption according to the resource and runtime behavior.

### Cross-question: What happens when a container exceeds its memory limit?

**Answer:**
It may be terminated with an out-of-memory event. Excessive CPU use against a CPU limit generally causes throttling rather than the same type of termination.

---

## Q101. What is a namespace?

**🔑 Keywords:** virtual cluster, logical isolation, resource quotas, naming scope, RBAC boundary

**Beginner-Friendly Answer:**
A namespace is a virtual subdivision inside a Kubernetes cluster. It lets you organize workloads (e.g. `ecommerce-dev`, `ecommerce-prod`, or `monitoring`). You can have a service called `backend` in the `dev` namespace and another service called `backend` in `prod` without any naming collision.

**Detailed Answer:**
A namespace groups Kubernetes resources and provides a way to organize and scope names. It can help separate teams, environments, or application components, but it is not by itself a complete security boundary.

---

## Q102. What is a label and a selector?

**🔑 Keywords:** key-value metadata, loose coupling, Service-to-Pod mapping, controller selection

**Beginner-Friendly Answer:**
Labels are key-value tags attached to objects (like `app: order-service`, `env: production`). Selectors are filters used by Services and Deployments to find those Pods (e.g., "Route traffic to any Pod with label `app: order-service`"). This loose coupling means Services automatically connect to new Pods as soon as they are launched.

**Detailed Answer:**
A label is key-value metadata attached to a Kubernetes object. A selector identifies objects matching specified labels and is commonly used by Services and controllers.

### Cross-question: What if the Service selector doesn't match the Pod labels?

**Answer:**
The Service may have no matching endpoints, so traffic will not reach the intended Pods. I would inspect the labels, selector, and endpoint information.

---

## Q103. What is a rolling update?

**🔑 Keywords:** zero downtime, incremental pod replacement, `maxSurge`, `maxUnavailable`, readiness check dependency

**Beginner-Friendly Answer:**
A rolling update replaces old version Pods with new version Pods incrementally so there is zero downtime. For example, if you have 4 Pods, Kubernetes launches 1 new Pod, waits for its readiness probe to pass, starts sending traffic to it, and only then terminates 1 old Pod. This repeats until all 4 Pods are running the new version.

**Detailed Answer:**
A rolling update replaces existing application Pods progressively with new ones. Proper readiness checks and rollout settings help maintain availability during the update.

---

## Q104. How do you troubleshoot a Pod in CrashLoopBackOff?

**🔑 Keywords:** `CrashLoopBackOff`, `kubectl describe pod`, `kubectl logs --previous`, exit codes (137 OOM, 1 error), configuration errors

**Beginner-Friendly Answer:**
`CrashLoopBackOff` means your container started, crashed, and Kubernetes is waiting exponentially longer before restarting it again. My troubleshooting steps:
1. Run `kubectl logs <pod-name> -n <namespace> --previous` to see why the dead container crashed right before dying.
2. Run `kubectl describe pod <pod-name>` and look at the exit code: Exit Code 137 means it ran out of memory (OOMKilled); Exit Code 1 means application code threw an unhandled exception or missing environment variable.

**Detailed Answer:**
I would inspect the Pod description and events, read current and previous container logs, verify the command and arguments, check configuration and secrets, inspect probes, and review resource limits.

Commands:

```bash
kubectl describe pod <pod-name> -n <namespace>
kubectl logs <pod-name> -n <namespace>
kubectl logs <pod-name> -n <namespace> --previous
```

### Cross-question: Why use --previous?

**Answer:**
It retrieves logs from the previous terminated container instance when those logs are available, which can help explain repeated crashes.

---

## Q105. What is the difference between ImagePullBackOff and CrashLoopBackOff?

**🔑 Keywords:** `ImagePullBackOff` (image download failure), `CrashLoopBackOff` (container runtime crash), troubleshooting distinction

**Beginner-Friendly Answer:**
- **`ImagePullBackOff`:** The container never even started! Kubernetes tried to download the image from Docker Hub or GHCR and failed (due to a typo in the tag, missing credentials, or network failure).
- **`CrashLoopBackOff`:** Kubernetes successfully downloaded the image and started the container, but the application code crashed immediately after starting up.

**Detailed Answer:**
ImagePullBackOff means Kubernetes is backing off while retrying an image pull that has failed. CrashLoopBackOff means a container has repeatedly terminated and Kubernetes is delaying subsequent restart attempts.
