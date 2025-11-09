# SRE & DevOps Curriculum - Resources & Requirements

This document provides detailed resources and requirements for each phase of the SRE & DevOps curriculum focused on Web3 infrastructure.

---

## Phase 1 – Core Cloud & IaC (Weeks 3–12)

**Goal:** Provision infrastructure like a senior infra engineer

### Week-by-Week Breakdown

#### Weeks 3–4: AWS/GCP Basics
**Topics:** VPC, EC2, IAM policies

**Resources:**
- [AWS Free Tier](https://aws.amazon.com/free/) - Free tier account setup
- [AWS VPC Documentation](https://docs.aws.amazon.com/vpc/)
- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [AWS IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [Google Cloud Free Tier](https://cloud.google.com/free) - Alternative to AWS
- [GCP VPC Documentation](https://cloud.google.com/vpc/docs)
- [GCP Compute Engine](https://cloud.google.com/compute/docs)

**Learning Objectives:**
- Create a VPC with public/private subnets
- Launch EC2 instances in different availability zones
- Configure security groups and network ACLs
- Create IAM users, roles, and policies
- Understand least privilege principle

#### Weeks 5–6: Terraform
**Topics:** Multi-env EKS cluster (dev/stg/prod)

**Resources:**
- [HashiCorp Learn - Terraform](https://learn.hashicorp.com/terraform)
- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [Terraform EKS Module](https://registry.terraform.io/modules/terraform-aws-modules/eks/aws/latest)
- [Terraform Best Practices](https://www.terraform.io/docs/cloud/guides/recommended-practices/index.html)
- [Terraform Workspaces](https://www.terraform.io/docs/state/workspaces.html)

**Learning Objectives:**
- Write Terraform modules for reusability
- Use workspaces or variables for multi-environment
- Manage Terraform state (local and remote)
- Understand Terraform lifecycle (plan, apply, destroy)
- Implement proper variable management

#### Weeks 7–8: Kubernetes
**Topics:** Deploy Nginx + HPA + PDB

**Resources:**
- [Kubernetes Official Documentation](https://kubernetes.io/docs/home/)
- [k3s Documentation](https://k3s.io/) - Lightweight K8s for local learning
- [kind Documentation](https://kind.sigs.k8s.io/) - Kubernetes in Docker
- [Kubernetes HPA Guide](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
- [Pod Disruption Budgets](https://kubernetes.io/docs/tasks/run-application/configure-pdb/)
- [Kubernetes Networking](https://kubernetes.io/docs/concepts/services-networking/)

**Learning Objectives:**
- Deploy applications using Deployments
- Configure Services (ClusterIP, NodePort, LoadBalancer)
- Set up Horizontal Pod Autoscaling (HPA)
- Create Pod Disruption Budgets (PDB)
- Understand Kubernetes resources and limits

#### Weeks 9–10: Helm + Kustomize
**Topics:** Package custom charts

**Resources:**
- [Helm Documentation](https://helm.sh/docs/)
- [Artifact Hub](https://artifacthub.io/) - Helm chart repository
- [Helm Chart Best Practices](https://helm.sh/docs/chart_best_practices/)
- [Kustomize Documentation](https://kustomize.io/)
- [Kustomize Examples](https://github.com/kubernetes-sigs/kustomize/tree/master/examples)

**Learning Objectives:**
- Create Helm charts from scratch
- Use Helm values for customization
- Package and publish charts
- Use Kustomize for configuration management
- Compare Helm vs Kustomize use cases

#### Weeks 11–12: CI/CD with GitHub Actions
**Topics:** Auto-deploy Terraform + Helm on PR merge

**Resources:**
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Terraform GitHub Actions](https://github.com/hashicorp/setup-terraform)
- [Kubernetes GitHub Actions](https://github.com/marketplace?type=actions&query=kubernetes)
- [GitHub Actions Best Practices](https://docs.github.com/en/actions/learn-github-actions/best-practices)
- [GitHub Actions Security](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions)

**Learning Objectives:**
- Create GitHub Actions workflows
- Implement Terraform plan/apply pipelines
- Automate Helm deployments
- Set up branch protection rules
- Implement proper secrets management

### Deliverable: `web3-infra-bootstrap`

**Repository Requirements:**
- Public GitHub repository named `web3-infra-bootstrap`
- Well-structured Terraform modules
- Helm charts for application deployment
- Complete CI/CD pipeline

**Must Include:**

1. **Terraform Infrastructure:**
   - [ ] VPC with public/private subnets across multiple AZs
   - [ ] EKS cluster (or managed Kubernetes) for dev/stg/prod
   - [ ] IAM roles and policies following least privilege
   - [ ] Security groups with proper rules
   - [ ] Multi-environment support (dev/stg/prod) using workspaces or variables
   - [ ] Remote state backend (S3 + DynamoDB or GCS)
   - [ ] Outputs for cluster endpoints and credentials

2. **Kubernetes Manifests:**
   - [ ] Nginx deployment with proper resource limits
   - [ ] Service configuration
   - [ ] Horizontal Pod Autoscaler (HPA)
   - [ ] Pod Disruption Budget (PDB)
   - [ ] ConfigMaps and Secrets management

3. **Helm Charts:**
   - [ ] Custom Helm chart for a sample application
   - [ ] Values files for different environments
   - [ ] Chart dependencies (if applicable)
   - [ ] Chart documentation (README.md)

4. **CI/CD Pipeline:**
   - [ ] GitHub Actions workflow for Terraform
     - Plan on PR
     - Apply on merge to main
     - Destroy on branch deletion (optional)
   - [ ] GitHub Actions workflow for Helm
     - Lint charts on PR
     - Deploy to dev on merge
     - Deploy to stg/prod with manual approval
   - [ ] Proper secret management (AWS/GCP credentials)
   - [ ] Branch protection rules

5. **Documentation:**
   - [ ] README with setup instructions
   - [ ] Architecture diagram
   - [ ] Environment-specific configuration guide
   - [ ] Troubleshooting guide

**Success Criteria:**
- ✅ One-command infrastructure provisioning
- ✅ Automated deployments via GitHub Actions
- ✅ Multi-environment support working correctly
- ✅ All infrastructure follows best practices
- ✅ Code is well-documented and maintainable

---

## Phase 2 – Blockchain Node Ops (Weeks 13–22)

**Goal:** Run production-grade nodes (like ConsenSys or Infura)

### Week-by-Week Breakdown

#### Weeks 13–14: Ethereum (Geth)
**Topics:** Sync mainnet node behind NAT

**Resources:**
- [Go Ethereum (Geth) Documentation](https://geth.ethereum.org/docs)
- [Geth Command Line Options](https://geth.ethereum.org/docs/interface/command-line-options)
- [Ethereum Node Setup Guide](https://ethereum.org/en/developers/docs/nodes-and-clients/run-a-node/)
- [Docker Documentation](https://docs.docker.com/)
- [NAT Traversal Techniques](https://en.wikipedia.org/wiki/NAT_traversal)

**Learning Objectives:**
- Install and configure Geth
- Understand sync modes (snap, full, archive)
- Configure NAT traversal (UPnP, port forwarding)
- Monitor node sync status
- Optimize node performance

#### Weeks 15–16: Polygon/Arbitrum
**Topics:** 2-node validator cluster

**Resources:**
- [Polygon Documentation](https://docs.polygon.technology/)
- [Polygon Validator Guide](https://docs.polygon.technology/docs/validate/validate/getting-started)
- [Arbitrum Documentation](https://docs.arbitrum.io/)
- [Arbitrum Nitro Node Setup](https://docs.arbitrum.io/node-running/node-operator)
- [Kubernetes StatefulSets](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)

**Learning Objectives:**
- Set up Polygon Bor + Heimdall nodes
- Configure Arbitrum Nitro nodes
- Create multi-node clusters
- Handle node synchronization
- Implement failover mechanisms

#### Weeks 17–18: RPC Load Balancing
**Topics:** Nginx + HAProxy with health checks

**Resources:**
- [Nginx Documentation](https://nginx.org/en/docs/)
- [HAProxy Documentation](http://www.haproxy.org/#docs)
- [Nginx Load Balancing](https://nginx.org/en/docs/http/load_balancing.html)
- [HAProxy Configuration Guide](http://www.haproxy.org/#docs)
- [Health Check Patterns](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

**Learning Objectives:**
- Configure Nginx as reverse proxy
- Set up HAProxy for load balancing
- Implement health checks for nodes
- Handle failover scenarios
- Monitor backend node health

#### Weeks 19–20: Monitoring
**Topics:** Prometheus + Grafana dashboards

**Resources:**
- [Prometheus Documentation](https://prometheus.io/docs/)
- [Grafana Documentation](https://grafana.com/docs/)
- [Prometheus Node Exporter](https://github.com/prometheus/node_exporter)
- [Grafana Dashboard Library](https://grafana.com/grafana/dashboards/)
- [Prometheus Query Language (PromQL)](https://prometheus.io/docs/prometheus/latest/querying/basics/)

**Learning Objectives:**
- Install and configure Prometheus
- Set up node exporters
- Create Grafana dashboards
- Monitor sync lag, peer count, CPU, memory
- Set up alerting rules

#### Weeks 21–22: Alerting
**Topics:** Alertmanager + PagerDuty webhooks

**Resources:**
- [Prometheus Alertmanager](https://prometheus.io/docs/alerting/latest/alertmanager/)
- [PagerDuty Documentation](https://developer.pagerduty.com/)
- [Alerting Best Practices](https://prometheus.io/docs/practices/alerting/)
- [Alertmanager Configuration](https://prometheus.io/docs/alerting/latest/configuration/)

**Learning Objectives:**
- Configure Alertmanager
- Create alert rules for critical metrics
- Set up PagerDuty integration
- Implement alert routing and grouping
- Test alert delivery

### Deliverable: `eth-node-ha`

**Repository Requirements:**
- Public GitHub repository named `eth-node-ha`
- Docker Compose or Kubernetes manifests
- Monitoring and alerting configuration
- Complete documentation

**Must Include:**

1. **Node Infrastructure:**
   - [ ] 2-node Ethereum (Geth) cluster
   - [ ] Docker Compose or Kubernetes StatefulSet configuration
   - [ ] Persistent volumes for blockchain data
   - [ ] Network configuration (NAT traversal)
   - [ ] Resource limits and requests

2. **High Availability:**
   - [ ] Load balancer configuration (Nginx or HAProxy)
   - [ ] Health check endpoints
   - [ ] Automatic failover mechanism
   - [ ] Node synchronization monitoring

3. **Monitoring Stack:**
   - [ ] Prometheus configuration
   - [ ] Node exporter setup
   - [ ] Custom metrics exporters (if needed)
   - [ ] Grafana dashboards for:
     - [ ] Sync lag monitoring
     - [ ] Peer count
     - [ ] CPU/Memory usage
     - [ ] Disk I/O
     - [ ] Network traffic
     - [ ] RPC request metrics

4. **Alerting:**
   - [ ] Alertmanager configuration
   - [ ] Alert rules for:
     - [ ] Sync lag > 5 minutes
     - [ ] Node down
     - [ ] High CPU/Memory usage
     - [ ] Disk space low
     - [ ] Peer count too low
   - [ ] PagerDuty webhook integration
   - [ ] Alert routing and grouping

5. **Auto-Restart:**
   - [ ] Kubernetes liveness/readiness probes
   - [ ] Restart policies
   - [ ] Health check scripts
   - [ ] Automatic recovery mechanisms

6. **Documentation:**
   - [ ] Setup and installation guide
   - [ ] Architecture diagram
   - [ ] Monitoring guide
   - [ ] Troubleshooting guide
   - [ ] Performance tuning recommendations

**Success Criteria:**
- ✅ 2-node cluster running and synchronized
- ✅ Load balancer distributing traffic correctly
- ✅ Monitoring dashboards showing all metrics
- ✅ Alerts firing correctly for critical issues
- ✅ Auto-restart working on node failures
- ✅ Production-ready configuration

---

## Phase 3 – SRE Fundamentals (Weeks 23–34)

**Goal:** Think in SLIs/SLOs, reduce MTTR

### Week-by-Week Breakdown

#### Weeks 23–24: Google SRE Book
**Topics:** Define SLOs for Ethereum nodes

**Resources:**
- [Google SRE Book](https://sre.google/books/) - Free online book
- [SRE Workbook](https://sre.google/workbook/table-of-contents/) - Practical exercises
- [SLI/SLO/SLA Guide](https://sre.google/workbook/sli-slo/)
- [Error Budgets](https://sre.google/workbook/error-budget-policy/)

**Learning Objectives:**
- Understand SLIs, SLOs, and SLAs
- Define meaningful SLIs for blockchain nodes
- Set appropriate SLO targets (e.g., 99.9% uptime)
- Calculate error budgets
- Implement SLO-based alerting

#### Weeks 25–26: Observability Triad
**Topics:** Prometheus, Loki, Tempo

**Resources:**
- [Prometheus Documentation](https://prometheus.io/docs/) (already covered)
- [Loki Documentation](https://grafana.com/docs/loki/latest/)
- [Tempo Documentation](https://grafana.com/docs/tempo/latest/)
- [Grafana Labs Observability Stack](https://grafana.com/docs/grafana/latest/setup-grafana/installation/docker/)
- [OpenTelemetry](https://opentelemetry.io/)

**Learning Objectives:**
- Set up Loki for log aggregation
- Configure Tempo for distributed tracing
- Integrate metrics, logs, and traces
- Create unified observability dashboards
- Correlate metrics, logs, and traces

#### Weeks 27–28: Chaos Engineering
**Topics:** Chaos Mesh - measure MTTR

**Resources:**
- [Chaos Mesh Documentation](https://chaos-mesh.org/docs/)
- [Chaos Engineering Principles](https://principlesofchaos.org/)
- [Chaos Mesh Examples](https://chaos-mesh.org/docs/user-guides/run-a-chaos-experiment/)
- [MTTR Metrics](https://www.atlassian.com/incident-management/kpis/mean-time-to-recovery)

**Learning Objectives:**
- Install and configure Chaos Mesh
- Design chaos experiments
- Inject pod failures, network partitions
- Measure Mean Time To Recovery (MTTR)
- Document recovery procedures

#### Weeks 29–30: Incident Response
**Topics:** Mock incidents + post-mortems

**Resources:**
- [Google SRE Book - Postmortem Culture](https://sre.google/sre-book/postmortem-culture/)
- [Atlassian Incident Response Guide](https://www.atlassian.com/incident-management/handbook)
- [PagerDuty Incident Response](https://response.pagerduty.com/)
- [Post-Mortem Templates](https://github.com/dastergon/postmortem-templates)

**Learning Objectives:**
- Run mock incident drills
- Follow incident response procedures
- Write comprehensive post-mortems
- Implement blameless culture
- Track and follow up on action items

#### Weeks 31–32: On-Call Rotation
**Topics:** Opsgenie setup

**Resources:**
- [Opsgenie Documentation](https://docs.opsgenie.com/)
- [Opsgenie Free Tier](https://www.atlassian.com/software/opsgenie/pricing)
- [On-Call Best Practices](https://www.pagerduty.com/resources/learn/on-call-best-practices/)
- [Runbook Templates](https://github.com/SiteReliabilityEngineering/runbooks)

**Learning Objectives:**
- Set up Opsgenie account
- Create on-call schedules
- Configure escalation policies
- Set up alert routing
- Create runbooks for common issues

#### Weeks 33–34: Error Budgets
**Topics:** Auto-pause deploys if budget < 10%

**Resources:**
- [Error Budget Policy](https://sre.google/workbook/error-budget-policy/)
- [Implementing Error Budgets](https://cloud.google.com/blog/products/devops-sre/implementing-error-budget-policies)
- [GitHub Actions Conditional Deployments](https://docs.github.com/en/actions/learn-github-actions/expressions)

**Learning Objectives:**
- Calculate error budgets from SLOs
- Track error budget burn rate
- Implement automated budget checks
- Pause deployments when budget is low
- Create budget dashboards

### Deliverable: `sre-playbook`

**Repository Requirements:**
- Public GitHub repository named `sre-playbook`
- SLO definitions and monitoring
- Observability dashboards
- Chaos engineering tests
- Post-mortem templates

**Must Include:**

1. **SLO Definitions:**
   - [ ] SLI definitions for Ethereum nodes:
     - [ ] Availability (uptime)
     - [ ] Latency (RPC response time)
     - [ ] Throughput (requests per second)
     - [ ] Freshness (sync lag)
   - [ ] SLO targets (e.g., 99.9% availability)
   - [ ] Error budget calculations
   - [ ] SLO monitoring dashboards

2. **Observability Dashboards:**
   - [ ] Prometheus metrics dashboards
   - [ ] Loki log aggregation setup
   - [ ] Tempo distributed tracing
   - [ ] Unified Grafana dashboards
   - [ ] Correlation between metrics/logs/traces

3. **Chaos Engineering:**
   - [ ] Chaos Mesh installation and configuration
   - [ ] Chaos experiments:
     - [ ] Pod kill (50% of pods)
     - [ ] Network partition
     - [ ] CPU stress
     - [ ] Memory stress
   - [ ] MTTR measurement and reporting
   - [ ] Recovery procedure documentation

4. **Incident Response:**
   - [ ] Incident response runbook
   - [ ] Post-mortem template
   - [ ] Example post-mortem from mock incident
   - [ ] Action items tracking system
   - [ ] Communication templates

5. **On-Call Setup:**
   - [ ] Opsgenie configuration
   - [ ] On-call rotation schedule
   - [ ] Escalation policies
   - [ ] Alert routing rules
   - [ ] Runbooks for common issues

6. **Error Budget Implementation:**
   - [ ] Error budget tracking dashboard
   - [ ] Automated budget calculation
   - [ ] GitHub Actions workflow to check budget
   - [ ] Auto-pause deployment when budget < 10%
   - [ ] Budget burn rate alerts

7. **Documentation:**
   - [ ] SRE playbook overview
   - [ ] SLO definitions and rationale
   - [ ] Observability setup guide
   - [ ] Chaos engineering procedures
   - [ ] Incident response guide
   - [ ] On-call handbook

**Success Criteria:**
- ✅ SLOs defined and monitored
- ✅ Full observability stack operational
- ✅ Chaos tests demonstrate resilience
- ✅ Post-mortem process documented
- ✅ On-call rotation active
- ✅ Error budget enforcement working

---

## Phase 4 – Web3-Specific DevOps (Weeks 35–36)

**Goal:** CI/CD for indexers

### Week-by-Week Breakdown

#### Weeks 35–36: Ponder
**Topics:** Custom indexing for EVM contracts

**Resources:**
- [Ponder Documentation](https://ponder.sh/docs)
- [Ponder GitHub](https://github.com/ponder-sh/ponder)
- [Ponder Examples](https://ponder.sh/examples)
- [Ponder Blog](https://ponder.sh/blog)
- [TypeScript Documentation](https://www.typescriptlang.org/docs/)

**Learning Objectives:**
- Set up Ponder project
- Define schema and tables
- Write event handlers in TypeScript
- Deploy Ponder indexer
- Query indexed data via API

### Deliverable: `web3-cicd-monorepo`

**Repository Requirements:**
- Public GitHub repository named `web3-cicd-monorepo`
- Monorepo structure with Ponder indexers and deployment configs
- Complete CI/CD pipeline

**Must Include:**

1. **Ponder Indexer:**
   - [ ] Schema definition (tables for indexed data)
   - [ ] Event handlers for contract events
   - [ ] Ponder configuration file
   - [ ] Deployment configuration
   - [ ] API query examples

2. **CI/CD Pipeline:**
   - [ ] GitHub Actions workflow for:
     - [ ] Tests on PR
     - [ ] Deploy on merge
   - [ ] Environment-specific configurations
   - [ ] Secret management

3. **Documentation:**
   - [ ] README with setup instructions
   - [ ] Deployment guide
   - [ ] CI/CD pipeline explanation

**Success Criteria:**
- ✅ Ponder indexer indexing contract events
- ✅ CI/CD pipeline deploying automatically

---

## Phase 5 – Senior-Level Capstone (Weeks 37–48+)

**Goal:** Build production-grade infrastructure projects

### Option A: Open-Source Validator Fleet

**Description:** Multi-chain node infrastructure with auto-scaling

**Requirements:**
- [ ] Support for multiple blockchain networks (Ethereum, Polygon, Arbitrum, etc.)
- [ ] Kubernetes-based deployment
- [ ] Auto-scaling based on load (Karpenter or Cluster Autoscaler)
- [ ] Cost optimization (spot instances, Hetzner)
- [ ] Comprehensive monitoring (Prometheus, Grafana)
- [ ] SLOs and error budgets
- [ ] Load balancing and high availability
- [ ] Complete documentation and blog post

**Resources:**
- [Karpenter Documentation](https://karpenter.sh/)
- [Kubernetes Cluster Autoscaler](https://github.com/kubernetes/autoscaler)
- [Hetzner Cloud](https://www.hetzner.com/cloud)
- [AWS Spot Instances](https://aws.amazon.com/ec2/spot/)

**Success Criteria:**
- ✅ Production-ready validator fleet
- ✅ Auto-scaling working correctly
- ✅ Cost-optimized infrastructure
- ✅ Published blog post or documentation
- ✅ Open-source repository with contributions

### Option B: L2 Rollup Infrastructure

**Description:** Arbitrum Nitro node cluster

**Requirements:**
- [ ] Arbitrum Nitro node cluster deployment
- [ ] Terraform infrastructure as code
- [ ] Helm charts for Kubernetes deployment
- [ ] Prometheus monitoring
- [ ] High availability configuration
- [ ] Cost optimization strategies
- [ ] Performance benchmarking
- [ ] Blog post: "How I reduced RPC costs 40%"

**Resources:**
- [Arbitrum Nitro Documentation](https://docs.arbitrum.io/node-running/node-operator)
- [Arbitrum Nitro GitHub](https://github.com/OffchainLabs/nitro)
- [Mirror.xyz](https://mirror.xyz/) - Publishing platform

**Success Criteria:**
- ✅ Production-ready Arbitrum Nitro cluster
- ✅ Cost reduction achieved and documented
- ✅ Published blog post on Mirror.xyz
- ✅ Open-source repository
- ✅ Performance metrics and benchmarks

### Option C: SRE Consulting

**Description:** Contribute to Kubernetes SIGs or CNCF projects

**Requirements:**
- [ ] Identify relevant Kubernetes SIG or CNCF project
- [ ] Make meaningful contributions (code, docs, bug fixes)
- [ ] Get commit access or maintainer status
- [ ] Create Prometheus exporter for EVM (example)
- [ ] Document contributions
- [ ] Build reputation in the community

**Resources:**
- [Kubernetes SIGs](https://github.com/kubernetes/community/blob/master/sig-list.md)
- [CNCF Projects](https://www.cncf.io/projects/)
- [Prometheus Exporters](https://prometheus.io/docs/instrumenting/exporters/)
- [Contributing to Kubernetes](https://www.kubernetes.dev/docs/guide/)

**Success Criteria:**
- ✅ Multiple contributions accepted
- ✅ Commit access or maintainer status
- ✅ Recognized contribution (e.g., Prometheus exporter)
- ✅ Active participation in community
- ✅ Portfolio of open-source contributions

### Option D: Safe Smart Contract Indexing with Ponder

**Description:** Index Safe smart contracts on a high-throughput chain using a self-hosted node

**Requirements:**
- [ ] Deploy and sync a self-hosted node for a high-throughput chain (Polygon, Arbitrum, Base, Optimism, etc.)
- [ ] Configure node for optimal performance (sync mode, caching, etc.)
- [ ] Set up Ponder indexer to index Safe smart contracts:
  - [ ] Safe contract addresses on chosen chain
  - [ ] Key events (SafeCreated, ExecutionSuccess, ExecutionFailure, etc.)
  - [ ] Safe module installations
  - [ ] Safe transactions and signatures
- [ ] Create comprehensive schema for Safe data:
  - [ ] Safes (address, owners, threshold, modules)
  - [ ] Transactions (nonce, to, value, data, signatures)
  - [ ] Executions (success/failure, gas used, timestamp)
  - [ ] Module installations
- [ ] Implement Ponder event handlers in TypeScript
- [ ] Set up monitoring and alerting:
  - [ ] Node sync status
  - [ ] Indexing lag
  - [ ] Ponder API health
  - [ ] Error rates
- [ ] Deploy infrastructure using Terraform:
  - [ ] Node infrastructure (EC2/VM)
  - [ ] Ponder indexer (Kubernetes or containerized)
  - [ ] Database (PostgreSQL)
  - [ ] Load balancer for Ponder API
- [ ] Implement CI/CD pipeline:
  - [ ] Automated deployments
  - [ ] Database migrations
  - [ ] Health checks
- [ ] Create API documentation and examples
- [ ] Performance optimization:
  - [ ] Handle high transaction volume
  - [ ] Optimize database queries
  - [ ] Implement caching if needed
- [ ] Complete documentation:
  - [ ] Architecture diagram
  - [ ] Setup guide
  - [ ] API documentation
  - [ ] Monitoring runbook

**Resources:**
- [Safe Documentation](https://docs.safe.global/)
- [Safe Contracts](https://github.com/safe-global/safe-contracts)
- [Ponder Documentation](https://ponder.sh/docs)
- [Polygon Node Setup](https://docs.polygon.technology/docs/validate/validate/getting-started)
- [Arbitrum Nitro Node](https://docs.arbitrum.io/node-running/node-operator)
- [Base Documentation](https://docs.base.org/)
- [Optimism Documentation](https://docs.optimism.io/)

**Success Criteria:**
- ✅ Self-hosted node synced and stable
- ✅ Ponder indexer successfully indexing Safe contracts
- ✅ Complete schema covering all Safe operations
- ✅ API serving indexed data reliably
- ✅ Monitoring and alerting operational
- ✅ Infrastructure deployed via Terraform
- ✅ CI/CD pipeline working
- ✅ Documentation complete and published
- ✅ Open-source repository with clear README
- ✅ Performance metrics showing < 1 minute indexing lag

---

## General Resources

### Learning Platforms
- [learnlinux.tv](https://learnlinux.tv) - Free Linux CLI tutorials
- [GitHub Skills Lab](https://skills.github.com) - Git and GitHub tutorials
- [HashiCorp Learn](https://learn.hashicorp.com/) - Terraform, Vault, Consul
- [Kubernetes Learning Path](https://kubernetes.io/docs/tutorials/)

### Communities
- [Kubernetes Slack](https://slack.k8s.io/)
- [CNCF Slack](https://cloud-native.slack.com/)
- [Ethereum Stack Exchange](https://ethereum.stackexchange.com/)
- [r/devops](https://www.reddit.com/r/devops/)
- [r/sre](https://www.reddit.com/r/sre/)

### Tools & Services
- **Cloud Providers:** AWS Free Tier, Google Cloud Free Tier
- **CI/CD:** GitHub Actions, GitLab CI, CircleCI
- **Monitoring:** Prometheus, Grafana, Datadog (free tier)
- **Logging:** Loki, ELK Stack
- **Tracing:** Tempo, Jaeger
- **Chaos Engineering:** Chaos Mesh, Litmus
- **On-Call:** Opsgenie (free tier), PagerDuty

### Books
- [Google SRE Book](https://sre.google/books/) - Free
- [The Phoenix Project](https://www.amazon.com/Phoenix-Project-DevOps-Helping-Business/dp/0988262592)
- [The Unicorn Project](https://www.amazon.com/Unicorn-Project-Developers-Disruption-Thriving/dp/1942788762)
- [Site Reliability Engineering Workbook](https://sre.google/workbook/table-of-contents/) - Free

---

## Progress Tracking

Create a tracking system (GitHub Projects, Notion, or simple markdown) to track:
- [ ] Completed weeks
- [ ] Deliverables status
- [ ] Learning notes
- [ ] Challenges faced and solutions
- [ ] Resources discovered

---

**Good luck on your SRE & DevOps journey! 🚀**

