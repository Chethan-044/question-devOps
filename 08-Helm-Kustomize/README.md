# Part 8: Helm and Kustomize

---

## Q39. What is Helm?

**🔑 Keywords:** package manager, Helm charts, values.yaml, templating, release management, versioning, rollback

**Beginner-Friendly Answer:**
Helm is the package manager for Kubernetes — think of it like `apt` for Ubuntu or `npm` for Node.js, but for whole Kubernetes applications. Instead of managing dozens of individual YAML files for Deployments, Services, and ConfigMaps, Helm bundles them together into a "Chart". You can easily customize things like replica counts or image tags simply by editing a single `values.yaml` file, and install or upgrade complex tools (like Prometheus or Grafana) with a single command.

**Detailed Answer:**
Helm is a package manager for Kubernetes.

It organizes Kubernetes resources into charts and supports configurable deployments through values.

For our project, Helm can package application components or supporting infrastructure such as monitoring tools.

### Cross-question: Why not write plain Kubernetes YAML?

Plain YAML is perfectly valid for simple deployments.

Helm becomes useful when we need reusable templates, configurable environments, chart versioning, and consistent release management.

### Cross-question: What is values.yaml?

It stores configurable values used by Helm chart templates.

For example, it may define replica counts, image repositories, tags, resource limits, or service ports.

---

## Q40. What is Kustomize?

**🔑 Keywords:** template-free, base and overlays, patches, environment customization, native kubectl integration, DRY manifests

**Beginner-Friendly Answer:**
Kustomize is another way to manage Kubernetes manifests without templates. Instead of adding placeholders like `{{ .Values.replicaCount }}` like Helm does, Kustomize uses pure Kubernetes YAML. You keep a standard "base" folder with your default configuration, and then create "overlays" (like `dev`, `staging`, `prod`) that patch only the differences (like increasing replicas or changing environment variables in production). It's built right into `kubectl` (`kubectl apply -k`).

**Detailed Answer:**
Kustomize modifies and composes Kubernetes YAML without requiring a separate template language.

It uses a base configuration and overlays to apply environment-specific changes.

For example, the same application base can be customized for development and production.

### Cross-question: What is the difference between Helm and Kustomize?

Helm primarily uses templates and values to render charts. Kustomize primarily applies patches and transformations to existing Kubernetes manifests.

Both can manage environment-specific configurations. The best choice depends on how the repository and deployment process are organized.
