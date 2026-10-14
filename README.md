# DevOps Project Interview Preparation

## E-commerce Microservices Platform — Beginner to Advanced

This repository contains a comprehensive interview preparation guide for a **microservices-based e-commerce application** with a production-oriented DevOps architecture.

---

## 📁 Table of Contents (Click to Open Any Folder)

Click any topic below to navigate directly to its questions, answers, keywords, and beginner-friendly explanations:

| # | Topic / Tool Folder | Description & Key Questions Covered |
|:---:|---|---|
| **01** | [📂 `01-Project-Overview`](./01-Project-Overview/) | **Q1–Q4:** Project architecture, microservices vs monolith, polyglot tech stack, customer order flow |
| **02** | [📂 `02-Linux-Networking`](./02-Linux-Networking/) | **Q5–Q8:** Linux fundamentals (`ps`, `df`, `free`, `journalctl`), SSH keys, ports, DNS vs HTTPS |
| **03** | [📂 `03-AWS-Fundamentals`](./03-AWS-Fundamentals/) | **Q9–Q14:** AWS VPC, CIDR, public/private subnets, Internet Gateway, NAT Gateway, Security Groups |
| **04** | [📂 `04-Terraform`](./04-Terraform/) | **Q15–Q21:** IaC, file layout, AWS provider, Floci endpoints, remote state in S3, modules, `count` vs `for_each` |
| **05** | [📂 `05-Docker`](./05-Docker/) | **Q22–Q25:** Images vs containers, multi-stage Dockerfiles, CMD vs ENTRYPOINT, container registries |
| **06** | [📂 `06-Jenkins-CICD`](./06-Jenkins-CICD/) | **Q26–Q30:** CI/CD concepts, commit-to-deploy workflow, Jenkinsfile pipelines, Trivy security scanning, GHCR |
| **07** | [📂 `07-Kubernetes`](./07-Kubernetes/) | **Q31–Q38:** Pods, Deployments, Services, ConfigMaps, Secrets, PVCs, readiness/liveness/startup probes |
| **08** | [📂 `08-Helm-Kustomize`](./08-Helm-Kustomize/) | **Q39–Q40:** Helm charts & `values.yaml`, Kustomize bases/overlays, when to use which |
| **09** | [📂 `09-GitOps-ArgoCD`](./09-GitOps-ArgoCD/) | **Q41–Q43:** GitOps principles, Argo CD synchronization, self-healing, resource pruning, Image Updater |
| **10** | [📂 `10-Gateway-API-ALB-DNS`](./10-Gateway-API-ALB-DNS/) | **Q44–Q50:** Gateway API (GatewayClass, Gateway, HTTPRoute), AWS Load Balancer Controller, Route 53, ACM |
| **11** | [📂 `11-Autoscaling`](./11-Autoscaling/) | **Q51–Q54:** HPA (Horizontal Pod Autoscaler), Karpenter (node compute provisioning), VPA, KEDA event-driven scaling |
| **12** | [📂 `12-Monitoring-Logging`](./12-Monitoring-Logging/) | **Q55–Q58:** Prometheus (TSDB metrics), Grafana (dashboards), Alertmanager, ELK Stack (Elasticsearch, Beats, Kibana) |
| **13** | [📂 `13-Security`](./13-Security/) | **Q59–Q62:** Principle of least privilege, Kubernetes RBAC, NetworkPolicy isolation, CPU/memory requests & limits |
| **14** | [📂 `14-Advanced-Architecture`](./14-Advanced-Architecture/) | **Q63–Q70:** Complete CI/CD flow, release rollbacks, Jenkins failure impact, bottleneck detection, pending Pods, ImagePullBackOff |
| **15** | [📂 `15-Design-Decisions`](./15-Design-Decisions/) | **Q71–Q75:** GHCR vs ECR, Jenkins on Bastion vs isolated, Terraform vs CloudFormation, Route 53 + ExternalDNS |
| **16** | [📂 `16-Rapid-Fire`](./16-Rapid-Fire/) | 25 rapid-fire definitions (EC2, EKS, ALB, AZ, IAM, ReplicaSet, DaemonSet, Canary, Blue-Green, etc.) |
| **17** | [📂 `17-Difficult-Cross-Questions`](./17-Difficult-Cross-Questions/) | **Q76–Q85:** Tough architectural cross-questions testing deep understanding beyond surface definitions |
| **18** | [📂 `18-Troubleshooting-Scenarios`](./18-Troubleshooting-Scenarios/) | Practical debug guides: service unreachable, local vs K8s differences, unknown HPA CPU, OutOfSync, ALB failures |
| **19** | [📂 `19-Interview-Explanation`](./19-Interview-Explanation/) | 60-second architecture pitch, the "Rule of 5", and interview strategy |
| **20** | [📂 `20-Study-Priority-Guide`](./20-Study-Priority-Guide/) | Priority matrix (Priority 1 to 12) and recommended weekly study schedule |

---

## 📋 Study Priority Order

| Priority | Topics | Folder Link | Questions Covered |
|:---:|---|---|:---:|
| **1** | Project Explanation | [📂 `01-Project-Overview`](./01-Project-Overview/) | Q1–Q4 |
| **2** | Linux and Networking | [📂 `02-Linux-Networking`](./02-Linux-Networking/) | Q5–Q8 |
| **3** | AWS Networking | [📂 `03-AWS-Fundamentals`](./03-AWS-Fundamentals/) | Q9–Q14 |
| **4** | Terraform | [📂 `04-Terraform`](./04-Terraform/) | Q15–Q21 |
| **5** | Docker | [📂 `05-Docker`](./05-Docker/) | Q22–Q25 |
| **6** | Jenkins and CI/CD | [📂 `06-Jenkins-CICD`](./06-Jenkins-CICD/) | Q26–Q30 |
| **7** | Kubernetes | [📂 `07-Kubernetes`](./07-Kubernetes/) | Q31–Q38 |
| **8** | Helm and Kustomize | [📂 `08-Helm-Kustomize`](./08-Helm-Kustomize/) | Q39–Q40 |
| **9** | GitOps and Argo CD | [📂 `09-GitOps-ArgoCD`](./09-GitOps-ArgoCD/) | Q41–Q43 |
| **10** | Gateway API and AWS Integration | [📂 `10-Gateway-API-ALB-DNS`](./10-Gateway-API-ALB-DNS/) | Q44–Q50 |
| **11** | Autoscaling and Observability | [📂 `11-Autoscaling`](./11-Autoscaling/) & [📂 `12-Monitoring-Logging`](./12-Monitoring-Logging/) | Q51–Q58 |
| **12** | Security, Architecture & Scenarios | [📂 `13-Security`](./13-Security/), [📂 `14-Advanced-Architecture`](./14-Advanced-Architecture/), [📂 `17-Difficult-Cross-Questions`](./17-Difficult-Cross-Questions/), [📂 `18-Troubleshooting-Scenarios`](./18-Troubleshooting-Scenarios/) | Q59–Q85 |

---

## 🎯 For Every Tool, Master These 5 Questions

1. **What is it?** (Beginner-friendly definition)
2. **Why did you choose it?** (Reason it belongs in your architecture)
3. **How does it work in your project?** (What happens behind the scenes)
4. **What happens if it fails?** (Impact and failure domain)
5. **How would you troubleshoot it?** (Step-by-step diagnostic workflow)

---

## ⚠️ Important Note for Interviews

Components tested locally are **implemented** (Docker containers, microservices, local storage, API communication). Components still being developed are part of your **planned cloud architecture** (EKS, Karpenter, ALB, Gateway API, Route 53). Always distinguish between the two so you present your project accurately.
