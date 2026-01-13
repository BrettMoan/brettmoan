# Brett Moan – Poly-repo Cloud Organizational Example 

This serves as an opinionated example of how to practice IaaC for cloud, while separating repos and responsibilities to respective teams. The idea being that individual teams  would be the code owners of certain repositories, and other teams could do PRs to the repos as needed. CODEOWNERS files would be implemented to control who has access to what, restricting the access for both `tags` and approvals for merges to the trunk (ie `main`) branch. Thus implementing SOC2 controls for approvals/automation, while also following best practices of `inner-source` within an organization.

## The Design/Architecture

- [x] Repository creation is controlled as configs, if a repo needs deleted, delete the respective config, if the repo needs changes, modify the config. Committing those changes to the trunk (main) preforms CRUD operations on Repository settings. This helps create an important control, when it comes to enforcing branch protection, and merge strategy rules, as SOC2 compliance requires all code going to higher environments like a Prod or Prod-UAT, be signed off by a second developer.

- [x] Each repository connects to the (aws) cloud environment using OIDC, using ephemeral tokens. This greatly minimizes any internal threat vector, as there are no "long lived" service account keys being persisted that have access to the cloud environment. Thus limiting the ability for a rouge internal actor to execute privilege escalation attempts within the network.

- [x] Each repository serves a separate purpose, which within an org setting, would be controlled by an appropriate team, thus allowing entirely separate delegation of duties, as well as separate & granular permissions on the CI/CD Service Accounts. the CICD SA on the Repo for the app doesn't have permissions to create cloud roles, or add cloud permissions, That is restricted to a dedicated CICD SA on a security repo. That repo would then be controlled by a Security / IAM team. 

- [x] Each repository follows simple structures, and straight forward design mechanisms. there is no large mono repo, or complex build, instead if a new developer needs to create a cloud resource, they submit a PR to the cloud Infra repo, if they need security on a Service Account, they submit a PR on the cloud Permissions repo. 

- [x] Each repository uses Terraform as a primary deployment technology. A straight forward, expansive, and massively adopted IaaC technology. By using a common IaaC for all of infra, applications, and security, developers of every type can understand the overall infrastructure, and raise PRs relevant to their work. With code approvals being the primary mechanism where owning teams interact and approve changes.

- [x] Trunk based development is supported, branches can optionally deploy to a low environment (ie "dev"), but merges to trunk (main) deploy to QA, and deployments to UAT/PROD are done via cutting Tags

- [x] Security & IAM is isolated from application code, but application specific infrastructure is stored alongside the application. Thus allowing easy replication of new microservices, and all relevant infrastructure, by using any existing microservice as a template. Shared infrastructure as-in infrastructure that isn't application specific, is controlled by an infra repo. By way of example, a Docker registry or a VPC would be shared, whereas an application specific DynanmoDB Table, SQL Database, or definition of a lambda function, are stored alongside the application code.

- [x] Because OIDC is used for auth, authorization to use the cloud varies by the gitref. a feature branch doesn't have the same access to the cloud, that a main branch does, which again has different access than a `tag`. A feature branch can access dev resources, the trunk can access `qa` (env 2) resources, a `stage` tag can access `stage` resources (env 3), and a `prod` tag can access `prod` resources (env 4). Where appropriate, the PR CI/CD SA would also have _read_ access on to resource metadata accross all envionments to produce `plan` results as part of PR practices.

- [x] Terraform state files are isolated by the combination of "environment" + "github repo". this allows security to be isolated. This also allows

## Repository Roles & Relationships

the 6 repos: 
- [**github-org-infra**](https://github.com/BrettMoan/github-org-infra): (owned by an infra & Security team) Central repo for managing the GitHub organization, repositories, teams, and settings via Terraform. Provisions and configures all other repos, controls who has access to PR.
- [**iam-oidc-permissions**](https://github.com/BrettMoan/iam-oidc-permissions): (owned by Security/IAM team) Manages AWS IAM roles, policies, and OIDC trust for CI/CD pipelines. Secures CI/CD and cloud access for all repos, this prevents feature branches from accessing unauthorized 
- [**cloud-org-infra**](https://github.com/BrettMoan/cloud-org-infra): (owned by infra or Cloud/Network Security team) Cloud organization infrastructure, including DNS, subdomains, routing, and cloud resource management. Supports organizational controls and broader cloud resource configuration.
- [**frontend-app**](https://github.com/BrettMoan/frontend-app): (owned by an application/frontend team) Infrastructure and deployment for the static front-end application. Interacts with `backend-api`.
- [**backend-api**](https://github.com/BrettMoan/backend-api): (owned by application/backend team) Serverless backend API infrastructure, consumed by the frontend and other services. Provides data to `reporting-analytics`.
- [**reporting-analytics**](https://github.com/BrettMoan/reporting-analytics): (owned by application/data team) Infrastructure for reporting and analytics, consuming data from backend.

