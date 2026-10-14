# Section 6: Terraform — Theory, Workflow, and Cross-Questions

---

## Q46. What is Infrastructure as Code (IaC)?

**🔑 Keywords:** declarative infrastructure, version control, repeatable environments, drift detection, automated provisioning

**Beginner-Friendly Answer:**
Infrastructure as Code (IaC) means writing files of code to create and manage your cloud resources (servers, databases, networks) instead of clicking buttons in the AWS console. This means your infrastructure can be version-controlled in Git, tested, shared with teammates, and recreated identically in minutes.

**Detailed Answer:**
Infrastructure as Code means defining infrastructure through files rather than creating every resource manually. This makes infrastructure easier to review, version, reproduce, and maintain.

---

## Q47. What is Terraform?

**🔑 Keywords:** HashiCorp HCL, declarative syntax, provider ecosystem, state management, desired state

**Beginner-Friendly Answer:**
Terraform is the most popular open-source IaC tool. It uses a clean language called HCL (HashiCorp Configuration Language). You describe the **desired state** ("I want a VPC with 2 public subnets and 1 EKS cluster"), and Terraform inspects your existing cloud account, figures out the difference, and issues the exact API calls to make reality match your code.

**Detailed Answer:**
Terraform is an infrastructure-as-code tool that uses declarative configuration to manage infrastructure through providers.

### Cross-question: What does declarative mean?

**Answer:**
You describe the desired result, such as a VPC with specified subnets. Terraform determines the actions required to move the managed infrastructure towards that configuration.

---

## Q48. What are the main Terraform files?

**🔑 Keywords:** `main.tf`, `variables.tf`, `outputs.tf`, `providers.tf`, `terraform.tfvars`, convention vs requirement

**Beginner-Friendly Answer:**
Terraform loads all `.tf` files in a folder together, but teams follow standard naming conventions:
- `main.tf`: Core resource and module declarations.
- `variables.tf`: Input variable definitions and defaults.
- `outputs.tf`: Values printed after applying (like VPC IDs or ALB URLs).
- `providers.tf`: Provider setup (e.g. AWS version and local endpoints).
- `terraform.tfvars`: The actual values assigned to variables for a specific environment.

**Detailed Answer:**
Terraform loads configuration from .tf files in the working directory. A clean project often separates responsibilities into files such as:
- main.tf — main resource or module configuration.
- variables.tf — input variable declarations.
- outputs.tf — values exposed after operations.
- providers.tf — provider and version configuration.
- terraform.tf — Terraform version and required provider constraints.
- terraform.tfvars — variable values for a particular environment.
These filenames are conventions; Terraform does not require these exact names.

---

## Q49. What is a Terraform provider?

**🔑 Keywords:** plugin architecture, API client, AWS provider, custom endpoints, Floci / LocalStack

**Beginner-Friendly Answer:**
A Terraform provider is a downloadable plugin that acts as a translator between Terraform and an external API (like AWS, Azure, GitHub, or Kubernetes). The AWS provider knows how to turn your HCL code into real AWS API calls. In local testing, you can point the provider to custom endpoints like Floci or LocalStack.

**Detailed Answer:**
A provider is a plugin that allows Terraform to interact with a platform or service. The AWS provider lets Terraform manage supported AWS resources.

---

## Q50. What is a Terraform module?

**🔑 Keywords:** reusable blueprints, input/output interface, DRY code, standardization across environments

**Beginner-Friendly Answer:**
A Terraform module is like a reusable function in programming. Instead of writing 150 lines of VPC, subnet, and route table code for development, staging, and production, you write a VPC module once and call it three times with different CIDR and name parameters.

**Detailed Answer:**
A module is a reusable collection of Terraform configuration. Modules help organize related resources and provide a consistent interface through input variables and outputs.

### Cross-question: Why use a VPC module instead of writing every networking resource manually?

**Answer:**
A well-maintained module can reduce repetitive code and standardize common networking patterns. However, you must still understand its inputs, outputs, defaults, and generated resources.

---

## Q51. Explain terraform init, plan, apply, and destroy.

**🔑 Keywords:** `init` (backend & plugin setup), `plan` (execution preview), `apply` (change execution), `destroy` (cleanup)

**Beginner-Friendly Answer:**
- **`terraform init`:** Prepares your folder, downloads providers, and configures the remote backend.
- **`terraform plan`:** Previews the exact changes Terraform intends to make without touching the cloud.
- **`terraform apply`:** Executes those changes against AWS after your confirmation.
- **`terraform destroy`:** Tears down and deletes every resource managed by that configuration.

**Detailed Answer:**
- `terraform init` initializes the working directory and downloads required providers and modules.
- `terraform plan` previews the changes Terraform intends to make.
- `terraform apply` executes the proposed infrastructure changes after approval as applicable.
- `terraform destroy` removes resources managed by the selected Terraform configuration and state.

### Cross-question: Should you run terraform apply without reviewing the plan?

**Answer:**
No. Review the plan to identify unexpected replacements, deletions, permission changes, and possible cost implications.

---

## Q52. What is Terraform state?

**🔑 Keywords:** `terraform.tfstate`, resource mapping, metadata caching, dependency tracking, drift detection

**Beginner-Friendly Answer:**
Terraform state is a JSON database (`terraform.tfstate`) where Terraform records the mapping between the code in your files (e.g. `aws_vpc.main`) and the real resource IDs created in AWS (e.g. `vpc-0a1b2c3d`). Without this file, Terraform has no memory of what it created previously.

**Detailed Answer:**
Terraform state maps resources managed by Terraform to real infrastructure objects and records relevant attributes. Terraform uses this state when planning changes.

---

## Q53. Why use remote state?

**🔑 Keywords:** S3 backend, DynamoDB state locking, team collaboration, state encryption, corruption prevention

**Beginner-Friendly Answer:**
If you keep the state file on your laptop, your teammates cannot work on the same infrastructure, and if your laptop dies, your state is gone. **Remote state** stores the state file in a centralized cloud bucket (like AWS S3 with versioning and encryption) and uses a database (like DynamoDB) for **state locking** so two engineers cannot run `apply` at the exact same second.

**Detailed Answer:**
Remote state allows a team or automation system to access a shared state location instead of relying on one developer's local state file. A suitable backend should provide access control, encryption, and appropriate state protection.

### Cross-question: Why is locking important?

**Answer:**
Locking helps prevent multiple operations from concurrently modifying the same state when the backend supports locking. Without suitable coordination, concurrent operations can create conflicts or inconsistent results.

---

## Q54. What is the difference between a Terraform variable and an output?

**🔑 Keywords:** input variable (parameter), output value (return value), inter-module data sharing

**Beginner-Friendly Answer:**
A **variable** is an *input* passed into Terraform (e.g. `subnet_cidr = "10.0.1.0/24"`). An **output** is a *result* printed on your screen or passed to other configurations (e.g. `output "alb_dns_name"`). It's just like function parameters and return values.

**Detailed Answer:**
A variable supplies input to a configuration. An output exposes a selected value, such as a VPC ID, subnet ID, or load balancer DNS name.

---

## Q55. What are count and for_each?

**🔑 Keywords:** `count` (numeric index list), `for_each` (map or set keys), index shifting problem, stable resource addressing

**Beginner-Friendly Answer:**
Both let you create multiple copies of a resource:
- `count` uses numbers (0, 1, 2). If you delete item #1, Terraform gets confused and recreates #2 under the wrong index.
- `for_each` uses named keys (e.g., `["public-a", "public-b"]`). Removing one does not disturb the others because each resource has a stable, permanent name.

**Detailed Answer:**
Both allow Terraform to create multiple instances of a resource or module. count uses a numeric index, while for_each uses keys from a map or set, making it useful when resources have stable identities.

### Cross-question: When can changing a count list be problematic?

**Answer:**
Removing an element from the middle of an indexed list can shift the identities associated with indexes and cause unexpected changes. Stable for_each keys can be a better fit for named resources.

---

## Q56. What is Terraform drift?

**🔑 Keywords:** out-of-band changes, manual console edits, `terraform plan -refresh-only`, reconciliation

**Beginner-Friendly Answer:**
Drift occurs when someone makes changes directly in the AWS web console (like manually changing a security group rule or deleting a subnet) without updating the Terraform code. When you next run `terraform plan`, Terraform notices that reality no longer matches your code and offers to overwrite the manual changes to restore the declared state.

**Detailed Answer:**
Drift occurs when the real infrastructure differs from the configuration or state Terraform expects. It can happen when someone changes resources manually or when external changes affect managed infrastructure.

### Cross-question: How do you investigate drift?

**Answer:**
Run a plan, inspect the proposed changes, compare the live configuration with the intended configuration, and determine whether to update the code or reconcile the infrastructure.

---

## Q57. What is the difference between Terraform and Ansible?

**🔑 Keywords:** Terraform (IaC, resource provisioning, declarative), Ansible (Configuration management, OS configuration, procedural/declarative playbooks)

**Beginner-Friendly Answer:**
- **Terraform:** Builds the *house* (creates the VPC, launches the EC2 virtual machines, provisions the databases).
- **Ansible:** Furnishes the *house* (logs into the EC2 machines over SSH to install Nginx, configure config files, and update operating system packages).

**Detailed Answer:**
Terraform primarily provisions and manages infrastructure resources. Ansible is commonly used for configuration management, software installation, and task automation on systems. Their capabilities overlap in some areas, but their primary workflows differ.

---

## Q58. What is terraform import?

**🔑 Keywords:** legacy infrastructure, state adoption, configuration alignment, import blocks

**Beginner-Friendly Answer:**
`terraform import` is used when you have resources that were created manually in the AWS console in the past, and you want to bring them under Terraform management without deleting and recreating them. It attaches the existing live resource to a Terraform state address.

**Detailed Answer:**
It associates an existing infrastructure object with a Terraform resource address and state. Importing an object does not automatically create a complete, correct configuration for it; the configuration must still match the intended management model.

---

## Q59. What is a Terraform workspace?

**🔑 Keywords:** multiple state files, environment branches, credential boundaries, directory separation vs workspaces

**Beginner-Friendly Answer:**
A Terraform workspace lets you manage multiple state files for the exact same configuration code (e.g. `default`, `staging`, `prod`). However, workspaces share the same provider credentials, so for true production environments, using separate directories or Git branches with dedicated AWS accounts is usually safer.

**Detailed Answer:**
A workspace provides a separate state instance for a configuration. Workspaces can help manage certain environment separations, but they do not automatically provide separate credentials, security boundaries, or independent infrastructure design.

---

## Q60. How would you troubleshoot a Terraform resource creation failure?

**🔑 Keywords:** error messages, IAM permissions, service quotas, duplicate names, provider debugging (`TF_LOG=DEBUG`)

**Beginner-Friendly Answer:**
When Terraform fails during `apply`:
1. Read the exact AWS API error returned in the terminal (e.g. `UnauthorizedOperation`, `VpcLimitExceeded`).
2. Verify that your AWS IAM credentials have permission to create that specific resource.
3. Check if region limits, CIDR overlaps, or naming conflicts exist.
4. If needed, set `TF_LOG=DEBUG` to view full raw HTTP API request and response payloads.

**Detailed Answer:**
I would inspect the full error message, validate the provider and resource configuration, check IAM permissions, verify region and resource prerequisites, review quotas and naming constraints, and run a new plan after fixing the root cause.
