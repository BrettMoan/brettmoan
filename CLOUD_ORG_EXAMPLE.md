# Brett Moan – Poly-repo Cloud Organizational Example 

This serves as an opinionated example of how to practice IaaC for cloud, while separating repos and responsibilities to respective teams. 

Individual teams  are the code owners of certain repositories, and other teams could do PRs to the repos as needed. 

CODEOWNERS files are be implemented to control who has access to what. Github supports allowing PRS, while restricting the access for both approvals for merges to the trunk (ie `main`) branch and rules regarding the creation of `tags`. This combination of controls, allows implementing SOC2 controls for approvals/automation, while also following best practices of `inner-source` within an organization.

## Cool cool, but what is the Design/Architecture?

1. Everything is done as IaaC. Everything. Terraform is a first-class language. Every developer is expected to become comfortable with some terraform code sitting in their repo, though the implementation of that terraform is kept as simple as possible. App developers aren't expected to to become "Terraform Gurus" but they are expected to understand that a "terraform plan" is a "dry-run" of what changes would happen, that a "terraform apply" makes changes, and that the platform team supports simple boiler plate terraform modules that app teams can implement using very simple terraform syntax. 

1. Repository creation is controlled as configs, if a repo needs deleted, delete the respective config, if the repo needs changes, modify the respective config. Committing those changes to the trunk (main) preforms CRUD operations on Repository settings. This helps create an important control, when it comes to enforcing branch protection, and merge strategy rules, as SOC2 compliance requires all code going to higher environments like a `staging` or `prod`, be signed off by a second developer.

1. all repository connections to the cloud (AWS) environment use OIDC, which are secure ephemeral tokens. This greatly minimizes any internal threat vector, as there are no "long lived" service account keys being persisted that have access to the cloud environment. Thus limiting the ability for a rouge internal actor to execute privilege escalation attempts within the network.

1. Each repository serves a separate purpose, which within an org setting, has PR approvals controlled by an appropriate team. This allows delegation of duties. And each repo's CI/CD Service Accounts has separate & granular permissions. For example: The CI/CD SA on the repo for the app doesn't have permissions to create cloud roles, or add cloud permissions. Those permissions are restricted to a dedicated CI/CD SA on a security repo, with PR approvals controlled by a Security / IAM team. 

1. Each repository follows simple structures, and straight forward design mechanisms. There is no large mono repo, or complex build, instead if a new developer needs to create a cloud resource, they submit a PR to the cloud Infra repo, if they need security on a Service Account, they submit a PR on the cloud Permissions repo. 

1. Each repository uses Terraform as a primary deployment technology. A straight forward, extendable, and massively adopted IaaC technology. By using this common IaaC for all infra, applications, and security alike, it allows developers of every type to understand the overall infrastructure. It empowers them to raise PRs relevant to their work. Rather than being limited to raising tickets and waiting in queues for other teams, any developer can raise a PR to the owning team for the changes they need, and the owning team can preform their code reviews, and follow simple and automated deployment strategies.

1. Trunk based development front and center in the design, for application code, branches can optionally deploy to a low environment (ie `dev`), but merges to trunk (main) deploy to `qa`. Deployments to `staging` & `prod` are done via cutting tags off of a specific GitRef of the trunk (`main`)

1. Because OIDC is used for auth, authorization to use the cloud varies by the gitref. A feature branch doesn't have the same access to the cloud, that a main branch does, which again has different access than a `tag`. A feature branch can access dev resources, the trunk can access `qa` (env 2) resources, a `stage` tag can access `stage` resources (env 3), and a `prod` tag can access `prod` resources (env 4). Where appropriate, the PR CI/CD SA would also have _read_ access on to resource metadata accross all envionments to produce `plan` results as part of PR practices.

1. While security & IAM is isolated from application code, application-specific-infrastructure is stored alongside the application. Thus allowing easy replication of new microservices, and all relevant infrastructure, by using any existing microservice as a template. Shared infrastructure (as in infrastructure that isn't application specific), is controlled by an infra repo. For example: a Docker registry or a VPC would be shared, whereas an application specific DynanmoDB Table, SQL Database, or definition of a lambda function, are stored alongside the application code.

1. Terraform state files are isolated by the combination of "environment" + "github repo". this allows security to be isolated. This also allows

## Repository Roles & Relationships

the 6 repos: 
- [**github-org-infra**](https://github.com/BrettMoan/github-org-infra): (owned by an infra & Security team) Central repo for managing the GitHub organization, repositories, teams, and settings via Terraform. Provisions and configures all other repos, controls who has access to PR.
- [**cloud-org-infra**](https://github.com/BrettMoan/cloud-org-infra): (owned by infra or Cloud/Network Security team) Cloud organization infrastructure, including DNS, subdomains, routing, and cloud resource management. Supports organizational controls and broader cloud resource configuration.
- [**backend-api**](https://github.com/BrettMoan/backend-api): (owned by application/backend team) Serverless backend API infrastructure, consumed by the frontend and other services. Provides data to `reporting-analytics`.
- TODO: [**iam-oidc-permissions**](https://github.com/BrettMoan/iam-oidc-permissions): (owned by Security/IAM team) Manages AWS IAM roles, policies, and OIDC trust for CI/CD pipelines. Secures CI/CD and cloud access for all repos.
- TODO: [**frontend-app**](https://github.com/BrettMoan/frontend-app): (owned by an application/frontend team) Infrastructure and deployment for the static front-end application. Interacts with `backend-api`.
- TODO: [**reporting-analytics**](https://github.com/BrettMoan/reporting-analytics): (owned by application/data team) Infrastructure for reporting and analytics, consuming data from backend.

