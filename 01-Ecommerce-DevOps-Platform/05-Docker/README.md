# Part 5: Docker and Containerization

---

## Q22. What is Docker?

**🔑 Keywords:** containerization, container image, runtime dependencies, consistent environment, isolated, lightweight, Dockerfile

**Beginner-Friendly Answer:**
Docker is like a shipping container for software. Just like a shipping container holds goods and can be transported anywhere, a Docker container holds your application and everything it needs to run (code, libraries, settings). It runs the same way on your laptop, your teammate's machine, or a cloud server. In our project, each microservice (User, Product, Order, Storage, Frontend) is packaged into its own Docker container.

**Detailed Answer:**
Docker is a platform used to build and run container images.

A container packages an application and its runtime dependencies so it can run in a consistent environment.

In our project, we created separate container images for the frontend and the User, Product, Order, and Storage services.

### Cross-question: Why did you containerize each microservice separately?

Each service has different dependencies and can be built, deployed, and scaled independently.

For example, the Go Product Service does not need the Java runtime required by the Order Service.

Separate images also make it easier to update a service without rebuilding every other service.

---

## Q23. What is the difference between an image and a container?

**🔑 Keywords:** image (template/blueprint), container (running instance), docker build, docker run, multiple containers from one image

**Beginner-Friendly Answer:**
An image is like a recipe — it has all the instructions and ingredients. A container is the actual dish you cook from that recipe. You can cook many dishes (containers) from the same recipe (image). Each dish is independent — eating one doesn't affect the others. Use `docker build` to create the recipe, and `docker run` to cook a dish from it.

**Detailed Answer:**
An image is a packaged template containing the application and its required filesystem layers.

A container is a running or stopped instance created from an image.

For example:

```bash
docker build -t ecommerce/product-service:1.0 .
```

builds an image.

```bash
docker run ecommerce/product-service:1.0
```

creates and starts a container from that image.

### Cross-question: Can multiple containers run from the same image?

Yes. Multiple containers can use the same image while maintaining separate runtime state.

This is useful when running multiple replicas of a stateless service.

---

## Q24. What is a Dockerfile?

**🔑 Keywords:** build instructions, FROM, COPY, RUN, CMD, EXPOSE, multi-stage build, base image, layers

**Beginner-Friendly Answer:**
A Dockerfile is like a step-by-step recipe for building a Docker image. It says: "Start with this base (FROM), copy these files (COPY), install these dependencies (RUN), and when the container starts, run this command (CMD)." Our frontend uses a cool trick called multi-stage build — first stage uses Node.js to build the React app, second stage uses a lightweight Nginx image to serve it. This keeps the final image small and clean.

**Detailed Answer:**
A Dockerfile contains instructions for building an image.

It specifies the base image, files to copy, dependencies to install, build commands, and the command used to start the application.

Our frontend uses a multi-stage build: Node.js builds the React application, and an Nginx image serves the resulting static files.

This avoids including the full Node.js build environment in the final runtime image.

### Cross-question: Why use multi-stage builds?

They can reduce image size and remove build-only dependencies from the runtime image.

They also make it easier to separate build-time and runtime requirements.

### Cross-question: What is a container registry?

A container registry stores and distributes container images.

Our selected registry is GHCR, which allows Jenkins to publish versioned images for the deployment workflow.

---

## Q25. What is the difference between CMD and ENTRYPOINT?

**🔑 Keywords:** ENTRYPOINT (main executable), CMD (default arguments), override behavior, combined usage, PID 1

**Beginner-Friendly Answer:**
Think of ENTRYPOINT as the main program that always runs, and CMD as the default options you pass to it. For example, if ENTRYPOINT is `python` and CMD is `app.py`, the container runs `python app.py`. You can override CMD easily when starting a container, but ENTRYPOINT stays fixed unless you specifically override it. They work best when used together.

**Detailed Answer:**
ENTRYPOINT defines the main executable for a container. CMD provides default arguments or a default command, depending on how the Dockerfile is written.

They can be used together.

For example, an application image might define an executable with ENTRYPOINT and provide its default arguments using CMD.

### Cross-question: What happens if a container's main process exits?

The container's main process has terminated, so the container normally stops. In Kubernetes, a controller may create or restart containers according to the workload's desired state and restart policy.

This is why a container should run the actual application process rather than perform a task and exit unexpectedly.
