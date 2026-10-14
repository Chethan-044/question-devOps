# Part 2: DevOps Core Comprehensive Interview Guide (Q1–Q172)

> **Complete 18-Section Interview Preparation Guide covering Fundamentals, Architecture, Tools, Security, and Troubleshooting Scenarios.**

---

## 📁 Sections & Tools Table of Contents (Click to Open)

| # | Section Folder | Key Topics & Questions Covered |
|:---:|---|---|
| **01** | [📂 `01-Project-Overview`](./01-Project-Overview/) | **Q1–Q8:** Project architecture, microservices vs monolith, polyglot tech stack, CI vs CD, developer push workflow |
| **02** | [📂 `02-Linux-Fundamentals`](./02-Linux-Fundamentals/) | **Q9–Q16:** Linux kernel vs distro, processes & PIDs (`ps`, `top`), threads, permissions (`chmod 755`), environment variables, `grep`/`find`/`tail`, ports |
| **03** | [📂 `03-Git-Version-Control`](./03-Git-Version-Control/) | **Q17–Q23:** Git vs GitHub, repositories, clone/pull/fetch, branches, merge conflicts, `.gitignore`, Git in GitOps |
| **04** | [📂 `04-Networking-Fundamentals`](./04-Networking-Fundamentals/) | **Q24–Q31:** IP addresses (IPv4/IPv6), subnets, CIDR notation, DNS resolution, TCP vs UDP, HTTP vs HTTPS, reverse proxy, public vs private subnets |
| **05** | [📂 `05-AWS-Cloud-Architecture`](./05-AWS-Cloud-Architecture/) | **Q32–Q45:** AWS platform, Regions & AZs, VPC, Internet Gateway, NAT Gateway, Route Tables, Security Groups vs NACLs, IAM roles, EKS, ALB vs NLB, Route 53, ACM, ExternalDNS |
| **06** | [📂 `06-Terraform-IaC`](./06-Terraform-IaC/) | **Q46–Q60:** IaC, declarative HCL, file structure, providers, modules, init/plan/apply/destroy, state & S3 locking, variables vs outputs, `count` vs `for_each`, drift, Ansible comparison, import, workspaces, troubleshooting |
| **07** | [📂 `07-Docker-Containerization`](./07-Docker-Containerization/) | **Q61–Q72:** Containers vs VMs, Docker images, Dockerfile instructions, multi-stage builds, CMD vs ENTRYPOINT, volumes, Compose, container registries, non-root security, scanning vs runtime monitoring |
| **08** | [📂 `08-Jenkins-CI-Pipeline`](./08-Jenkins-CI-Pipeline/) | **Q73–Q83:** Jenkins automation server, Jenkinsfile pipelines, controller vs agents, declarative syntax, webhooks, credentials store, CI workflow, test gates, Bastion trade-offs, stage failure handling, CI vs release |
| **09** | [📂 `09-Trivy-Security-Scanning`](./09-Trivy-Security-Scanning/) | **Q84–Q89:** Trivy scanner, pre-deployment image scanning, CVE definitions, severity vs exploitability, pipeline failure thresholds, scanner limitations & defense in depth |
| **10** | [📂 `10-Kubernetes-Fundamentals`](./10-Kubernetes-Fundamentals/) | **Q90–Q105:** K8s architecture, clusters, Pods, Deployments, ReplicaSets, Services (ClusterIP, NodePort, LoadBalancer), ConfigMaps, Secrets, Probes (readiness, liveness, startup), Requests vs Limits, Namespaces, Labels & Selectors, Rolling Updates, CrashLoopBackOff vs ImagePullBackOff |
| **11** | [📂 `11-Helm-And-Kustomize`](./11-Helm-And-Kustomize/) | **Q106–Q112:** Helm package manager, charts (`Chart.yaml`, `values.yaml`), releases, Helm vs Kustomize, Kustomize base & overlays, troubleshooting broken Helm releases |
| **12** | [📂 `12-GitOps-ArgoCD`](./12-GitOps-ArgoCD/) | **Q113–Q122:** GitOps principles, Argo CD architecture, Synced vs Healthy status, automated sync, self-healing, Image Updater, app code vs deployment config repo separation, drift reconciliation, immutable image digests |
| **13** | [📂 `13-Gateway-API-And-ALB`](./13-Gateway-API-And-ALB/) | **Q123–Q133:** Gateway API concepts, GatewayClass, Gateway, HTTPRoute, AWS Load Balancer Controller, TargetGroupConfiguration, Ingress vs Gateway API, Target Groups, ALB troubleshooting, TLS termination |
| **14** | [📂 `14-Autoscaling-Resource-Management`](./14-Autoscaling-Resource-Management/) | **Q134–Q139:** HPA (Horizontal Pod Autoscaler), Pod scaling vs Node scaling, Karpenter node provisioning, VPA (Vertical Pod Autoscaler), KEDA event-driven autoscaling, why HPA fails / pending pods |
| **15** | [📂 `15-Observability-Monitoring-Logging`](./15-Observability-Monitoring-Logging/) | **Q140–Q151:** Observability vs Monitoring, Prometheus (TSDB & PromQL), metrics types (counter, gauge, histogram), Grafana dashboards, Alertmanager, Elasticsearch, Kibana, Beats/Filebeat, logs vs metrics, investigating 500 error spikes, SLI / SLO / SLA |
| **16** | [📂 `16-Security-Principles`](./16-Security-Principles/) | **Q152–Q158:** Principle of least privilege, Kubernetes RBAC, NetworkPolicy internal firewalls, authentication vs authorization, secret rotation, encryption at rest vs in transit, software supply-chain security |
| **17** | [📂 `17-Advanced-Scenarios-Troubleshooting`](./17-Advanced-Scenarios-Troubleshooting/) | **Q159–Q172:** Real production scenarios: image pushed but old version running, Pod running but site down, Terraform forces replacement, local vs EKS differences, product info missing, slow app after HPA, unhealthy ALB targets, Synced but Unhealthy Argo CD, high CPU without dashboard, critical Trivy CVE before release, manual change disappearing, private EKS node pull failures, database connection errors, rolling back failed release |
| **18** | [📂 `18-Rapid-Fire-And-Strategy`](./18-Rapid-Fire-And-Strategy/) | 30 rapid-fire revision Q&As, 5-step interview preparation plan, and the core end-to-end change lifecycle story |

---

[⬅️ Back to Main Repository README](../README.md)
