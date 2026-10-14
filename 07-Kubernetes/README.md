# Part 7: Kubernetes

> This is one of the most important sections because your application is designed to run on Kubernetes.

---

## Q31. What is Kubernetes?

**🔑 Keywords:** container orchestration, scheduling, service discovery, self-healing, scaling, desired state, cluster management

**Beginner-Friendly Answer:**
If Docker gives you containers, Kubernetes is the manager running the entire shipping port. Running one or two containers manually is easy, but running dozens across multiple servers is hard. Kubernetes takes care of starting containers, making sure they stay healthy, restarting them if they crash, scaling them up when traffic spikes, and balancing network traffic between them. In our project, Kubernetes on Amazon EKS is the platform hosting our e-commerce microservices.

**Detailed Answer:**
Kubernetes is a container orchestration platform.

It manages containerized workloads and provides features such as scheduling, service discovery, health checks, scaling, and workload recovery.

In our project, Kubernetes is the target platform for deploying the application services and supporting components.

### Cross-question: Why do we need Kubernetes if Docker already runs containers?

Docker can run containers, but Kubernetes provides cluster-level orchestration.

It manages desired replica counts, schedules workloads across nodes, provides stable service endpoints, and can replace failed containers or Pods according to workload configuration.

Docker and Kubernetes solve related but different problems.

---

## Q32. What is a Pod?

**🔑 Keywords:** smallest deployable unit, shared network namespace, localhost communication, shared storage, co-located containers

**Beginner-Friendly Answer:**
A Pod is the smallest building block in Kubernetes. You don't deploy containers directly onto servers; you deploy Pods, and the Pod wraps around one (or more) containers. Containers inside the same Pod share the same network IP and storage, meaning they can talk to each other over `localhost` just like two programs running on the same computer. In our e-commerce app, each microservice runs inside its own Pod.

**Detailed Answer:**
A Pod is the smallest deployable unit in Kubernetes.

It contains one or more containers that share networking and can share storage volumes.

In our application, a typical microservice Pod contains the main application container.

Kubernetes schedules Pods onto nodes.

### Cross-question: Why not deploy containers directly instead of using Pods?

Kubernetes manages containers through Pods because a Pod represents the unit of scheduling, networking, and lifecycle management.

Even if an application uses a single container, Kubernetes runs that container inside a Pod.

---

## Q33. What is a Deployment?

**🔑 Keywords:** replica management, declarative updates, rolling updates, self-healing, stateless applications, ReplicaSet controller

**Beginner-Friendly Answer:**
A Deployment is like a supervisor for your Pods. Instead of creating individual Pods manually, you tell a Deployment: "I want 3 copies of the Order Service running at all times." If one crashes, the Deployment notices and starts a new one. When you update your app to a new version, the Deployment does a rolling update — replacing old Pods with new ones one by one without any downtime.

**Detailed Answer:**
A Deployment manages a desired number of replicas of a stateless application and supports controlled updates.

For example, a Deployment can specify that the Order Service should have three replicas.

Kubernetes attempts to maintain that desired state.

### Cross-question: What happens if one Pod crashes?

If a Deployment-managed Pod fails or disappears, Kubernetes works to maintain the desired number of replicas.

The exact recovery behavior depends on the failure and the controllers involved.

---

## Q34. What is a Kubernetes Service?

**🔑 Keywords:** stable endpoint, internal load balancer, Pod IP ephemeral, ClusterIP, kube-dns, CoreDNS, selector/labels

**Beginner-Friendly Answer:**
Pods are temporary — they get created, destroyed, and given new random IP addresses all the time. If the Order Service needs to talk to the Product Service, it can't rely on a Pod's IP because that IP might change 5 minutes later. A Service acts as a permanent, stable phone number (and internal load balancer) sitting in front of your Pods. Other services simply call `http://product-service:8002`, and the Service routes the traffic to whichever Pod is healthy.

**Detailed Answer:**
A Service provides a stable network endpoint for a group of Pods.

Pod IP addresses can change when Pods are recreated. Applications should not normally depend on fixed Pod IPs.

A Service provides a stable way to discover and reach the selected Pods.

### Cross-question: How does the Order Service find the Product Service?

Within our Kubernetes namespace, the Order Service can use the Kubernetes Service DNS name:

```
http://product-service:8002
```

The Kubernetes DNS system resolves the Service name, and the Service routes traffic to appropriate backend Pods.

The actual application port and Service port must match the configured networking.

---

## Q35. What is a ConfigMap?

**🔑 Keywords:** non-sensitive configuration, environment variables, config files, decoupling code from config, key-value store

**Beginner-Friendly Answer:**
A ConfigMap is where you store non-secret settings outside of your application code, such as database hostnames, port numbers, or feature flags. This lets you change application settings without having to recompile or rebuild your Docker image. For example, our Storage Service gets its database connection details (`DB_HOST=postgres`, `DB_PORT=5432`) from a ConfigMap.

**Detailed Answer:**
A ConfigMap stores non-sensitive configuration for Kubernetes workloads.

For example, our Storage Service uses a Kubernetes ConfigMap to configure values such as:

```
DB_HOST=postgres
DB_PORT=5432
DB_NAME=ecommerce_db
```

The Pod receives these values through its environment or mounted files.

### Cross-question: Should we store database passwords in a ConfigMap?

No. ConfigMaps are intended for non-sensitive configuration. Kubernetes Secrets are the appropriate resource for sensitive values, although Secrets still require appropriate access controls and encryption configuration.

---

## Q36. What is a Kubernetes Secret?

**🔑 Keywords:** sensitive data, base64 encoding, passwords, API tokens, encryption at rest, RBAC control, external secret stores

**Beginner-Friendly Answer:**
A Secret is designed specifically for sensitive information like passwords, API keys, and TLS certificates. Unlike ConfigMaps, Secrets are handled with tighter access controls. In our project, the PostgreSQL database password is provided to the services via a Kubernetes Secret. Keep in mind: standard Kubernetes Secrets are base64-encoded (not truly encrypted by default), so real production setups enable encryption at rest or integrate tools like AWS Secrets Manager or HashiCorp Vault.

**Detailed Answer:**
A Secret is a Kubernetes resource used to store sensitive configuration, such as database credentials.

In our local application, the PostgreSQL credentials are supplied through a Kubernetes Secret.

Production environments should also consider encryption at rest, least-privilege RBAC, and external secret-management systems where appropriate.

### Cross-question: Are Kubernetes Secrets automatically encrypted?

Their values are base64-encoded in their standard serialized representation, but base64 is not encryption.

The security of Secrets depends on cluster configuration, access controls, encryption at rest, and how they are delivered to applications.

---

## Q37. What is a PersistentVolumeClaim?

**🔑 Keywords:** PVC, PV, storage abstraction, data persistence, stateful workloads, database volume, reclaim policy

**Beginner-Friendly Answer:**
Normally, when a container dies, all files inside it disappear. That's fine for stateless APIs, but a disaster for a database! A PersistentVolumeClaim (PVC) is like a request ticket saying: "Hey Kubernetes, give my PostgreSQL container 10GB of persistent storage that survives even if the container restarts." When the PostgreSQL Pod restarts, it reconnects to the same volume, keeping all user and order records intact.

**Detailed Answer:**
A PersistentVolumeClaim, or PVC, is a request for persistent storage by a Kubernetes workload.

Our PostgreSQL deployment uses a PVC so database files can live on persistent storage rather than only inside the container's writable layer.

### Cross-question: What happens if the PostgreSQL Pod is deleted?

If the PVC and its underlying storage remain available, the replacement Pod can mount that storage and recover its data.

The result depends on the volume's lifecycle, storage driver, access modes, and reclaim policy.

A PVC alone does not guarantee backups or disaster recovery.

---

## Q38. What is a readiness probe?

**🔑 Keywords:** traffic routing, healthy endpoint, liveness probe (restart), startup probe (initialization), failure threshold

**Beginner-Friendly Answer:**
A readiness probe asks the container: "Are you ready to handle user requests right now?" If an application is still warming up, loading caches, or connecting to the database, the readiness probe fails, and Kubernetes stops sending web traffic to that Pod until it passes. This prevents users from getting 502/503 errors during deployments or cold starts.

**Detailed Answer:**
A readiness probe checks whether a container is ready to receive traffic.

If a Pod fails readiness checks, Kubernetes can stop including it as a ready backend for its Service.

### Cross-question: What is a liveness probe?

A liveness probe checks whether the application should be restarted.

A failing liveness probe can cause Kubernetes to restart the container.

### Cross-question: What is a startup probe?

A startup probe gives a slow-starting application time to initialize before liveness and readiness checks are evaluated according to their configured behavior.

Startup probes are useful when applications need longer initialization times.
