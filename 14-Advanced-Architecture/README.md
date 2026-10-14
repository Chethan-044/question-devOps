# Part 14: Advanced Architecture Questions

> These questions test whether you understand how the tools interact rather than merely knowing their definitions.

---

## Q63. Explain the complete CI/CD workflow from code commit to application deployment.

**🔑 Keywords:** developer commit, webhook, Jenkins pipeline, Trivy scan, GHCR publish, GitOps repo update, Argo CD reconcile, EKS scheduling

**Beginner-Friendly Answer:**
Here is the whole automated pipeline from start to finish:
1. Developer writes code and pushes a Git commit.
2. A webhook notifies Jenkins.
3. Jenkins pulls the code, runs unit tests, and packages the code into a Docker image.
4. Jenkins scans that image for security holes using Trivy. If safe, it pushes the image to GitHub Container Registry (GHCR) with a clean version tag.
5. The desired deployment manifest in Git is updated with the new tag.
6. Argo CD sees that Git was updated, syncs with our AWS EKS cluster, and tells Kubernetes to do a rolling update of the Pods.
7. Prometheus monitors the new Pods to ensure response times and error rates remain normal.

**Detailed Answer:**
A developer pushes a change to GitHub. Jenkins receives the configured trigger and checks out the source code.

The pipeline installs dependencies, runs tests, builds the appropriate service image, and scans it using Trivy.

If the pipeline passes the required checks, Jenkins publishes the image to GHCR with an identifiable tag or digest.

The desired deployment configuration is then updated using the chosen image-update workflow. Argo CD detects the change in Git and reconciles the Kubernetes resources.

Kubernetes schedules the required Pods, pulls the referenced images, and maintains the desired workload state.

Monitoring and logging systems help us verify application health and investigate problems after deployment.

### Cross-question: Does Jenkins directly deploy every Kubernetes manifest in this design?

Not necessarily. Our design separates CI from GitOps-based CD. Jenkins builds and publishes the image, while Argo CD manages the deployment state.

Jenkins may trigger or update the desired-state repository, but Argo CD remains responsible for reconciling that state with Kubernetes.

---

## Q64. What happens if a newly deployed application version fails?

**🔑 Keywords:** Pod events, kubectl logs, rollback, revert Git commit, Argo CD reconciliation, blast radius control

**Beginner-Friendly Answer:**
If a bad version gets deployed and starts crashing, the first step is to quickly inspect what went wrong using `kubectl describe pod` and `kubectl logs`. Because we use GitOps, fixing it in production is clean: you simply revert the bad commit in Git (or roll back via Argo CD). Argo CD immediately detects the reversion and redeploys the previous, known-working version of the application.

**Detailed Answer:**
First, I would identify the failure using deployment status, Pod events, application logs, and monitoring data.

I would check whether the failure is caused by image pulling, configuration, readiness probes, resource constraints, application errors, or dependency failures.

If the problem is caused by a bad release, I would restore a known-good desired configuration or use the appropriate rollback procedure.

With GitOps, the rollback can involve reverting the relevant configuration commit and allowing Argo CD to reconcile the previous desired state.

### Cross-question: Why not manually change the live Deployment and leave it that way?

If Git remains the source of truth, Argo CD may restore the configuration defined in Git.

A durable rollback should therefore update the desired state or use a supported release-management procedure consistent with the GitOps workflow.

---

## Q65. What happens if Jenkins goes down?

**🔑 Keywords:** CI impact vs runtime impact, running applications unaffected, single point of failure (SPOF), Bastion architecture

**Beginner-Friendly Answer:**
If Jenkins crashes, your live shopping website will keep running completely normally! Users can still browse products and buy items because Kubernetes and Argo CD run independently of Jenkins. The only thing you can't do is build and deploy *new* code updates until Jenkins is brought back online. In a learning project, running Jenkins on a Bastion is okay, but for production, Jenkins needs high availability and backups.

**Detailed Answer:**
New CI pipeline executions may be delayed or unavailable, depending on the Jenkins architecture.

Already deployed workloads can continue running because Jenkins is not responsible for maintaining their runtime state.

Argo CD and Kubernetes can continue their reconciliation and workload-management functions if they remain healthy and do not depend on Jenkins for each operation.

### Cross-question: Does putting Jenkins on a Bastion automatically make it highly available?

No. A Bastion is a host used for controlled administration. Running Jenkins on it does not automatically provide Jenkins high availability.

For a production design, we would evaluate host failure, persistent Jenkins data, backups, agent architecture, security, and recovery procedures.

For our learning environment, a single Jenkins instance is a reasonable starting point, but it is a single point of failure.

---

## Q66. What happens if the Product Service becomes unavailable?

**🔑 Keywords:** cascading failure, dependent services, circuit breaker, timeouts, non-idempotent retries, 503 error handling

**Beginner-Friendly Answer:**
If the Product Service goes down, shoppers won't be able to see product details or place new orders (because the Order Service needs to check product pricing before confirming the order). In our microservices setup, we must have proper timeouts and error handling so that a dead Product Service doesn't cause the Order Service to freeze indefinitely or crash from hanging connections.

**Detailed Answer:**
Requests that depend on the Product Service may fail, including order creation if the Order Service needs to validate product details.

Kubernetes may restart failed containers, but a restart alone does not guarantee that the service will recover.

I would inspect Pod status, readiness, logs, Service endpoints, resource usage, and downstream dependencies.

The application should also use sensible timeouts and error handling so a failed dependency does not cause every request to hang indefinitely.

### Cross-question: Should we retry every failed request?

No. Retries can amplify load during an outage.

They should be bounded and used where appropriate. Non-idempotent operations, such as creating an order, need particular care to prevent duplicate processing.

---

## Q67. What happens if PostgreSQL becomes unavailable?

**🔑 Keywords:** storage failure, persistent bottleneck, database connection pool, PVC durability, read replicas, backups

**Beginner-Friendly Answer:**
Because all user, product, and order records live in PostgreSQL, the Storage Service won't be able to read or write any data while the database is down. New orders will fail with an error. To diagnose, I would check if the PostgreSQL Pod crashed, check its PVC disk space, and inspect the logs. In real production, you use managed services like Amazon RDS Multi-AZ to prevent single points of failure for your database.

**Detailed Answer:**
The Storage Service will be unable to complete database operations while PostgreSQL is unavailable.

Requests that require persistence may fail, and dependent services may return errors.

I would inspect the PostgreSQL Pod, PVC, database logs, connectivity, credentials, and available storage.

For a production database, we would also plan backups, restore testing, availability, monitoring, and recovery procedures.

### Cross-question: Is storing PostgreSQL data in a PVC enough for production?

No. A PVC provides persistent storage, but it does not by itself provide database high availability, tested backups, disaster recovery, or protection against every storage failure.

---

## Q68. What happens when traffic increases dramatically?

**🔑 Keywords:** HPA replica scaling, Karpenter node provisioning, ALB traffic distribution, shared dependency bottleneck

**Beginner-Friendly Answer:**
When thousands of shoppers flood the site:
1. HPA detects high CPU/memory or request rates on our Pods and increases replica counts (e.g., from 3 to 15 Pods).
2. If the current EC2 nodes don't have enough room, Karpenter detects pending Pods and provisions new EC2 instances in minutes.
3. The Application Load Balancer spreads incoming traffic across all healthy Pods.
4. However, if the PostgreSQL database or a downstream service gets overwhelmed, adding more web Pods won't fix it — we must monitor the whole chain to find the actual bottleneck.

**Detailed Answer:**
First, I would check application metrics, request latency, error rates, CPU, memory, and current replica counts.

If HPA is configured with suitable metrics and scaling policies, it may increase the number of application Pods.

If the cluster lacks sufficient capacity to schedule those Pods, a correctly configured Karpenter installation may provision additional nodes.

The load balancer distributes traffic to healthy backends according to its configuration, while monitoring helps us determine whether the application, database, or another dependency has become the bottleneck.

### Cross-question: Will adding more application Pods always solve the problem?

No. If the bottleneck is PostgreSQL, a downstream service, a connection pool, or another shared dependency, increasing frontend or API replicas may not help.

Scaling should be based on measurements and an understanding of the actual bottleneck.

---

## Q69. How would you troubleshoot a Pod stuck in Pending?

**🔑 Keywords:** pending state, kubectl describe pod, scheduling failure, insufficient CPU/memory, nodeSelector, tolerations, PVC binding

**Beginner-Friendly Answer:**
If a Pod is stuck in "Pending", it means Kubernetes cannot find a node that has enough free resources or meets its requirements. My first command is always `kubectl describe pod <pod-name>`. I scroll to the "Events" section at the bottom. It usually tells you the exact problem: "0/3 nodes available: insufficient memory" or "persistentvolumeclaim not bound". From there, you either reduce resource requests, fix node issues, or trigger node scaling via Karpenter.

**Detailed Answer:**
I would begin with:

```bash
kubectl get pods -n ecommerce
kubectl describe pod <pod-name> -n ecommerce
kubectl get events -n ecommerce --sort-by=.lastTimestamp
```

The describe output and events help identify scheduling failures.

Possible causes include insufficient CPU or memory, node selectors that cannot be satisfied, missing tolerations, unavailable persistent storage, or scheduling constraints.

If node capacity is insufficient, I would inspect node resources and autoscaler behavior rather than assuming the application itself is broken.

---

## Q70. How would you troubleshoot ImagePullBackOff?

**🔑 Keywords:** ImagePullBackOff, ErrImagePull, image tag typo, private registry secret, imagePullSecrets, registry network/rate limits

**Beginner-Friendly Answer:**
`ImagePullBackOff` means Kubernetes tried to download your container image and failed, so it's waiting before trying again. Run `kubectl describe pod <name>` and look at the Events. The top 3 causes are: (1) A typo in the image name or tag, (2) The image was never pushed to the registry by CI, or (3) Kubernetes lacks credentials to pull from a private registry (missing or incorrect `imagePullSecret`).

**Detailed Answer:**
I would inspect the Pod events:

```bash
kubectl describe pod <pod-name> -n ecommerce
```

Then I would verify that the image repository and tag exist, the image reference is correct, the registry is reachable, and the workload has valid registry credentials if the image is private.

For our architecture, I would also confirm that Jenkins successfully pushed the image to GHCR and that the deployment references the correct tag or digest.
