Project goal
- Learn production-style DevOps, GitOps, and data platform engineering by building a private football scouting platform on Talos Kubernetes.

Non-goals
- No public internet exposure
- No multi-node HA assumptions
- No GPU/video workloads in phase 1

Threat model
- Only approved Tailscale identities may access the platform
- No public ingress, Funnel, NodePort, or non-Tailscale LoadBalancer exposure
- Minimize blast radius with namespace isolation, RBAC, and NetworkPolicies
- Assume failures: bad deploy, expired secret, SSD loss, node rebuild

Responsibility split
- Ansible: cluster bootstrap and one-time prerequisites
- Flux: ongoing Kubernetes desired state

Namespaces
- flux-system -> for the git based deployment & cluster updates
- tailscale -> connectivity & network security 
- observability - > monitoring
- data -> database, object storage & BI level tables (dbt)
- serving -> the services that make up the data handling from cronjob ingestion to dashboard
- pipelines -> the data pipelines that make the data useable for the dashboard & AI model training

Ansible handles:
- initial cluster bootstrap so all configs are into place for storage, backup, observability etc
- if the config & install is part of the bare metal infrastructure, Ansible owns it

FluxCD
- runs a git focused deployment strategy 
- if a resource lives inside a cluster it is owned by FluxCD

Exposure policy
- Default service type is ClusterIP
- Only selected services may be exposed through the Tailscale operator
- Traefik is internal-only if used

Phase 1 scope
- Flux bootstrap
- SOPS + age
- namespace and network baseline
- Tailscale operator
- observability stack
- Postgres/Timescale and MinIO
- first scraping/ETL CronJobs
- simple internal API/dashboard

Reliability strategy
- Backups and restore drills over HA
- etcd snapshots
- database backups with PITR target later
- object storage lifecycle and backup

Secrets strategy
- SOPS + age
- no plaintext secrets in Git
- least-privilege secret access per namespace

bootstrap sequence
1. talos cluster comes up based on the controlplane.yaml + check if talosctl, flux & kubectl commands are working correctly
2. k8s gets up and running with no CNI
3. ansible installs cilium
4. cluster networking is being setup
5. fluxs is bootstrapped
6. flux puts the clusters/hom into place
7. tailscale operator is being installed
