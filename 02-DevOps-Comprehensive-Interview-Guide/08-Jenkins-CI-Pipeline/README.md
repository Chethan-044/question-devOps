# Section 8: Jenkins and CI Pipeline Theory

---

## Q73. What is Jenkins?

**🔑 Keywords:** automation server, CI/CD engine, open-source, plugins, build automation, master-agent architecture

**Beginner-Friendly Answer:**
Jenkins is a popular open-source automation server. It listens for events (like a developer pushing code to GitHub) and runs automated tasks: running unit tests, compiling binaries, building Docker images, and alerting teams if anything fails. It uses thousands of community plugins to integrate with almost any DevOps tool.

**Detailed Answer:**
Jenkins is an automation server that runs pipelines for activities such as building, testing, scanning, packaging, and releasing software.

---

## Q74. What is a Jenkins pipeline?

**🔑 Keywords:** pipeline-as-code, `Jenkinsfile`, version control, stages, steps, declarative vs scripted syntax

**Beginner-Friendly Answer:**
A Jenkins Pipeline is an automated workflow defined in code using a file named `Jenkinsfile`. Instead of configuring jobs by clicking through the Jenkins web UI, you write the workflow (Checkout → Test → Build → Scan → Push) in a text file saved in your Git repository. This makes your build pipeline version-controlled, auditable, and reproducible.

**Detailed Answer:**
A pipeline is a defined sequence of automation stages. It can be written in a Jenkinsfile, stored in version control, and executed by Jenkins.

---

## Q75. What is a Jenkins agent?

**🔑 Keywords:** controller (master) vs agent (worker), distributed builds, workload isolation, ephemeral build nodes

**Beginner-Friendly Answer:**
In Jenkins, the **Controller** is the brain that schedules jobs, serves the web dashboard, and stores configs. An **Agent** is a separate worker machine (or container) that actually does the heavy lifting — downloading code, running compilers, and running tests. Using separate agents keeps your controller fast, isolated, and secure.

**Detailed Answer:**
An agent is a machine or execution environment where Jenkins runs build tasks. The controller coordinates pipeline execution and agents perform assigned work.

### Cross-question: Why might a separate agent be useful?

**Answer:**
It can isolate build workloads, provide the necessary runtime, and reduce the risk of running untrusted build steps on the controller.

---

## Q76. What is a declarative Jenkins pipeline?

**🔑 Keywords:** `pipeline {}` block, structured syntax, stages, steps, post-actions, error handling

**Beginner-Friendly Answer:**
Declarative Pipeline is the modern, easy-to-read syntax for writing Jenkinsfiles. It uses a structured hierarchy with clear sections: `pipeline`, `agent`, `stages`, and `steps`. It has built-in error handling and syntax validation, making it much less error-prone than older scripted Groovy pipelines.

**Detailed Answer:**
Declarative Pipeline is a structured syntax for defining stages, steps, agents, environment variables, conditions, and post-build behavior.

Example:

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Build stage'
            }
        }
    }
}
```

This is a small example, not a complete production pipeline.

---

## Q77. What is a webhook?

**🔑 Keywords:** HTTP POST callback, event-driven, GitHub webhook, payload verification, secret token

**Beginner-Friendly Answer:**
A webhook is an automated notification sent over HTTP. Instead of Jenkins constantly polling GitHub every 30 seconds asking "Did anyone push code?", GitHub immediately sends an HTTP POST request to Jenkins the millisecond a commit happens. Jenkins catches the message and starts the build pipeline instantly.

**Detailed Answer:**
A webhook is an HTTP request sent by one system to notify another system about an event. A source-control platform can send a webhook to trigger a Jenkins job when code changes.

### Cross-question: What if a webhook does not trigger Jenkins?

**Answer:**
I would check webhook delivery status, endpoint reachability, authentication and signature settings if used, Jenkins job configuration, and relevant logs.

---

## Q78. What is a Jenkins credential?

**🔑 Keywords:** Jenkins Credential Store, secret text, SSH keys, masking in console logs, credential binding plugin

**Beginner-Friendly Answer:**
The Jenkins Credential Store lets you securely store sensitive data (like GitHub personal access tokens, AWS keys, or registry passwords) inside Jenkins. When a pipeline runs, it uses plugins like `withCredentials` to inject the secret temporarily without ever printing the secret in the build console logs.

**Detailed Answer:**
Jenkins credentials store sensitive information such as tokens, usernames, passwords, and private keys for use by authorized jobs.

### Cross-question: Should credentials be printed in pipeline logs?

**Answer:**
No. Secrets should not be deliberately printed, and masking is not a complete security boundary. Pipeline permissions and access to logs and build agents must also be controlled.

---

## Q79. What does a CI pipeline do in this project?

**🔑 Keywords:** checkout, unit testing, docker build, Trivy scan, GHCR image push, quality gate

**Beginner-Friendly Answer:**
In our e-commerce project, the CI pipeline:
1. Clones the microservice source code from GitHub.
2. Runs unit and integration tests.
3. Builds the Docker container image.
4. Scans the image for vulnerabilities using Trivy.
5. If tests and security scans pass, pushes the tagged image to GitHub Container Registry (GHCR).

**Detailed Answer:**
It checks out application code, runs the configured validation and tests, builds the container image, performs vulnerability scanning, and publishes the image when the required checks pass.

---

## Q80. Why run tests before publishing an image?

**🔑 Keywords:** quality gate, fail-fast principle, preventing broken artifact releases, build hygiene

**Beginner-Friendly Answer:**
You should always test before publishing because once an image is pushed to a registry, downstream deployment systems (like Argo CD) could automatically pick it up and deploy it to production. Testing first acts as a quality gate that blocks defective code from ever becoming a downloadable release.

**Detailed Answer:**
Tests can detect application defects before an artifact is released. Publishing only after required validation passes reduces the chance of promoting a known-bad build.

---

## Q81. Why is Jenkins located on the Bastion Host in this architecture?

**🔑 Keywords:** architectural trade-off, administrative jump box, learning environment, attack surface, dedicated CI servers

**Beginner-Friendly Answer:**
In our hands-on learning environment, placing Jenkins on the Bastion Host let us combine remote management access and CI execution on a single EC2 machine, keeping things simple and cost-effective. However, in production, this is an anti-pattern: Jenkins should be isolated on its own server or in Kubernetes so that a compromise of the CI server doesn't compromise the Bastion host.

**Detailed Answer:**
The Bastion Host provides a controlled access point for certain administrative or automation tasks. In this architecture, Jenkins is placed there as part of the chosen setup.

### Cross-question: Is putting Jenkins on a Bastion Host always the best production design?

**Answer:**
No. It is an architectural choice with security and operational trade-offs. A production deployment should isolate Jenkins appropriately, restrict access, manage credentials carefully, and avoid exposing its interface directly to the public internet. A dedicated CI environment may be preferable depending on the threat model and workload.

---

## Q82. What happens when a pipeline stage fails?

**🔑 Keywords:** pipeline abort, `post { failure {} }`, notifications (Slack/Email), failure logs, build status

**Beginner-Friendly Answer:**
When any stage in a Jenkins pipeline encounters an error or failing test, execution stops immediately by default. Subsequent stages (like pushing the Docker image) are skipped, preventing broken artifacts from being deployed. Jenkins logs the exact failure, turns the build red, and triggers notifications to developers via Slack or email.

**Detailed Answer:**
Jenkins reports the failure and, depending on the pipeline definition, may skip later stages, execute cleanup or notification steps, and preserve logs for diagnosis.

---

## Q83. What is the difference between CI and a release process?

**🔑 Keywords:** CI (artifact production & validation) vs Release (promotion & deployment policy), environment gates

**Beginner-Friendly Answer:**
- **Continuous Integration (CI):** Validates code changes and produces a verified, tested artifact (like a Docker image).
- **Release Process:** The business and operational decision that controls *when* and *where* that artifact gets deployed (e.g. promoting from Staging to Production after manual approval or automated canary testing).

**Detailed Answer:**
CI validates and produces artifacts. A release process determines which validated artifacts and configuration are promoted into a particular environment and when they are deployed.
