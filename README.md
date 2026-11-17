# DevOps for Developers

A free, structured, zero-to-hero DevOps curriculum — written to be read either right here on GitHub, or on the companion website with full English + Bangla (বাংলা) support, diagrams, and embedded videos where a topic needs one.

**🌐 Read with diagrams, video, and a Bangla toggle:** **[learn.bazardev.com](https://learn.bazardev.com)**

---

## How this repo works

Every chapter is one plain Markdown file — fully readable right here on GitHub, with working tables, code blocks, and diagrams (as SVGs hosted alongside each chapter). The [website](https://learn.bazardev.com) renders the exact same files, plus:

- A **বাংলা (Bangla)** translation of every chapter, one click away
- Embedded video walkthroughs on topics complex enough to deserve one
- A built-in table of contents, chapter navigation, and search-friendly structure

Pick whichever you prefer — the content is identical either way.

## Learning path

Take the courses **in this order**: each one assumes the ones before it, so nothing asks you to know something you have not met yet.

| Step | Course | You need first | Why it sits here |
|---|---|---|---|
| 1 | [Linux & Shell Basics](02-linux-shell) | nothing | The terminal, files, permissions, `sudo`, SSH: everything else runs on it |
| 2 | [Git & GitHub (Advanced)](03-git-github) | Linux basics | Every later course keeps its work in Git |
| 3 | [Docker](01-docker) | Linux, Git | Packaging an app so it runs the same everywhere |
| 4 | [CI/CD (GitHub Actions)](04-cicd) | Git, Docker | Automatically testing and shipping what you built |
| 5 | [Cloud (AWS / VPS Basics)](06-cloud) | Linux, CI/CD | Running it on a real server, with a domain and HTTPS |
| 6 | [Monitoring & Logging](07-monitoring) | Linux, Cloud | Knowing when the server is unhealthy |
| 7 | [Infrastructure as Code & Ansible](08-iac) | Linux, Git, Cloud | Turning the server setup into repeatable code |
| 8 | [Terraform](09-terraform) | IaC, Docker | Creating the cloud itself from code |
| 9 | [Kubernetes](05-kubernetes) | Docker, Git, CI/CD | Running many containers across many servers (an advanced, optional step) |

The folder numbers (`01-docker`, `02-linux-shell`, ...) are kept as they are so that links never break; **this table is the order that counts.**

## Courses

### ✅ [Linux & Shell Basics](02-linux-shell)
*The language you use to talk to a server.*

| # | Chapter |
|---|---|
| 1 | [Why Linux, and finding your way around](02-linux-shell/01-finding-your-way-around.md) |
| 2 | [Working with files and directories](02-linux-shell/02-files-and-directories.md) |
| 3 | [Permissions and ownership](02-linux-shell/03-permissions-and-ownership.md) |
| 4 | [Users, groups, and sudo](02-linux-shell/04-users-groups-and-sudo.md) |
| 5 | [Processes and services (systemd)](02-linux-shell/05-processes-and-services.md) |
| 6 | [Package management (apt)](02-linux-shell/06-package-management.md) |
| 7 | [Pipes, redirection, and text tools](02-linux-shell/07-pipes-redirection-text-tools.md) |
| 8 | [SSH and remote access](02-linux-shell/08-ssh-and-remote-access.md) |

### ✅ [Git & GitHub (Advanced)](03-git-github)
*Version control for working in a team, deep dive.*

| # | Chapter |
|---|---|
| 1 | [Branching strategies for teams](03-git-github/01-branching-strategies.md) |
| 2 | [Merge vs rebase](03-git-github/02-merge-vs-rebase.md) |
| 3 | [Resolving merge conflicts](03-git-github/03-resolving-merge-conflicts.md) |
| 4 | [Interactive rebase: cleaning up history](03-git-github/04-interactive-rebase.md) |
| 5 | [Stash, cherry-pick, and reflog](03-git-github/05-stash-cherry-pick-reflog.md) |
| 6 | [Pull requests and code review on GitHub](03-git-github/06-pull-requests-and-code-review.md) |
| 7 | [Undoing mistakes: reset vs revert](03-git-github/07-reset-vs-revert.md) |
| 8 | [GitHub power features](03-git-github/08-github-power-features.md) |

### ✅ [Docker](01-docker) — zero to hero
*What containers are, why they matter, and Dockerizing a real app, start to finish.*

| # | Chapter |
|---|---|
| 1 | [What is Docker, and why does it matter?](01-docker/01-what-is-docker.md) |
| 2 | [Install Docker + your first container](01-docker/02-install-and-first-container.md) |
| 3 | [Images vs Containers](01-docker/03-images-vs-containers.md) |
| 4 | [Dockerfile — write your own image](01-docker/04-dockerfile.md) |
| 5 | [Volumes & Networking](01-docker/05-volumes-and-networking.md) |
| 6 | [Docker Compose — multi-container apps](01-docker/06-docker-compose.md) |
| 7 | [Best practices + debugging](01-docker/07-best-practices-debugging.md) |
| 8 | [Real project: Dockerize a Laravel app](01-docker/08-real-project-laravel-dockerize.md) |

### ✅ [CI/CD (GitHub Actions)](04-cicd)
*Push code, get automatic tests and deploys.*

| # | Chapter |
|---|---|
| 1 | [What is CI/CD, and why does it matter?](04-cicd/01-what-is-cicd.md) |
| 2 | [GitHub Actions basics: workflows, jobs, steps](04-cicd/02-github-actions-basics.md) |
| 3 | [Triggers: events, branches, paths, schedules](04-cicd/03-triggers-and-events.md) |
| 4 | [Environment variables, secrets, and matrix builds](04-cicd/04-env-vars-secrets-matrix.md) |
| 5 | [Caching and artifacts](04-cicd/05-caching-and-artifacts.md) |
| 6 | [Automated testing in CI](04-cicd/06-automated-testing-in-ci.md) |
| 7 | [Continuous deployment: shipping automatically](04-cicd/07-continuous-deployment.md) |
| 8 | [Real project: a full pipeline for a Dockerized Laravel app](04-cicd/08-real-project-laravel-pipeline.md) |

### ✅ [Cloud (AWS / VPS Basics)](06-cloud)
*Put your app on a real server, with a domain and HTTPS.*

| # | Chapter |
|---|---|
| 1 | [What is the cloud? VPS vs AWS vs shared hosting](06-cloud/01-what-is-the-cloud.md) |
| 2 | [Your first VPS: SSH keys, a safe start, and a firewall](06-cloud/02-your-first-vps.md) |
| 3 | [Domains and DNS](06-cloud/03-domains-and-dns.md) |
| 4 | [A web server with HTTPS](06-cloud/04-web-server-and-https.md) |
| 5 | [AWS core concepts: accounts, regions, and IAM](06-cloud/05-aws-core-concepts.md) |
| 6 | [AWS EC2, S3, and RDS](06-cloud/06-ec2-s3-and-rds.md) |
| 7 | [Networking, security, backups, and costs](06-cloud/07-networking-security-and-costs.md) |
| 8 | [Real project: a Laravel app in production](06-cloud/08-real-project-laravel-in-production.md) |

### ✅ [Monitoring & Logging](07-monitoring)
*Knowing what is happening in production: metrics, logs, dashboards and alerts.*

| # | Chapter |
|---|---|
| 1 | [Why Monitor? Metrics, Logs, Alerts, and the Four Golden Signals](07-monitoring/01-why-monitor-and-what-to-watch.md) |
| 2 | [Linux Server Health: CPU, Memory, Disk, and the Logs](07-monitoring/02-linux-server-health.md) |
| 3 | [Application Logs: Levels, Structure, and Rotation](07-monitoring/03-application-logs.md) |
| 4 | [Metrics and Prometheus](07-monitoring/04-metrics-and-prometheus.md) |
| 5 | [Grafana Dashboards](07-monitoring/05-grafana-dashboards.md) |
| 6 | [Alerting That Helps Instead of Annoys](07-monitoring/06-alerting.md) |
| 7 | [Central Logging, Uptime Checks, and Error Tracking](07-monitoring/07-central-logging-uptime-and-errors.md) |
| 8 | [Real Project: Monitoring a Laravel App](07-monitoring/08-real-project-monitoring-a-laravel-app.md) |

### ✅ [Infrastructure as Code & Ansible](08-iac)
*Writing server setup as code, and configuring servers with Ansible.*

| # | Chapter |
|---|---|
| 1 | [Why Infrastructure as Code? Terraform and Ansible in One Picture](08-iac/01-why-infrastructure-as-code.md) |
| 2 | [Ansible Basics: Inventory and Ad-hoc Commands](08-iac/02-ansible-basics.md) |
| 3 | [Ansible Playbooks: Modules, Handlers, and Idempotency](08-iac/03-ansible-playbooks.md) |
| 4 | [Ansible Roles, Templates, and Vault for Secrets](08-iac/04-ansible-roles-templates-and-vault.md) |
| 5 | [Ansible Variables, Facts, Conditions, and `register`](08-iac/05-ansible-variables-facts-and-conditions.md) |
| 6 | [Ansible Roles for a Laravel Stack: PHP, Caddy, MySQL](08-iac/06-ansible-laravel-stack-roles.md) |
| 7 | [Running Ansible Safely: Tags, Limits, Lint, and CI](08-iac/07-ansible-safety-testing-and-ci.md) |
| 8 | [Real Project: A Laravel Server, Built with Ansible](08-iac/08-real-project-a-laravel-server-with-ansible.md) |

### ✅ [Terraform](09-terraform)
*Building cloud infrastructure from code, and handing it to Ansible.*

| # | Chapter |
|---|---|
| 1 | [What Is Terraform? Install and Your First Run (No Cloud Needed)](09-terraform/01-what-is-terraform-and-your-first-run.md) |
| 2 | [HCL: Variables, Outputs, and Expressions](09-terraform/02-hcl-variables-outputs-and-expressions.md) |
| 3 | [State, and How Terraform Decides What to Change](09-terraform/03-state-and-how-terraform-thinks.md) |
| 4 | [Modules: Reusable Infrastructure](09-terraform/04-modules.md) |
| 5 | [A Real Cloud Provider (Plan Only)](09-terraform/05-a-real-cloud-provider-plan-only.md) |
| 6 | [Dependencies, `count`, `for_each`, and `lifecycle`](09-terraform/06-dependencies-count-for-each-and-lifecycle.md) |
| 7 | [Remote State, Workspaces, and CI](09-terraform/07-remote-state-workspaces-and-ci.md) |
| 8 | [Real Project: Terraform and Ansible Together](09-terraform/08-real-project-terraform-and-ansible-together.md) |

### ✅ [Kubernetes](05-kubernetes)
*Run, heal and scale containers across many servers.*

| # | Chapter |
|---|---|
| 1 | [What is Kubernetes, and why?](05-kubernetes/01-what-is-kubernetes.md) |
| 2 | [Local cluster, `kubectl`, and your first Pod](05-kubernetes/02-local-cluster-and-kubectl.md) |
| 3 | [Pods, ReplicaSets, and Deployments](05-kubernetes/03-pods-replicasets-deployments.md) |
| 4 | [Services and networking](05-kubernetes/04-services-and-networking.md) |
| 5 | [ConfigMaps and Secrets](05-kubernetes/05-configmaps-and-secrets.md) |
| 6 | [Volumes, PersistentVolumes, and StatefulSets](05-kubernetes/06-volumes-and-statefulsets.md) |
| 7 | [Ingress, health probes, and resource limits](05-kubernetes/07-ingress-probes-and-limits.md) |
| 8 | [Real project: Laravel on Kubernetes](05-kubernetes/08-real-project-laravel-on-kubernetes.md) |

## What every chapter tries to do

- Explain things **as simply as possible** — in plain English, with a Bangla version always one click away on the website.
- Build on earlier chapters explicitly, instead of teaching each topic in isolation.
- Include free resources (official docs, videos) wherever one genuinely helps.
- Include a diagram wherever a picture explains something faster than a paragraph.
- End with a short "test yourself" section so you know you actually absorbed it.

## Start here

New to all this? Start with [Linux & Shell Basics, Chapter 1](02-linux-shell/01-finding-your-way-around.md) — or browse the full, navigable version at **[learn.bazardev.com](https://learn.bazardev.com)**.
