# Brett Moan – Personal Showcase

Hello! This page is to demonstate my technical skills to anyone interested (co-workers, friends, and potential employers).

Below is a summary of my github repos that are in a "demo" state that i beleive help demonstart my architectual and coding skills to prospective employers.  The intent is to showcase my personal and organizational infrastructure, managed as code and designed for best practices, automation, and clarity.

For a full resume up-to-date resume of my work in the professional area please visit: 

### [**My full resume**](https://registry.jsonresume.org/brettmoan)


### My first Python backend API

Flask applicaiton made as part of an interview process. Made this 5 years ago before I knew about `fastapi`. 

- https://github.com/BrettMoan/interview

### Passion project (Out of date, due to family reponsiblities)
as a passion project I started working on porting some simple game automation to python. It was an effort to build `bots` for old school runescape. The goals of the project was I wanted to learn about manually managing memory bindings for a C library. I also wanted an exercise in porting a library from one langauage (pascal) to another (python). Eventually I want to get into understanding computer vision, but for now this project is on hold due to the more important work of being a dad to young children
 - [pyautoeios](https://github.com/BrettMoan/pyautoeios)
 - [PyReflect](https://github.com/BrettMoan/PyReflect)
 - [pyscreeze](https://github.com/BrettMoan/pyscreeze) (forked to support images with transparency) (outdated PR [here](https://github.com/asweigart/pyscreeze/pull/57) )

### Poly-repo Cloud Organizational Example (example of an opinionated ideal state for a corporate code base)

This serves as an opinionated example of how to practice IaaC for cloud, while seperating repos and responsibilies to respective teams. The idea being that individual teams  would be the code owners of certain repositories, and other teams could do PRs to the repos as needed. CODEOWNERS files would be implemented to control who has access to what, restricting the access for both `tags` and approvals for merges to the trunk (ie `main`) branch. Thus implementing SOC2 controls for approvals/automation, while also following best practices of `inner-source` within an organiation

## Repository Roles & Relationships

the 6 repos: 
- [**github-org-infra**](https://github.com/BrettMoan/github-org-infra): (owned by an infra & Security team) Central repo for managing the GitHub organization, repositories, teams, and settings via Terraform. Provisions and configures all other repos, controls who has access to PR.
- [**iam-oidc-permissions**](https://github.com/BrettMoan/iam-oidc-permissions): (owned by Security/IAM team) Manages AWS IAM roles, policies, and OIDC trust for CI/CD pipelines. Secures CI/CD and cloud access for all repos, this prevents feature branches from accessing unauthorized 
- [**network-config**](https://github.com/BrettMoan/network-config): (owned by infra or Network Security team) DNS, subdomains, and routing infrastructure for the organization. Supports routing for networking settings.
- [**frontend-app**](https://github.com/BrettMoan/frontend-app): (owned by an application/frontend team) Infrastructure and deployment for the static front-end application. Interacts with `backend-api`.
- [**backend-api**](https://github.com/BrettMoan/backend-api): (owned by application/backend team) Serverless backend API infrastructure, consumed by the frontend and other services. Provides data to `reporting-analytics`.
- [**reporting-analytics**](https://github.com/BrettMoan/reporting-analytics): (owned by applciation/data team) Infrastructure for reporting and analytics, consuming data from backend.


