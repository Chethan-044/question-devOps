# Part 11: HPA, Karpenter, and Scaling

> These questions are common in DevOps interviews because they test whether you understand what happens under load.

---

## Q51. What is HPA?

**🔑 Keywords:** Horizontal Pod Autoscaler, replica scaling, CPU/memory utilization, custom metrics, Metrics Server, scale out/in

**Beginner-Friendly Answer:**
HPA (Horizontal Pod Autoscaler) automatically scales the number of Pod copies (replicas) up or down based on load. Think of it like adding more cashier lanes at a supermarket during rush hour. If CPU usage on the Order Service goes above 70%, HPA tells Kubernetes to launch 3 more Order Service Pods. When the rush ends and CPU drops, HPA safely shuts down the extra Pods to save resources.

**Detailed Answer:**
HPA stands for Horizontal Pod Autoscaler.

It adjusts the number of Pod replicas based on configured metrics, such as CPU utilization, memory utilization where supported, or custom metrics.

In our architecture, HPA can scale application workloads when traffic or resource demand increases.

### Cross-question: What happens when traffic increases?

If the configured metrics exceed the HPA target and the metrics are available, HPA can increase the desired number of replicas.

Kubernetes then attempts to schedule the additional Pods.

If existing nodes do not have sufficient capacity, the Pods may remain Pending until capacity becomes available.

This is why Pod autoscaling alone does not solve every capacity problem.

---

## Q52. What is Karpenter?

**🔑 Keywords:** node autoscaling, just-in-time compute, node provisioning, bin-packing, right-sizing instances, replacement for Cluster Autoscaler

**Beginner-Friendly Answer:**
While HPA scales Pods, Karpenter scales the actual EC2 worker nodes (the virtual machines) underneath. If HPA orders 10 new Pods, but the existing nodes are full, those Pods get stuck in "Pending". Karpenter watches for pending Pods, calculates the exact instance type needed (e.g., "I need an instance with 4 vCPUs and 8GB RAM"), and launches that EC2 server in seconds. When load drops, Karpenter consolidates and shuts down unnecessary servers to cut AWS costs.

**Detailed Answer:**
Karpenter is a Kubernetes node-provisioning system that can launch and manage compute capacity according to workload scheduling requirements and its configured policies.

In our architecture, it is intended to add node capacity when existing nodes cannot accommodate workloads.

### Cross-question: What is the difference between HPA and Karpenter?

HPA changes the number of application Pod replicas.

Karpenter manages node capacity by provisioning appropriate nodes when required and configured.

They operate at different layers and can work together.

### Cross-question: What happens if HPA scales from two Pods to ten, but there is insufficient node capacity?

Kubernetes attempts to schedule the additional Pods. Some may remain Pending because the cluster has insufficient resources.

If Karpenter is correctly configured and the workloads meet its requirements, it can provision additional nodes.

If no suitable node can be provisioned, the Pods remain unscheduled until the issue is resolved.

---

## Q53. What is VPA?

**🔑 Keywords:** Vertical Pod Autoscaler, resource sizing, CPU/memory requests and limits, in-place update or restart, right-sizing containers

**Beginner-Friendly Answer:**
If HPA scales *out* (adding more identical Pods), VPA scales *up* (making a single Pod bigger). Think of HPA as hiring more workers, and VPA as giving a single worker more food and tools. VPA monitors your containers, notices if a Pod is constantly starving for memory, and recommends or automatically adjusts its CPU and memory requests so it doesn't run out of memory (OOM).

**Detailed Answer:**
VPA stands for Vertical Pod Autoscaler.

It recommends or adjusts container CPU and memory requests based on observed usage, depending on its configuration and operating mode.

Unlike HPA, which changes the number of replicas, VPA focuses on the resources allocated to individual containers.

### Cross-question: Can HPA and VPA be used together?

Yes, but their interaction requires careful configuration.

For example, CPU-based HPA and a VPA mode that changes CPU requests can affect each other's calculations. The selected metrics and scaling policies must be compatible.

---

## Q54. What is KEDA?

**🔑 Keywords:** event-driven autoscaling, external triggers, queue length, Kafka lag, RabbitMQ, SQS, zero-to-one scaling

**Beginner-Friendly Answer:**
Standard HPA usually reacts to CPU or RAM usage, which is often a "lagging indicator" (by the time CPU spikes, users are already waiting). KEDA (Kubernetes Event-driven Autoscaling) lets you scale based on real-time external events — like how many messages are sitting in an Amazon SQS queue, Kafka topic, or Redis stream. If there are 5,000 orders in the queue, KEDA immediately scales up the Order Workers to process them, even scaling from 0 Pods to save money when the queue is empty.

**Detailed Answer:**
KEDA is an event-driven autoscaling component for Kubernetes.

It can scale workloads based on external event sources and metrics, such as queue length, when the relevant scaler is supported and configured.

### Cross-question: When would you use KEDA instead of ordinary HPA?

HPA is appropriate for common metric-driven scaling, such as CPU utilization.

KEDA is useful when scaling should depend on event-driven metrics, such as messages waiting in a queue.

KEDA can work with Kubernetes HPA rather than replacing it in every scenario.
