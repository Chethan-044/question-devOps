# Part 6: Jenkins and CI/CD

---

## Q26. What is CI/CD?

**🔑 Keywords:** Continuous Integration, Continuous Delivery, Continuous Deployment, automation, build, test, deploy, pipeline, feedback loop

**Beginner-Friendly Answer:**
CI/CD is about automating the boring, repetitive parts of software delivery. Continuous Integration (CI) means every time someone pushes code, it automatically gets built and tested — so bugs are caught early. Continuous Delivery (CD) means the software is always ready to be released. Continuous Deployment goes one step further — qualifying changes are automatically deployed to production. In our project, Jenkins handles the CI part (build, test, scan) and Argo CD handles the CD part (deploy to Kubernetes).

**Detailed Answer:**
CI/CD stands for Continuous Integration and Continuous Delivery or Deployment.

Continuous Integration automates activities such as building and testing code changes.

Continuous Delivery keeps software ready for release, while Continuous Deployment automatically releases qualifying changes to the target environment.

In our architecture, Jenkins handles CI activities, and Argo CD manages the GitOps-based deployment process.

### Cross-question: Why use Jenkins instead of GitHub Actions?

We selected Jenkins because we want to learn and operate a dedicated CI server in our architecture.

Jenkins provides pipeline automation, plugin integrations, credentials management, and control over build agents.

GitHub Actions could also implement the CI workflow. Neither tool is universally better; the choice depends on operational requirements and existing infrastructure.

---

## Q27. What happens when a developer pushes code to GitHub?

**🔑 Keywords:** webhook trigger, checkout, build, test, Trivy scan, GHCR push, image update, Argo CD sync, automated pipeline

**Beginner-Friendly Answer:**
Here's the chain of events: Developer pushes code → GitHub notifies Jenkins (via webhook) → Jenkins pulls the latest code → runs tests → builds a Docker image → scans it with Trivy for vulnerabilities → pushes the clean image to GHCR → the desired deployment config is updated → Argo CD notices the change → deploys the new version to Kubernetes. It's a fully automated assembly line from code to running application.

**Detailed Answer:**
The source code is pushed to the repository, and a configured webhook or polling mechanism triggers Jenkins.

Jenkins checks out the code, runs the relevant tests, builds the application, and performs image scanning with Trivy.

If the pipeline succeeds, Jenkins publishes the container image to GHCR.

The deployment workflow then updates the desired image reference in Git or uses a configured image-update mechanism. Argo CD detects the desired-state change and reconciles the application in Kubernetes.

The exact trigger and image-update implementation depend on the Jenkins pipeline and Argo CD Image Updater configuration.

### Cross-question: Why do we need both Jenkins and Argo CD?

They have different responsibilities.

Jenkins performs CI activities such as building, testing, scanning, and publishing images.

Argo CD continuously compares the desired Kubernetes state in Git with the state in the cluster and reconciles differences.

Keeping these responsibilities separate makes the workflow easier to reason about.

---

## Q28. What is a Jenkins Pipeline?

**🔑 Keywords:** Jenkinsfile, stages, declarative pipeline, scripted pipeline, version-controlled, reproducible builds, agent

**Beginner-Friendly Answer:**
A Jenkins Pipeline is a script that defines the entire build-and-deploy process as code. Think of it as an instruction manual with chapters (stages): Stage 1 — checkout code, Stage 2 — install dependencies, Stage 3 — run tests, Stage 4 — build image, Stage 5 — scan with Trivy, Stage 6 — push to GHCR. This script lives in a file called Jenkinsfile alongside your application code, so it's version-controlled and reviewable.

**Detailed Answer:**
A Jenkins Pipeline defines a sequence of automated build and delivery stages.

A typical pipeline for our project could contain:
1. Checkout source code.
2. Install dependencies.
3. Run tests.
4. Build the container image.
5. Scan the image using Trivy.
6. Authenticate to GHCR.
7. Push the versioned image.

The pipeline can be defined in a Jenkinsfile and stored alongside the application source code.

### Cross-question: Why use a Jenkinsfile?

A Jenkinsfile allows the pipeline definition to be version-controlled with the application.

This makes pipeline changes reviewable and repeatable.

---

## Q29. What is Trivy?

**🔑 Keywords:** security scanner, vulnerability detection, container image scanning, severity threshold, CVE, supply chain security

**Beginner-Friendly Answer:**
Trivy is like a security guard that checks your Docker images for known vulnerabilities before you ship them. It scans the image's operating system packages and application dependencies against a database of known security issues (CVEs). If it finds something critical, it can fail the pipeline so you don't accidentally deploy a vulnerable image. It's an essential part of supply chain security.

**Detailed Answer:**
Trivy is a security scanner that can detect vulnerabilities and other security issues in supported targets, including container images and source repositories.

In our pipeline, it can scan an image before Jenkins publishes it to GHCR.

### Cross-question: What happens if Trivy finds a critical vulnerability?

The pipeline can fail based on a configured severity threshold and vulnerability policy.

We should investigate whether the finding affects our application or base image, update vulnerable dependencies where possible, and rebuild and rescan.

A scanner is only useful if the pipeline has clear policies for handling its findings.

---

## Q30. What is GHCR?

**🔑 Keywords:** GitHub Container Registry, image storage, versioned tags, image distribution, docker pull, authentication, OCI

**Beginner-Friendly Answer:**
GHCR (GitHub Container Registry) is like a warehouse where you store your Docker images. After Jenkins builds and scans an image, it pushes it to GHCR with a version tag (like `order-service:1.2.0`). When Kubernetes needs to deploy the app, it pulls the image from GHCR. It's integrated with GitHub, making it easy to manage access and link images to your repositories.

**Detailed Answer:**
GHCR stands for GitHub Container Registry.

It stores container images associated with GitHub.

In our architecture, Jenkins publishes versioned service images to GHCR, and Kubernetes pulls those images when deploying the application.

### Cross-question: Why not store the images directly in Git?

Git is designed primarily for source code and version-controlled files. A container registry is designed to store and distribute container images.

Using GHCR separates application source control from the distribution of built artifacts.

### Cross-question: Why use image tags?

Tags identify particular image versions.

For example:

```
ghcr.io/your-account/ecommerce/order-service:1.2.0
```

A versioned tag helps identify which build is deployed.

For stronger supply-chain integrity, production systems can also pin images by digest rather than relying solely on mutable tags such as `latest`.
