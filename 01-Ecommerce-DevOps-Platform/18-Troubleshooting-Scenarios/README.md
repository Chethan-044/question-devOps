# Part 18: Your Project-Specific Troubleshooting Scenarios

> These are especially useful because you have already worked through many of these issues.

---

## Scenario 1: The Order Service cannot reach the Product Service.

**Interviewer:** *What will you check?*

**🔑 Keywords:** Pod status, Service endpoints, CoreDNS resolution, port vs targetPort, NetworkPolicy, application logs

**Beginner-Friendly Answer:**
I will check if the Product Service Pod is actually running, then check if its Kubernetes Service has healthy endpoints. Next, I'll verify that the Order Service is using the correct internal DNS URL (`http://product-service:8002`) and that the target port matches. Finally, I'll review the Order Service logs to see if the issue is a DNS lookup failure, connection refusal, or timeout.

**Detailed Answer:**
I would check whether the Product Service Pod is healthy, whether its Kubernetes Service exists, and whether the Service has ready endpoints.

Then I would verify the Order Service's configured URL, the Service port and target port, DNS resolution, and any applicable network policies.

I would also inspect the Order Service logs to see whether the error is a DNS failure, connection refusal, timeout, or application-level response.

---

## Scenario 2: The application works locally but fails in Kubernetes.

**🔑 Keywords:** localhost misconception, ConfigMap/Secret discrepancies, environment variables, DNS resolution, container ports, probes

**Beginner-Friendly Answer:**
A classic issue when moving from local docker-compose to Kubernetes is using `localhost` instead of Kubernetes Service DNS names. In Kubernetes, each Pod has its own network namespace, so `localhost` refers only to that specific Pod. For example, the Storage Service must use `DB_HOST=postgres` rather than `DB_HOST=localhost`. I would also check environment variables, ConfigMaps, Secrets, and container listening ports.

**Detailed Answer:**
I would compare the local and Kubernetes configurations.

I would check environment variables, service URLs, container ports, image versions, database credentials, ConfigMaps, Secrets, probes, and network connectivity.

One common cause is using localhost inside a container when the application should connect to another Kubernetes Service by its DNS name.

For example, the Storage Service uses `DB_HOST=postgres` in Kubernetes rather than `DB_HOST=localhost`.

---

## Scenario 3: The HPA shows unknown CPU utilization.

**🔑 Keywords:** metrics-server missing, missing resource requests, Metrics API, targetCPUUtilizationPercentage

**Beginner-Friendly Answer:**
If HPA says `TARGETS: <unknown>/50%`, it almost always means one of two things: either the Kubernetes `metrics-server` is not installed/running, or the application container does not have `resources.requests.cpu` configured in its Deployment manifest. HPA cannot calculate CPU percentage if it doesn't know what baseline request it is comparing against!

**Detailed Answer:**
I would check whether the metrics pipeline is functioning and whether the Metrics API can return Pod metrics.

I would also verify that the workload has suitable CPU resource requests, because CPU utilization targets are evaluated relative to requests.

I would inspect the HPA status and events to determine why metrics are unavailable.

---

## Scenario 4: Argo CD says an application is OutOfSync.

**🔑 Keywords:** OutOfSync, git diff, manual live edits, controller mutated fields, auto-sync, pruning risk

**Beginner-Friendly Answer:**
`OutOfSync` means the state of resources running in Kubernetes differs from the YAML files committed in Git. I would look at the Argo CD UI or run `argocd app diff` to see exactly which field is different. Did someone manually edit a replica count? Did a Kubernetes controller add default fields? I will never click "Sync" blindly without checking the diff, especially if pruning is enabled.

**Detailed Answer:**
OutOfSync means the live application state differs from the desired state in Git.

I would inspect the diff and identify the differing resources and fields.

The difference could be caused by an intentional Git change, a manual modification, a controller updating a field, or a configuration issue.

I would not blindly synchronize without first understanding the difference, especially if pruning is enabled.

---

## Scenario 5: Jenkins successfully pushes an image, but Kubernetes continues running the old version.

**🔑 Keywords:** mutable tag anti-pattern (`:latest`), imagePullPolicy: Always, Argo CD Image Updater polling, Git commit push

**Beginner-Friendly Answer:**
If Jenkins pushed an image but Kubernetes didn't update:
1. Did Jenkins use the `:latest` tag without `imagePullPolicy: Always`? (Kubernetes won't re-pull if the tag name didn't change).
2. Did Argo CD's desired state repo in Git actually get updated with the new tag?
3. Is Argo CD Image Updater configured properly and running?
4. Always use unique, versioned tags (like Git commit SHAs) so Kubernetes clearly sees that a new image exists.

**Detailed Answer:**
I would verify that the new image was pushed to the correct repository and tag.

Then I would check whether the desired deployment reference was updated and whether Argo CD detected that change.

I would also verify whether Image Updater is configured correctly and whether Kubernetes actually pulled the new image.

A mutable tag such as latest can create ambiguity if the deployment configuration does not change and the image pull behavior is not appropriate.

---

## Scenario 6: The Gateway exists, but the application cannot be accessed externally.

**🔑 Keywords:** Gateway status, AWS Load Balancer Controller logs, target group health, subnets, route tables, ACM TLS certificate

**Beginner-Friendly Answer:**
If external users can't reach the app:
1. Check `kubectl describe gateway` to see if the AWS Load Balancer Controller accepted it.
2. Check the controller logs: did it have permissions to create the AWS ALB?
3. Verify in AWS that the ALB target groups show healthy targets (Pods).
4. Verify Security Group rules allow incoming traffic on ports 80/443.
5. Check if Route 53 DNS records point to the ALB DNS name.
6. Verify the ACM SSL certificate is validated and active.

**Detailed Answer:**
I would inspect the Gateway status, its listeners, HTTPRoute attachment, backend references, and the controller logs.

Then I would check whether the expected ALB was provisioned, whether its listeners and target groups are healthy, whether security rules permit the required traffic, and whether DNS points to the correct destination.

I would also check the TLS certificate and listener configuration if HTTPS is failing.

---

## Scenario 7: Terraform wants to replace an EC2 instance unexpectedly.

**🔑 Keywords:** resource replacement, `terraform plan` analysis, forces replacement, immutable attributes, state drift

**Beginner-Friendly Answer:**
If `terraform plan` shows `-/+` (destroy and recreate) on an EC2 instance, NEVER run `terraform apply` without investigating! Certain attribute changes (like changing the AMI, instance subnet, or user_data) cannot be updated in-place and force AWS to destroy the server and build a new one. I will inspect the plan output to see which specific attribute has `(forces replacement)` and decide whether that change is truly intended.

**Detailed Answer:**
I would inspect the Terraform plan to identify the attribute causing replacement.

Some EC2 attributes require replacement rather than an in-place update. Other cases may involve differences between configuration and state.

I would not immediately approve the replacement because it could interrupt services or destroy associated resources.

I would first determine whether the change is expected and whether any data or configuration must be preserved.
