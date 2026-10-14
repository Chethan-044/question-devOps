# Section 12: GitOps, Argo CD, and Image Updater

---

## Q113. What is GitOps?

**🔑 Keywords:** Git as single source of truth, declarative configuration, automated reconciliation loop, pull-based CD, drift detection

**Beginner-Friendly Answer:**
GitOps is a modern way to manage Kubernetes deployments where **Git is the single source of truth**. Instead of developers or CI tools running `kubectl apply` directly against production servers (which is dangerous and lacks tracking), you push Kubernetes YAML files to a Git repo. An in-cluster operator (like Argo CD) continuously checks Git and automatically updates the cluster to match.

**Detailed Answer:**
GitOps is an operational approach in which the desired system configuration is declared in version control and automated controllers reconcile the running environment with that configuration.

---

## Q114. What is Argo CD?

**🔑 Keywords:** declarative GitOps controller, Kubernetes operator, Application CRD, visual dashboard, desired vs live state

**Beginner-Friendly Answer:**
Argo CD is a declarative GitOps continuous delivery tool for Kubernetes. It runs inside your cluster and watches your Git repository. It constantly compares the desired manifests in Git with what is actually running in the cluster. If there is a difference, Argo CD displays it on a visual dashboard and synchronizes the cluster automatically.

**Detailed Answer:**
Argo CD is a GitOps continuous-delivery tool for Kubernetes. It monitors configured sources and compares the desired manifests with the live cluster resources.

---

## Q115. What is the difference between Synced and Healthy in Argo CD?

**🔑 Keywords:** Synced (Git state == Live state), Healthy (workload readiness & passing probes), out-of-sync vs degraded

**Beginner-Friendly Answer:**
- **`Synced`:** Means the YAML in Kubernetes matches the YAML in Git 100%.
- **`Healthy`:** Means the Pods in Kubernetes are actually running and their readiness probes are passing!
An application can be **Synced** (Argo CD applied the exact image tag written in Git) but **Degraded/Unhealthy** (that image is crashing on startup with a database connection error).

**Detailed Answer:**
Synced indicates that the live resources match the desired state according to Argo CD's comparison. Healthy indicates that resources meet Argo CD's health assessment. An application can be synced but unhealthy.

---

## Q116. What is automated sync?

**🔑 Keywords:** automated sync policy, polling interval, webhook trigger, hands-off continuous delivery

**Beginner-Friendly Answer:**
By default, you can configure Argo CD to require manual approval before applying changes. When you enable **Automated Sync**, Argo CD immediately applies any new commits pushed to the Git repo without human intervention, achieving full continuous deployment.

**Detailed Answer:**
Automated sync allows Argo CD to apply desired configuration changes without requiring a user to initiate every sync manually, subject to its settings and permissions.

---

## Q117. What is self-healing in Argo CD?

**🔑 Keywords:** self-heal option, automated drift correction, preventing manual configuration drift, cluster integrity

**Beginner-Friendly Answer:**
Self-healing is like an automated security guard for your cluster. If an engineer logs into the cluster and manually edits a Deployment using `kubectl edit` (for example, changing replicas from 2 to 10), Argo CD notices the unauthorized change and immediately overrides it, reverting the cluster back to what Git says.

**Detailed Answer:**
With the relevant configuration enabled, Argo CD can reconcile certain live changes that differ from the declared desired state.

### Cross-question: Can self-healing fix every application failure?

**Answer:**
No. It can reconcile configuration drift, but it cannot necessarily fix broken application code, unavailable dependencies, invalid images, insufficient capacity, or external network failures.

---

## Q118. What is Argo CD Image Updater?

**🔑 Keywords:** image update automation, container registry watcher, semver matching, Git write-back, automated PRs/commits

**Beginner-Friendly Answer:**
Argo CD Image Updater connects your container registry to your Git repo. When Jenkins builds and pushes a new image (e.g. `order-service:1.3.0`), Image Updater detects the new image in GHCR, automatically commits the updated tag into the Git repository, and Argo CD syncs the new version to Kubernetes without any human typing.

**Detailed Answer:**
Argo CD Image Updater automates image-version updates for configured Argo CD applications according to supported image-update strategies and write-back configuration.

---

## Q119. Why separate application code from deployment configuration?

**🔑 Keywords:** repository separation, app repo vs config repo, build isolation, security boundaries, deployment history

**Beginner-Friendly Answer:**
Separating application source code and deployment manifests into two different Git repos is an industry best practice:
1. Prevents infinite CI build loops (updating an image tag shouldn't trigger Jenkins to recompile application code).
2. Tighter access control (developers can push code, but only DevOps/leads or automated tools can push to the production deployment repo).
3. Clean Git history specifically dedicated to release tracking.

**Detailed Answer:**
Separating them can make releases easier to review and audit. The application repository contains the source code, while deployment configuration declares how the application should run in an environment.

---

## Q120. What is configuration drift in GitOps?

**🔑 Keywords:** live state vs desired state discrepancy, manual interventions, drift remediation, audit alerts

**Beginner-Friendly Answer:**
Configuration drift occurs when someone manually changes something directly in the Kubernetes cluster (e.g., adding an environment variable or changing resource limits) without updating the Git repository. GitOps tools detect this discrepancy (marking the app `OutOfSync`) and allow you to reconcile it back to Git.

**Detailed Answer:**
Configuration drift is a difference between the desired configuration and the live state. GitOps tools detect drift and, when configured and permitted, reconcile it.

---

## Q121. What happens if an image is published but the Kubernetes configuration still references the old image?

**🔑 Keywords:** image registry vs manifest reference, deployment gap, GitOps reconciliation trigger

**Beginner-Friendly Answer:**
Nothing happens in Kubernetes! Kubernetes does not automatically scan Docker registries. Kubernetes only runs whatever image tag is specified in its Deployment manifests. If the manifest in Git still points to `:1.0.0`, Kubernetes will continue running `:1.0.0` even if `:2.0.0` was pushed to the registry hours ago.

**Detailed Answer:**
Publishing an image alone does not guarantee deployment. The desired configuration must reference the new image, and the deployment controller must successfully reconcile that configuration.

---

## Q122. Why use immutable image references?

**🔑 Keywords:** SHA256 digest, mutable tags anti-pattern (`:latest`), supply chain integrity, reproducible deployments

**Beginner-Friendly Answer:**
Tags like `:latest` or `:v1` are **mutable** — someone can accidentally overwrite them with a different image tomorrow. An **immutable image reference** uses the image's unique cryptographic SHA256 digest (e.g., `image@sha256:7f83...`). This guarantees that every single server pulls the exact same binary bits, preventing supply-chain tampering and making deployments 100% reproducible.

**Detailed Answer:**
Immutable references, such as image digests, identify specific image content. They improve traceability and help prevent an image tag from silently pointing to different content over time.
