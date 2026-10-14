# Section 14: Autoscaling and Resource Management

---

## Q134. What is the Horizontal Pod Autoscaler (HPA)?

**🔑 Keywords:** Horizontal Pod Autoscaler, replica count adjustment, CPU/memory metrics, custom metrics, scaling out/in

**Beginner-Friendly Answer:**
HPA (Horizontal Pod Autoscaler) automatically scales the number of Pod copies (replicas) up or down based on observed demand. If CPU usage on the Order Service spikes above 70%, HPA launches 4 more Order Service Pods. When the traffic rush dies down, HPA gently reduces the replica count to save cluster resources.

**Detailed Answer:**
HPA adjusts the number of Pod replicas based on configured metrics and scaling rules. It can use CPU, memory, or supported custom and external metrics.

---

## Q135. What is the difference between HPA and cluster autoscaling?

**🔑 Keywords:** application tier scaling (HPA) vs infrastructure tier scaling (Cluster Autoscaler / Karpenter), pending pods

**Beginner-Friendly Answer:**
- **HPA (Pod level):** Decides *how many copies of your application* should run (e.g. scaling from 3 to 15 Pods).
- **Cluster Autoscaling (Node level):** Decides *how many EC2 servers* the cluster needs. If HPA creates 12 new Pods and there's no room left on the existing servers, the cluster autoscaler boots up new EC2 nodes so those Pods have a place to run.

**Detailed Answer:**
HPA changes the number of Pod replicas. Cluster autoscaling mechanisms change the amount of compute capacity available to run workloads, for example by adding or removing nodes.

---

## Q136. What is Karpenter?

**🔑 Keywords:** just-in-time node provisioning, Karpenter controller, NodePool CRD, instance right-sizing, cost optimization

**Beginner-Friendly Answer:**
Karpenter is an advanced, high-performance node autoscaler developed by AWS for Kubernetes. Unlike the older Cluster Autoscaler (which had to scale pre-configured Auto Scaling Groups), Karpenter bypasses ASGs: it looks directly at pending Pods, calculates the exact CPU and memory needed, launches the cheapest and best-fitting EC2 instance in seconds, and automatically consolidates underutilized nodes to minimize AWS costs.

**Detailed Answer:**
Karpenter is a Kubernetes node-provisioning tool that can provision suitable compute capacity in response to workload scheduling requirements and configured constraints. Its exact behavior depends on the provider and version.

---

## Q137. What is VPA?

**🔑 Keywords:** Vertical Pod Autoscaler, container sizing, CPU/memory requests, right-sizing workloads, in-place vs restart

**Beginner-Friendly Answer:**
While HPA scales *out* (adding more identical Pods), VPA scales *up* (making a single Pod bigger). If your container is constantly choking for memory, VPA observes this and automatically increases its memory and CPU request values so it doesn't crash from out-of-memory errors.

**Detailed Answer:**
Vertical Pod Autoscaler recommends or adjusts Pod resource requests, and in supported configurations may update resource settings. Its behavior depends on the selected mode and workload constraints.

---

## Q138. What is KEDA?

**🔑 Keywords:** Kubernetes Event-driven Autoscaling, external triggers, queue length (SQS, Kafka, RabbitMQ), scale-to-zero

**Beginner-Friendly Answer:**
KEDA (Kubernetes Event-driven Autoscaling) allows you to scale Pods based on real-world events instead of waiting for CPU to spike. For example, if there are 2,000 customer orders waiting in an Amazon SQS message queue or Kafka topic, KEDA instantly scales up your worker Pods to process them. When the queue is empty, KEDA can scale the Pods down to 0 to save 100% of costs.

**Detailed Answer:**
KEDA is a Kubernetes-based event-driven autoscaling component that supports scaling workloads using external event sources and metrics, including scale-to-zero patterns for supported configurations.

---

## Q139. Why might HPA fail to scale a workload?

**🔑 Keywords:** missing metrics-server, undefined `resources.requests.cpu`, target thresholds, pending pods, node capacity exhaustion

**Beginner-Friendly Answer:**
If HPA isn't scaling your application:
1. `metrics-server` is not installed or crashing (HPA shows `<unknown>/70%`).
2. The Deployment manifest forgot to define `resources.requests.cpu` (HPA cannot calculate utilization without a baseline request).
3. Max replica limit in the HPA configuration has already been reached.
4. Or, HPA *did* scale the replica count, but the new Pods are stuck in `Pending` because the cluster has no free CPU/memory on any worker node.

**Detailed Answer:**
Possible causes include missing metrics, unsuitable resource requests, incorrect HPA configuration, unavailable capacity, scheduling constraints, or a workload that cannot usefully scale through additional replicas.

### Cross-question: What if HPA increases replicas but the new Pods remain Pending?

**Answer:**
I would inspect Pod events, node capacity, resource requests, affinity rules, taints and tolerations, persistent-volume constraints, and whether the cluster has a functioning capacity-provisioning mechanism.
