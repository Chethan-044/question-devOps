# Part 9: GitOps and Argo CD

> This section is central to the architecture you selected.

---

## Q41. What is GitOps?

**🔑 Keywords:** Git as single source of truth, declarative state, automated reconciliation, drift detection, audit trail, version-controlled infrastructure

**Beginner-Friendly Answer:**
GitOps is a practice where your Git repository is the single source of truth for your entire cluster. Instead of a developer or CI server running `kubectl apply` directly against production (which is risky and leaves no audit trail), you commit your desired Kubernetes YAML files into Git. An automated agent running inside the cluster (like Argo CD) constantly watches Git and makes sure the real cluster matches what Git says. If someone manually tampers with the cluster, GitOps can undo it.

**Detailed Answer:**
GitOps is an approach where Git stores the desired state of the system, and automation continuously reconciles the running environment with that desired state.

In our project, Git stores the Kubernetes configuration that Argo CD uses to deploy the application.

This makes changes reviewable and provides a history of intended deployments.

### Cross-question: Why is Git the source of truth?

Git provides version history, code review, and a record of intended configuration changes.

It lets the team see what should be deployed and who changed the desired state.

However, Git only acts as the source of truth if the deployment workflow is configured consistently and unmanaged changes are handled appropriately.

---

## Q42. What is Argo CD?

**🔑 Keywords:** GitOps continuous delivery, Kubernetes controller, desired vs live state, reconciliation, self-healing, sync, pruning

**Beginner-Friendly Answer:**
Argo CD is a continuous delivery tool specifically built for Kubernetes following GitOps principles. It lives inside your cluster and acts like a watchdog: it pulls the manifests from your Git repo and compares them with what is actually running in Kubernetes. If it sees a difference (e.g., someone updated an image tag in Git), it "syncs" the cluster to match Git. If enabled, it also has "self-healing" — if someone accidentally changes a live deployment, Argo CD automatically reverts it back to what's defined in Git.

**Detailed Answer:**
Argo CD is a GitOps continuous delivery tool for Kubernetes.

It watches configured Git repositories and compares the desired application state with the actual cluster state.

If the states differ, Argo CD can report the difference and, when configured for automated synchronization, reconcile the cluster.

### Cross-question: What is self-healing in Argo CD?

Self-healing means Argo CD can automatically correct certain live-state changes that differ from the desired configuration in Git, when automated synchronization and self-healing are configured.

For example, if someone manually changes a managed Deployment's replica count, Argo CD may restore the Git-defined value.

### Cross-question: What is pruning?

Pruning allows Argo CD to delete resources that were previously managed by the application but have been removed from the desired configuration, subject to its settings.

It must be configured carefully because deleting a manifest from Git can have consequences for the corresponding live resource.

---

## Q43. What is Argo CD Image Updater?

**🔑 Keywords:** image update automation, registry polling, semantic versioning, Git commit back, continuous deployment, automated sync

**Beginner-Friendly Answer:**
Argo CD Image Updater connects your container registry to GitOps. Normally, when Jenkins builds and pushes a new image like `order-service:1.2.1` to GHCR, someone would need to update the YAML in Git. Argo CD Image Updater automates this: it watches GHCR, detects that a newer eligible version is published, updates the image tag in Git (or via Argo CD parameter overrides), and Argo CD immediately deploys the new version.

**Detailed Answer:**
Argo CD Image Updater automates image-update workflows for applications managed by Argo CD.

In our architecture, Jenkins publishes a new application image to GHCR. Image Updater can detect eligible image versions and update the desired deployment reference using a supported update method.

Argo CD then reconciles the updated desired state with Kubernetes.

### Cross-question: Does Argo CD Image Updater build images?

No. Jenkins or another CI system builds and publishes the images.

Image Updater handles image selection and desired-state updates, not application compilation.

### Cross-question: Why do we need image versioning?

Versioning helps identify the exact application build that is deployed, simplifies rollbacks, and improves traceability between source code, CI runs, and running workloads.
