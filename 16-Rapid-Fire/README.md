# Part 16: Rapid-Fire Interview Questions

> These are short questions an interviewer may ask to check your fundamentals quickly.

| Question | Beginner-friendly answer | Key Term |
|---|---|---|
| **What is EC2?** | AWS's virtual server service. | Elastic Compute Cloud |
| **What is EKS?** | AWS's managed Kubernetes service. | Elastic Kubernetes Service |
| **What is an ALB?** | An Application Load Balancer that routes HTTP/HTTPS traffic. | Layer 7 Load Balancer |
| **What is an Availability Zone?** | An isolated location within an AWS Region. | Datacenter isolation / Redundancy |
| **What is an EIP?** | An Elastic IP, a static public IPv4 address in AWS. | Persistent Public IP |
| **What is IAM?** | AWS's identity and access-management service. | Permissions & Security |
| **What is a Kubernetes namespace?** | A way to organize and scope resources within a cluster. | Logical partition / Multi-tenancy |
| **What is a ReplicaSet?** | A Kubernetes controller that maintains a desired number of matching Pod replicas. | Pod count supervisor |
| **What is a DaemonSet?** | A controller that runs a Pod on each eligible node, according to its configuration. | One pod per node (e.g. logging/monitoring) |
| **What is a StatefulSet?** | A controller for workloads needing stable identities and ordered or persistent behavior. | Stateful apps / Databases |
| **What is a Job?** | A Kubernetes resource for running a task to completion. | Batch processing / Run-once task |
| **What is a CronJob?** | A Kubernetes resource that schedules Jobs. | Time-based scheduled task |
| **What is a rolling update?** | Gradually replacing old application replicas with new ones. | Zero-downtime deployment |
| **What is rollback?** | Returning to a previous known-good application or configuration version. | Disaster recovery / Undo release |
| **What is an API?** | An interface that allows software components to communicate. | Application Programming Interface |
| **What is REST?** | An architectural style commonly used to design HTTP APIs. | Representational State Transfer |
| **What is PostgreSQL?** | A relational database management system. | Relational DB / SQL persistence |
| **What is a webhook?** | An HTTP callback used to notify a system when an event occurs. | Event-driven trigger / Web push |
| **What is an artifact?** | A build output, such as a container image. | Deliverable binary / Container image |
| **What is observability?** | The ability to understand a system's internal state using outputs such as metrics, logs, and traces. | Telemetry / Health visibility |
| **What is a health check?** | A check used to determine an application's health or readiness. | Probe / Traffic readiness |
| **What is a rolling restart?** | A controlled restart of workload Pods through a supported rollout mechanism. | Graceful restart |
| **What is a canary deployment?** | A release strategy that initially sends a limited portion of traffic to a new version. | Gradual risk-managed rollout |
| **What is blue-green deployment?** | A release strategy using two environments so traffic can switch between versions. | Instant switch / Rollback ease |
| **What is a supply-chain vulnerability?** | A security weakness in a dependency, build process, image, or other part of software delivery. | Third-party package risk |
