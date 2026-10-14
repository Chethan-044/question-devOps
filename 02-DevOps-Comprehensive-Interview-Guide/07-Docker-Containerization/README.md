# Section 7: Docker — Theory and Practical Interview Questions

---

## Q61. What is Docker?

**🔑 Keywords:** container platform, container runtime, image builder, process isolation, portability

**Beginner-Friendly Answer:**
Docker is a software platform that lets you package an application together with all of its libraries, system tools, and settings into a standardized container. This eliminates the classic developer complaint: *"Well, it worked on my machine!"* because the container runs identically on laptops, test servers, and AWS clouds.

**Detailed Answer:**
Docker is a platform and toolset used to build, distribute, and run applications in containers.

---

## Q62. What is a container?

**🔑 Keywords:** cgroups, namespaces, shared Linux kernel, lightweight virtualization, container vs VM

**Beginner-Friendly Answer:**
A container is a lightweight, isolated execution environment running directly on the host operating system's kernel. Unlike a Virtual Machine (VM) — which packages a whole heavy guest operating system and virtual hardware — containers share the host Linux kernel using features called `namespaces` (for process isolation) and `cgroups` (for resource limits). This makes containers start in milliseconds and use very little RAM.

**Detailed Answer:**
A container is an isolated running environment for an application and its dependencies. Containers share the host operating-system kernel rather than each running a separate full guest operating system.

### Cross-question: How is a container different from a virtual machine?

**Answer:**
A virtual machine typically runs a guest operating system on virtualized hardware. Containers generally use operating-system-level isolation and are usually lighter, though the actual resource and security characteristics depend on the workload and runtime.

---

## Q63. What is a Docker image?

**🔑 Keywords:** read-only template, filesystem layers, base image, copy-on-write, container blueprint

**Beginner-Friendly Answer:**
A Docker image is a read-only blueprint or snapshot of an application's files and dependencies. It is made of multiple layered filesystems stacked together. When you run an image, Docker adds a thin, writable layer on top — that running instance is the container.

**Detailed Answer:**
An image is an immutable template made from filesystem layers and configuration. Containers are created from images.

---

## Q64. What is a Dockerfile?

**🔑 Keywords:** build instructions, text manifest, layer caching, base image, build context

**Beginner-Friendly Answer:**
A Dockerfile is a simple text file containing step-by-step instructions that tell Docker how to build an image. It specifies where to start (`FROM`), what code to copy (`COPY`), what packages to install (`RUN`), what port to expose (`EXPOSE`), and what program to launch when started (`CMD`).

**Detailed Answer:**
A Dockerfile describes how to build an image. It can specify a base image, working directory, copied files, dependency installation, runtime configuration, and startup command.

---

## Q65. Explain FROM, WORKDIR, COPY, RUN, CMD, and ENTRYPOINT.

**🔑 Keywords:** Dockerfile directives, build time vs run time, executable declaration, Kubernetes command overrides

**Beginner-Friendly Answer:**
- **`FROM`:** Picks the starting base image (e.g. `python:3.11-slim`).
- **`WORKDIR`:** Sets the active working folder inside the container for all subsequent commands.
- **`COPY`:** Copies files from your host machine into the container image.
- **`RUN`:** Executes commands during image build time (like `pip install`).
- **`CMD`:** Default arguments/command supplied at runtime.
- **`ENTRYPOINT`:** The main fixed program that executes when the container boots up.

**Detailed Answer:**
- FROM selects a base image.
- WORKDIR sets the working directory for subsequent instructions.
- COPY copies files into the image.
- RUN executes build-time commands.
- CMD provides a default command or arguments.
- ENTRYPOINT defines the main executable or entry point.

### Cross-question: What happens when Kubernetes overrides a container's command?

**Answer:**
It can replace the image's configured entry point or default command depending on the Pod specification. An incorrect override can make the container exit immediately.

---

## Q66. What is a multi-stage Docker build?

**🔑 Keywords:** multi-stage builds, intermediate build stages, minimal runtime images, attack surface reduction, image size optimization

**Beginner-Friendly Answer:**
A multi-stage build uses multiple `FROM` lines in one Dockerfile. In the first stage (the build stage), you install heavy compilers, SDKs, and build tools (like Maven or Node.js) to compile your code. In the second stage (the runtime stage), you start fresh with a tiny, clean image (like Alpine or distroless) and copy ONLY the compiled binary into it. This shrinks image size from 1GB to 50MB and removes attack vectors.

**Detailed Answer:**
A multi-stage build uses separate build stages so that the final image can contain only the runtime components needed to run the application. This can reduce image size and avoid including build tools in the production image.

---

## Q67. What is the difference between CMD and ENTRYPOINT?

**🔑 Keywords:** executable vs default parameters, CLI override behavior, `docker run` arguments

**Beginner-Friendly Answer:**
Think of `ENTRYPOINT` as the fixed command you can't easily change (like `git`), and `CMD` as the default argument (like `status`). If you run `docker run my-image`, it executes `git status`. If you run `docker run my-image commit`, the argument `commit` overrides `CMD` while `ENTRYPOINT` (`git`) remains intact.

**Detailed Answer:**
ENTRYPOINT defines the main executable, while CMD provides a default command or default arguments. Their exact interaction depends on how they are written and how the container is launched.

---

## Q68. What are Docker volumes?

**🔑 Keywords:** persistent storage, host filesystem bypass, container lifecycle independence, database durability

**Beginner-Friendly Answer:**
By default, any data written inside a container disappears when that container is removed. A Docker **volume** is a special directory on the host machine managed by Docker that is mounted into the container. Data saved in a volume survives container restarts, updates, and deletions — making it essential for databases like PostgreSQL.

**Detailed Answer:**
Volumes provide persistent storage managed by Docker and allow data to exist independently of a particular container's writable layer.

### Cross-question: Why shouldn't important database data exist only in a container's writable layer?

**Answer:**
The data may be lost when the container is removed or replaced. Persistent storage should be configured according to the database's durability and recovery requirements.

---

## Q69. What is a multi-container application?

**🔑 Keywords:** microservices, Docker Compose, service networking, environment orchestration, local development

**Beginner-Friendly Answer:**
A multi-container application is made of several containers that work as a unified system — such as a React frontend container, an API container, and a PostgreSQL database container. Developers typically use **Docker Compose** (`docker-compose.yml`) to start, connect, and network all these containers locally with a single command (`docker compose up`).

**Detailed Answer:**
It is an application composed of multiple containers that work together, such as a frontend, backend API, and database. Docker Compose can help define and run such applications in development and suitable test environments.

---

## Q70. What is a container registry?

**🔑 Keywords:** OCI registry, image repository, image tags, immutable digest (SHA256), GHCR, Docker Hub, AWS ECR

**Beginner-Friendly Answer:**
A container registry is a cloud library where Docker images are stored, versioned, and shared. When your CI pipeline builds an image, it pushes it to a registry (like GitHub Container Registry - GHCR or Amazon ECR). When Kubernetes deploys your application, it downloads (pulls) the image from that registry. Using a Git commit SHA tag or immutable SHA256 digest ensures you always know the exact code running in production.

**Detailed Answer:**
A container registry stores and distributes container images. CI pipelines publish images to a registry, and deployment platforms pull those images when running workloads.

### Cross-question: Why tag images with a commit SHA?

**Answer:**
A commit-based tag helps identify which source revision produced an image. Immutable digest references provide an even stronger way to identify the exact image content.

---

## Q71. Why should containers run as non-root where possible?

**🔑 Keywords:** container hardening, security best practice, user namespace, privilege escalation, least privilege

**Beginner-Friendly Answer:**
By default, Docker containers run as the `root` user (UID 0). If an attacker finds a security hole in your web application and breaks out of the container, having root privileges could allow them to take over the entire host server. Running as a non-root user (e.g. `USER appuser` with UID 1000) severely limits what an attacker can touch.

**Detailed Answer:**
Running as a non-root user reduces the privileges available to an application if it is compromised. It is one layer of container hardening, alongside minimal images, patching, capability restrictions, and runtime controls.

---

## Q72. What is the difference between image scanning and runtime monitoring?

**🔑 Keywords:** static analysis vs dynamic analysis, build-time vulnerability detection (Trivy), runtime intrusion detection (Falco)

**Beginner-Friendly Answer:**
- **Image Scanning (Static):** Happens *before* deployment (in Jenkins with Trivy). It looks inside the image files for known outdated packages or vulnerabilities (CVEs).
- **Runtime Monitoring (Dynamic):** Happens *while* the container is running in production. It watches for abnormal behavior, such as a container suddenly spawning unexpected bash shells or making unauthorized network calls. Both are required for complete security.

**Detailed Answer:**
Image scanning checks an image for known vulnerabilities and other configured policy violations. Runtime monitoring observes actual workload behavior and activity after deployment. Both are useful, but neither replaces the other.
