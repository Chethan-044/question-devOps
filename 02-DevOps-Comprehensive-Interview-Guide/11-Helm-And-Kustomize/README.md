# Section 11: Helm and Kustomize

---

## Q106. What is Helm?

**🔑 Keywords:** package manager, Helm charts, templating engine, release versioning, single-command install

**Beginner-Friendly Answer:**
Helm is the package manager for Kubernetes (like `apt` for Linux or `npm` for Node.js). Instead of manually managing 15 separate YAML files (Deployments, Services, Ingress, Secrets) to install a tool like Grafana, you run `helm install my-grafana grafana/grafana` and Helm packages and installs everything cleanly in one command.

**Detailed Answer:**
Helm is a package manager for Kubernetes applications. It packages resources into charts and allows configuration through values and templates.

---

## Q107. What is a Helm chart?

**🔑 Keywords:** `Chart.yaml`, `values.yaml`, `templates/`, packaged application, manifest generation

**Beginner-Friendly Answer:**
A Helm chart is a folder of files that describe a Kubernetes application. It consists of:
- `Chart.yaml`: Information about the chart (name, version, description).
- `values.yaml`: Default configuration values (replica counts, image tags, ports).
- `templates/`: Kubernetes YAML templates with placeholders (like `{{ .Values.replicaCount }}`).

**Detailed Answer:**
A chart is a collection of files that describes a Kubernetes application. It typically contains a Chart.yaml, a values.yaml, and templates that render Kubernetes manifests.

---

## Q108. What is values.yaml?

**🔑 Keywords:** configuration parameters, template variables, environment overrides (`--set` or `-f values-prod.yaml`)

**Beginner-Friendly Answer:**
`values.yaml` is the central configuration file for a Helm chart. Instead of modifying complex template files directly, you simply change the settings in `values.yaml` (e.g. `replicaCount: 3`, `image: "my-app:1.2.0"`). You can have different values files for different environments (e.g., `values-dev.yaml` and `values-prod.yaml`).

**Detailed Answer:**
It contains default configuration values used by a chart's templates. Different environments can supply different values without needing to rewrite the templates.

---

## Q109. What is a Helm release?

**🔑 Keywords:** installed chart instance, release name, release revision, upgrade history, rollback

**Beginner-Friendly Answer:**
A release is a specific running installation of a Helm chart in your cluster. If you have a chart called `wordpress`, you can install it once as release `blog-dev` and install the same chart again as release `blog-prod`. Each release has its own revision history, making rollbacks easy (`helm rollback`).

**Detailed Answer:**
A release is an installed instance of a chart. The same chart can be installed multiple times with different release names or configuration.

---

## Q110. What is the difference between Helm and Kustomize?

**🔑 Keywords:** Helm (templates & variables, chart packaging, release tracking) vs Kustomize (template-free, pure YAML, base and overlays)

**Beginner-Friendly Answer:**
- **Helm:** Uses templates and variables (`{{ .Values.name }}`). It generates YAML files on the fly and manages releases as tracked packages.
- **Kustomize:** Does NOT use templates. It uses 100% pure Kubernetes YAML. You keep a standard "base" folder and apply "overlays" (patches) on top for dev, staging, and prod. It is built right into `kubectl` (`kubectl apply -k`).

**Detailed Answer:**
Helm uses charts and templates to render configurable Kubernetes resources. Kustomize modifies or composes Kubernetes manifests through overlays and patches without requiring a template language in the same way.

### Cross-question: Can both be used in the same project?

**Answer:**
Yes. They can serve different purposes, but the team should define a clear ownership model so that resources are not managed inconsistently by multiple deployment mechanisms.

---

## Q111. What is a Kustomize overlay?

**🔑 Keywords:** environment customization, base inheritance, patches, DRY manifests, `kustomization.yaml`

**Beginner-Friendly Answer:**
In Kustomize, you keep your common configuration in a `base/` directory. An **overlay** is an environment-specific folder (e.g. `overlays/production/`) that inherits everything from `base/` and applies only the specific differences needed for that environment — such as increasing the replica count from 1 to 5 or using larger memory limits.

**Detailed Answer:**
An overlay contains environment-specific customizations built on top of a base configuration. For example, a production overlay can change replica counts or resource settings without duplicating every base manifest.

---

## Q112. How do you troubleshoot a broken Helm deployment?

**🔑 Keywords:** `helm status`, `helm history`, `helm template`, dry-run rendering, Kubernetes events, `helm rollback`

**Beginner-Friendly Answer:**
When a Helm deployment fails:
1. Run `helm status <release-name>` and `helm history <release-name>` to see the failure reason and recent revisions.
2. Run `helm template <chart-path> -f values.yaml` or `helm install --dry-run --debug` to render the exact YAML manifests locally and spot syntax or variable errors.
3. Check `kubectl get events` to see if Pods are failing to schedule or pulling bad images.
4. If production is down, quickly roll back to the previous healthy revision with `helm rollback <release-name> <revision>`.

**Detailed Answer:**
I would inspect the release status and history, render the manifests, validate the resulting Kubernetes resources, check Pod events and logs, and compare the effective values with the expected configuration.
