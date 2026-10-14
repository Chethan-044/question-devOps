# Section 17: Advanced Scenario-Based Questions

> These questions test whether you understand how the tools work together, not just their definitions.

---

## Q159. Jenkins successfully publishes an image, but Argo CD still runs the old version. What would you check?

**🔑 Keywords:** image tag update, deployment repo, Argo CD Image Updater write-back, sync status, polling interval

**Beginner-Friendly Answer:**
If Jenkins built and pushed the image, but Argo CD hasn't deployed it:
1. Did the deployment manifest in Git actually get updated with the new tag? (Publishing to GHCR does nothing until Git changes).
2. If using Argo CD Image Updater, check its logs: did it detect the new tag? Did it have write permissions to push the commit to Git?
3. In Argo CD, check if auto-sync is enabled or if the repo polling interval hasn't triggered yet (click "Refresh").

**Detailed Answer:**
I would verify the image tag or digest that Jenkins published, check whether the desired deployment configuration was updated, inspect Image Updater configuration if used, and check whether Argo CD detected and applied the configuration change.

### Follow-up: Why isn't publishing an image enough?

**Answer:**
Kubernetes deploys the image reference in its desired configuration. The new image must be referenced and the deployment must reconcile successfully.

---

## Q160. A Pod is Running, but the website is unavailable. What would you check?

**🔑 Keywords:** readiness probe failure, Service selector mismatch, ALB target group status, Route 53 DNS, Security Group ingress

**Beginner-Friendly Answer:**
"Running" only means the container process is alive. I would systematically check:
1. `kubectl get pods`: Are the readiness probes failing (e.g. `0/1 READY`)?
2. `kubectl describe svc`: Does the Service selector match the Pod labels, or are Endpoints empty?
3. AWS Console: Are the ALB Target Groups marked `Healthy`?
4. Security Groups: Is port 80/443 open to incoming traffic?
5. DNS: Does the domain name resolve to the correct ALB DNS address?

**Detailed Answer:**
Running only describes a Pod's execution state; it does not prove the application is reachable. I would check readiness, logs, Service selectors, endpoints, target health, route configuration, ALB listeners, DNS, TLS, and network access.

---

## Q161. Terraform says a resource must be replaced. What do you do?

**🔑 Keywords:** `(forces replacement)`, immutable cloud attributes, downtime risk, data loss assessment, state drift

**Beginner-Friendly Answer:**
NEVER type `yes` blindly! Some changes (like changing a subnet, VPC, or EC2 instance type/AMI) force AWS to completely destroy the existing resource and create a brand-new one (`-/+`). If that resource is an EC2 server or a database, this could cause immediate downtime or permanent data loss. I would inspect the plan output, find which specific attribute has `(forces replacement)`, and decide if that change is truly intended or if an alternative configuration avoids replacement.

**Detailed Answer:**
I would inspect the plan to identify which attribute forces replacement and assess the impact on availability, data, dependencies, and cost. I would verify whether the change is intended before applying it.

---

## Q162. The application works locally but fails on EKS. Why?

**🔑 Keywords:** `localhost` vs CoreDNS names, environment variables, IAM permissions, container architecture (ARM vs x86), persistent volume claims

**Beginner-Friendly Answer:**
Common differences between localhost and cloud Kubernetes:
1. **Hostnames:** The app used `localhost:5432` locally instead of Kubernetes Service DNS (`postgres:5432`).
2. **Missing Secrets/ConfigMaps:** Environment variables were not mounted into the EKS Pod.
3. **Architecture mismatch:** The Docker image was built on an Apple M1/M2 Mac (ARM64) but EKS nodes are Intel/AMD (x86_64).
4. **IAM Permissions:** The cloud Pod doesn't have the AWS IAM Role needed to talk to AWS services.

**Detailed Answer:**
The environments may differ in environment variables, secrets, network access, service discovery, CPU or memory availability, file permissions, image architecture, or database connectivity. I would compare the environments and inspect Kubernetes events and application logs.

---

## Q163. The frontend loads, but product information does not appear. How would you investigate?

**🔑 Keywords:** DevTools Network tab, HTTPRoute / ALB routing, CORS errors, backend Service endpoints, Storage Service dependency

**Beginner-Friendly Answer:**
1. Open the browser's Developer Tools (F12) -> Network tab to see the API call: is it returning 404, 500, or a CORS error?
2. If the API returns 502/503: Check if the Product Service Pod is healthy in Kubernetes.
3. Check Product Service logs with `kubectl logs`: is it throwing errors because it cannot reach the Storage Service or PostgreSQL?
4. Follow the chain from frontend -> ALB -> HTTPRoute -> Product Service -> Storage Service -> PostgreSQL.

**Detailed Answer:**
I would inspect the browser's network requests, frontend errors, API endpoints, Gateway or ALB routing, backend Service endpoints, Product Service logs, and its dependency on the Storage Service. I would also verify database connectivity and relevant configuration.

---

## Q164. An HPA has increased the replica count, but the application is still slow. Why?

**🔑 Keywords:** non-compute bottlenecks, database connection pool exhaustion, downstream dependency latency, CPU throttling

**Beginner-Friendly Answer:**
Adding more web servers (replicas) only helps if CPU or RAM on the application tier is the bottleneck. If the bottleneck is actually a slow database query, a locked table in PostgreSQL, connection pool exhaustion, or a slow third-party payment API, adding 20 more application Pods will just hammer the already-struggling database even harder and make things worse!

**Detailed Answer:**
Additional replicas may not solve the bottleneck if the database, a shared Storage Service, external APIs, CPU limits, connection pools, or network latency are limiting performance. I would examine end-to-end latency and resource metrics before changing the replica count again.

---

## Q165. The ALB reports unhealthy targets. What would you investigate?

**🔑 Keywords:** health check path (`/healthz`), target port, application listening interface (`0.0.0.0` vs `127.0.0.1`), Security Group rules

**Beginner-Friendly Answer:**
1. **Health Check Path:** Is the ALB checking `/` when the application only responds to `/api/health`?
2. **Listening IP:** Is the container listening on `127.0.0.1` (localhost only) instead of `0.0.0.0` (all interfaces)?
3. **Security Groups:** Does the worker node's security group allow incoming traffic on the application port from the ALB security group?
4. **Application Logs:** Is the app crashing as soon as the ALB sends a health check request?

**Detailed Answer:**
I would verify the health-check path, port, protocol, expected status code, target registration, application listening address, readiness, security-group rules, and backend logs. I would also confirm that the routing controller generated the intended target configuration.

---

## Q166. A deployment is marked Synced but not Healthy in Argo CD. What does this indicate?

**🔑 Keywords:** `Synced` (Git manifest applied) vs `Degraded` / `Progressing` (Pod crash or probe failure), live state diagnosis

**Beginner-Friendly Answer:**
It means Argo CD successfully applied the exact YAML file committed in Git (`Synced`), but the Pods created in Kubernetes are failing (`Unhealthy` or `Degraded`). For example, the new container might have a typo in its database password and is in `CrashLoopBackOff`. The GitOps controller did its job, but the application itself is broken.

**Detailed Answer:**
The live resources match the declared configuration, but one or more resources do not meet Argo CD's health assessment. I would identify the unhealthy resource and investigate its events, status, and application logs.

---

## Q167. Prometheus shows high CPU usage, but Grafana has no application dashboard. What would you check?

**🔑 Keywords:** Prometheus scrape target, PromQL query verification, Grafana data source configuration, dashboard panel queries

**Beginner-Friendly Answer:**
1. Check if the Prometheus Data Source in Grafana has a green "Connection Successful" test.
2. In Prometheus Web UI (`/targets`), verify that Prometheus is actively scraping the application Pod metrics endpoint.
3. In Grafana Explore tab, run a simple PromQL query like `up` or `container_cpu_usage_seconds_total` to see if raw metrics exist.
4. Check if the Grafana dashboard panel queries have typos in metric names or namespace labels.

**Detailed Answer:**
I would confirm that Prometheus is scraping the intended targets, that the relevant metrics exist, and that Grafana uses the correct data source. Then I would check dashboard queries, labels, variables, and permissions.

---

## Q168. A Trivy scan finds a critical vulnerability just before release. What should happen?

**🔑 Keywords:** release policy, severity threshold, exploitability assessment, mitigation exception, blocker vs tracking

**Beginner-Friendly Answer:**
The pipeline should automatically block the release. I would review the CVE: is there an updated base image or patch available? If yes, update the Dockerfile, rebuild, and rescan. If no official fix exists yet, assess whether the vulnerable package is exposed to user input; if safe, request a formal, documented security exception with the security team before releasing.

**Detailed Answer:**
I would verify the finding and assess the release policy, exploitability, exposure, and availability of a fix. If the policy blocks critical findings, the release should not proceed until the issue is resolved or an explicitly authorized exception is granted.

---

## Q169. Someone manually changes a Kubernetes deployment, and the change disappears. Why?

**🔑 Keywords:** GitOps self-healing, automated drift reconciliation, source of truth enforcement, Argo CD

**Beginner-Friendly Answer:**
This is the power of GitOps with **Argo CD Self-Healing** enabled! When someone manually runs `kubectl scale` or `kubectl edit` directly on the cluster, Argo CD detects that the live cluster no longer matches Git, and immediately overwrites the manual changes to restore the declared desired state in Git. Changes must be committed to Git to be permanent.

**Detailed Answer:**
The GitOps controller may be reconciling the live cluster back to the desired state stored in Git. The permanent change should normally be made through the approved configuration workflow.

---

## Q170. The private EKS nodes cannot pull images. What would you check?

**🔑 Keywords:** NAT Gateway route table, VPC endpoints, image pull secrets (GHCR / private registries), DNS resolution

**Beginner-Friendly Answer:**
1. **Outbound Internet:** Does the private subnet have a default route (`0.0.0.0/0`) pointing to an active NAT Gateway in a public subnet?
2. **Registry Credentials:** Is there a valid `imagePullSecret` with working credentials to pull from GHCR?
3. **AWS VPC Endpoints:** If pulling from ECR, are the VPC Endpoints for ECR (`ecr.api` and `ecr.dkr`) and S3 configured?
4. **DNS:** Is CoreDNS running properly on the nodes?

**Detailed Answer:**
I would check image names and tags, registry credentials and permissions, DNS resolution, outbound connectivity, route tables, NAT Gateway or relevant VPC endpoints, network rules, and the Pod's image-pull events.

---

## Q171. A service suddenly starts returning database connection errors. What would you investigate?

**🔑 Keywords:** database availability, connection pool limits, secret rotation, security groups, disk space / IOPS

**Beginner-Friendly Answer:**
1. **Database Health:** Is PostgreSQL running, or did it run out of disk space or hit max connections?
2. **Credentials:** Were database secrets recently rotated without updating the application's ConfigMap or Secret?
3. **Network & Security Groups:** Did someone change a Security Group rule or NetworkPolicy blocking port 5432?
4. **Database Logs:** Check the PostgreSQL server logs directly to see if queries are being rejected due to authentication failures or resource limits.

**Detailed Answer:**
I would check database availability, credentials, secret updates, DNS, network access, connection limits, connection-pool settings, recent releases, and database logs. I would avoid assuming that the application itself is the only source of the failure.

---

## Q172. How would you roll back a failed application release?

**🔑 Keywords:** GitOps rollback, `git revert`, declarative desired state, post-rollback verification, drift prevention

**Beginner-Friendly Answer:**
Because we use GitOps, the cleanest, most reproducible rollback is to run `git revert <commit-sha>` on our deployment repository and push it. Argo CD immediately detects the reversion and redeploys the previous, known-good container image and configuration. Never just manually edit the live cluster with `kubectl`, because Argo CD or future syncs will simply overwrite your manual fix!

**Detailed Answer:**
I would identify the last known-good image and configuration, then use the project's approved release or GitOps workflow to restore them. I would verify the rollout, application health, and dependent services afterward.

### Follow-up: Why not simply edit the live Deployment and stop there?

**Answer:**
A manual change can create drift and may be overwritten by the GitOps controller. The desired configuration should be updated so that the rollback is traceable and reproducible.
