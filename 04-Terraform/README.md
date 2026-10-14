# Part 4: Terraform

> Terraform is particularly important for your project because you are using it to define infrastructure rather than relying entirely on manual console configuration.

---

## Q15. What is Terraform?

**🔑 Keywords:** Infrastructure as Code (IaC), declarative, provider, HCL, version-controlled, repeatable, plan/apply

**Beginner-Friendly Answer:**
Terraform is like a blueprint for your cloud infrastructure. Instead of clicking through the AWS console to create servers, networks, and databases one by one, you write everything in code files. Terraform reads those files and automatically creates or updates all the resources for you. The best part? You can save these files in Git, review changes before applying them, and recreate the entire infrastructure from scratch if needed.

**Detailed Answer:**
Terraform is an Infrastructure as Code tool.

It allows us to describe infrastructure in configuration files and create, modify, or destroy supported resources through a provider.

In our project, Terraform defines infrastructure such as the VPC, subnets, Internet Gateway, NAT Gateway, security groups, Bastion, and the planned EKS environment.

Instead of manually creating every resource, we maintain a version-controlled configuration describing the intended infrastructure.

### Cross-question: Why use Terraform instead of the AWS console?

Terraform provides repeatability, version control, reviewable changes, and a consistent way to manage infrastructure.

The console is useful for inspection, learning, and troubleshooting, but manual changes can cause the actual infrastructure to differ from the Terraform configuration.

### Cross-question: Is Terraform only for AWS?

No. Terraform supports multiple providers, including providers for other cloud platforms and infrastructure systems.

Our configuration uses the AWS provider, with a local endpoint for supported Floci operations.

---

## Q16. Explain the main Terraform files in your project.

**🔑 Keywords:** versions.tf, provider.tf, variable.tf, terraform.tfvars, vpc.tf, nat.tf, bastion.tf, output.tf, dependency graph

**Beginner-Friendly Answer:**
We organize our Terraform code into separate files based on what each file does — like having separate chapters in a book. One file defines the VPC, another defines the NAT Gateway, another defines variables, and so on. Terraform doesn't care about filenames — it loads ALL .tf files in a folder as one big configuration. The filenames are just for us humans to stay organized.

**Detailed Answer:**
We organize Terraform into files based on responsibility.

| File | Purpose |
|------|---------|
| versions.tf | Declares Terraform and provider version requirements |
| provider.tf | Configures the AWS provider and Floci endpoints |
| variable.tf | Declares configurable inputs |
| terraform.tfvars | Supplies values for variables |
| vpc.tf | Defines the VPC, subnets, IGW, and routing |
| nat.tf | Defines the NAT Gateway, EIP, and private default route |
| bastion.tf | Defines the Bastion and its security group |
| route53.tf | Defines DNS resources |
| acm.tf | Defines the certificate resource |
| output.tf | Displays useful values after Terraform operations |

We use separate files to make the configuration easier to understand and maintain.

Terraform loads all .tf files in a directory as one configuration, so the filenames are organizational rather than execution-order instructions.

### Cross-question: Does Terraform execute vpc.tf before nat.tf because of the filenames?

No. Terraform builds a dependency graph from resource references and explicit dependencies.

For example, a NAT Gateway references a subnet and an Elastic IP. Those references establish dependencies.

---

## Q17. What is a Terraform provider?

**🔑 Keywords:** plugin, API interaction, AWS provider, endpoint configuration, Floci, provider block

**Beginner-Friendly Answer:**
A Terraform provider is a plugin that teaches Terraform how to talk to a specific platform. Think of it like a translator — the AWS provider translates your Terraform code into AWS API calls. Without it, Terraform wouldn't know how to create an EC2 instance or a VPC. In our lab, we configure custom endpoints so the provider talks to our local Floci emulator instead of real AWS.

**Detailed Answer:**
A provider is a plugin that allows Terraform to interact with a particular platform or service.

Our project uses the AWS provider.

Normally, it communicates with AWS endpoints. In our local lab, we configure service-specific endpoints to direct supported API requests to Floci.

### Cross-question: Why do we configure endpoints in Floci?

Because Floci is emulating AWS locally. If the provider sends a request to the real AWS endpoint, it may attempt to manage real cloud resources rather than our local environment.

The endpoint configuration must match the services and behavior supported by our Floci setup.

> **⚠️ Important:** Floci does not necessarily implement every AWS API or behave identically to real AWS. A successful local test does not prove that every part of the configuration will work unchanged in AWS.

---

## Q18. Explain terraform init, plan, apply, and destroy.

**🔑 Keywords:** init (download providers), plan (preview changes), apply (execute changes), destroy (remove resources), workflow

**Beginner-Friendly Answer:**
Think of it as a 4-step process: First, `terraform init` sets up your workspace and downloads the required plugins. Then, `terraform plan` shows you a preview of what will change (like a "what if" report). If it looks good, `terraform apply` actually creates or updates the resources. And if you want to tear everything down, `terraform destroy` removes all the managed resources. Always review the plan before applying!

**Detailed Answer:**
`terraform init` initializes the working directory, downloads required providers, and prepares the backend.

`terraform plan` calculates the changes Terraform proposes to make.

`terraform apply` applies the approved changes.

`terraform destroy` removes resources managed by the configuration, subject to dependencies and resource behavior.

A normal workflow is:

```bash
terraform init
terraform fmt -recursive
terraform validate
terraform plan
terraform apply
```

### Cross-question: Why should you review the plan before applying?

The plan reveals which resources Terraform intends to create, modify, replace, or destroy.

Reviewing it helps prevent accidental resource deletion, unexpected replacements, and costly infrastructure changes.

---

## Q19. What is Terraform state?

**🔑 Keywords:** terraform.tfstate, resource mapping, state file, S3 backend, locking, drift detection, don't delete

**Beginner-Friendly Answer:**
Terraform state is like a memory file that remembers what infrastructure Terraform has created. It maps your code (like `aws_vpc.ecommerce`) to the actual resource ID in AWS. Without this file, Terraform wouldn't know what already exists and might try to create duplicate resources. That's why you should never delete the state file carelessly, and in production, you store it in a secure remote location like S3.

**Detailed Answer:**
Terraform state records information about resources Terraform manages and helps Terraform map configuration resources to actual infrastructure.

For example, it records the relationship between an address such as `aws_vpc.ecommerce` and the corresponding VPC ID.

Terraform uses state when calculating future changes.

### Cross-question: What happens if the state file is deleted?

Terraform may lose its mapping to existing resources. Depending on the configuration and resource discovery, a subsequent plan could attempt to create resources that already exist or produce other inconsistencies.

You should not simply delete state to fix every Terraform error.

### Cross-question: Where should production Terraform state be stored?

A common AWS design uses an S3 backend with appropriate access controls, encryption, and versioning. A locking mechanism should also be configured according to the backend's supported capabilities and the Terraform version.

Our local Floci lab should be kept separate from any real AWS production state.

---

## Q20. What is a Terraform module?

**🔑 Keywords:** reusable configuration, DRY principle, inputs/outputs, root module, child module, consistency across environments

**Beginner-Friendly Answer:**
A Terraform module is like a reusable template. Instead of copy-pasting the same VPC code for development, staging, and production, you write it once as a module and call it multiple times with different settings. It's like a function in programming — you define it once and reuse it with different parameters.

**Detailed Answer:**
A module is a reusable collection of Terraform configuration.

For example, we could create a reusable VPC module that accepts CIDR ranges, Availability Zones, and subnet definitions as inputs.

The root configuration can call that module instead of duplicating all the networking resources.

### Cross-question: Why use modules?

Modules improve reuse, consistency, organization, and maintainability.

They also make it easier to standardize infrastructure across development, staging, and production environments.

---

## Q21. What is the difference between count and for_each?

**🔑 Keywords:** count (numeric index), for_each (map/set keys), resource identity, index shifting, stable keys

**Beginner-Friendly Answer:**
Both `count` and `for_each` let you create multiple copies of a resource. `count` is like numbering items 0, 1, 2, 3 — simple but if you remove item 1, everything after it shifts. `for_each` uses named keys (like "public-subnet-a", "private-subnet-b") — so removing one doesn't affect the others. Use `for_each` when each resource has a unique identity.

**Detailed Answer:**
Both allow Terraform to create multiple instances of a resource.

`count` uses a numeric index. It is useful when resources are largely interchangeable or naturally indexed.

`for_each` uses keys from a map or set. It is useful when each resource has a meaningful identity.

For example, our subnet configuration uses count to create subnets from lists of CIDR blocks.

A map-based for_each can be more maintainable when each subnet has a distinct name, CIDR, and purpose.

### Cross-question: What can go wrong when using count?

If resources are indexed by position and the list changes, Terraform may propose changes to resource instances because their indexes no longer represent the same items.

With `for_each`, stable keys often make resource identity clearer.
