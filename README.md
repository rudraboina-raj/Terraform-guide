# Terraform-guide

What is Terraform?
-----------------------------------------------------------

Terraform is an open-source Infrastructure as Code (IaC) tool developed by HashiCorp.
It allows you to define, provision, and manage infrastructure (servers, databases, networking, etc.) using code — making infrastructure repeatable, consistent, and version-controlled.

⚙️ Infrastructure as Code (IaC)
-----------------------------------------------------------------------------------

Infrastructure as Code (IaC) is the practice of managing infrastructure (like servers, databases, and networks) through code rather than manual processes.

Examples:

AWS → CloudFormation (CFT)

Azure → Resource Manager (ARM templates)

OpenStack → Heat templates

Terraform → Multi-cloud IaC using provider APIs.

🧩 API as Code
-----------------------------------------------------------------------

API as Code is a concept that allows automation of cloud resources across multiple providers (AWS, Azure, GCP, etc.) using their APIs.

Terraform uses this principle to interact with cloud providers via APIs.

API → Application Programming Interface

Terraform acts as a universal API manager for multiple providers.

🪄 Advantages of Terraform
--------------------------------------------------------------------------

* Manage any infrastructure (multi-cloud support)

* Track your infrastructure state

* Automate infrastructure changes

* Collaborate effectively via version control

* Standardize configurations across environments.

🔁 Lifecycle of Terraform
---------------------------------------------------------------

Write Terraform configuration files

Define resources using .tf files.

Plan ;
Use terraform plan to preview changes Terraform will make to your infrastructure.

Apply ;
Use terraform apply to provision your infrastructure and update the state file.

🧠 Terraform Modules
-------------------------------------------------------------------------

* Modules help you create reusable infrastructure components.

Definition:
A module is a way of writing reusable, maintainable, and shareable code blocks (Terraform scripts).

Example:
You can write a Terraform file to create S3 and DynamoDB resources, and use it as a module inside other Terraform scripts.

🗂️ Terraform State Files
---------------------------------------------------------------------------------

Terraform keeps track of resources using a state file (terraform.tfstate), which represents the real-world infrastructure.

Best Practices:
---------------------------------------------------------------------------

* Isolate and organize state files to reduce the blast radius.

* Use different state files for environments like dev, prod, and staging.

* Store state files in remote backends such as Amazon S3 for safety.

* Use DynamoDB for state file locking to prevent concurrent updates.

Example:
If someone modifies the prod environment accidentally, separate state files ensure the dev environment remains unaffected.

⚠️ Problems with Terraform
------------------------------------------------------------------

* State file is a single source of truth.
If it gets corrupted or lost, you may lose infrastructure tracking.

* Manual changes made directly in the cloud console are not detected or auto-corrected by Terraform.

* Not GitOps friendly – doesn’t integrate easily with GitOps tools like FluxCD or ArgoCD.

* Complexity – Large Terraform projects can become complex and difficult to manage.

* Overlap with Configuration Management Tools – Terraform is sometimes used for configuration management, but it’s primarily for infrastructure provisioning, not configuration.

🧰 Ideal Terraform Setup
-------------------------------------------------------------------------

 Here’s a common setup for using Terraform in an organization:

Workflow:

* Developers/DevOps Engineers write Terraform scripts.

* They store the scripts in a GitHub repository.

* Jenkins (or any CI/CD tool) pulls the Terraform scripts and runs them.

* Terraform provisions resources on AWS.

* Terraform state files are stored in S3 (remote backend).

* DynamoDB is used for state file locking to avoid parallel runs.

Diagram:
-------------------------------------------------------------------

Users --> Jenkins --> Terraform (GitHub) --> AWS
                    |
                    |-- stores state file in --> S3
                    |
                    |-- DynamoDB --> used for locking Terraform state.


🧩 Terraform in Real Organizations (Example Use Case)
---------------------------------------------------------------------------------

* DevOps engineers write and maintain Terraform scripts.

* Scripts are stored in GitHub repositories.

* Other users (e.g., developers, support engineers) don’t have direct AWS access.

* They trigger Jenkins pipelines to execute Terraform scripts indirectly.

* Jenkins pulls the Terraform files from GitHub and provisions the AWS resources.

* The users then get output or status of their created resources.


📦 Product Files in GitHub
----------------------------------------------------------------------------

The Terraform state file should not be stored directly in GitHub.

Instead, it should go into a remote backend (like AWS S3) integrated with a locking solution (like DynamoDB).

Example configuration:
---------------------------------------------------------

terraform {
  backend "s3" {
    bucket         = "terraform-state-bucket"
    key            = "env/prod/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
