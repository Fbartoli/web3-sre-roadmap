# Granular Exercises & Deliverables - SRE & DevOps Curriculum

This document provides detailed, step-by-step exercises and incremental deliverables for each phase.

---

## Phase 1 – Core Cloud & IaC (Weeks 3–12)

### Weeks 3–4: AWS/GCP Basics

#### Exercise 1.1: AWS Account Setup (Day 1)
**Objective:** Set up AWS free tier account and configure CLI

**Tasks:**
- [ ] Create AWS account
- [ ] Enable MFA on root account
- [ ] Create IAM admin user (not root)
- [ ] Install AWS CLI: `brew install awscli` (macOS) or follow [AWS CLI install guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- [ ] Configure AWS CLI: `aws configure`
- [ ] Verify access: `aws sts get-caller-identity`

**Deliverable:** Screenshot of successful `aws sts get-caller-identity` output

---

#### Exercise 1.2: Create VPC with Subnets (Day 2-3)
**Objective:** Build network foundation

**Tasks:**
- [ ] Create VPC via AWS Console (CIDR: 10.0.0.0/16)
- [ ] Create public subnet (10.0.1.0/24) in us-east-1a
- [ ] Create public subnet (10.0.2.0/24) in us-east-1b
- [ ] Create private subnet (10.0.10.0/24) in us-east-1a
- [ ] Create private subnet (10.0.11.0/24) in us-east-1b
- [ ] Create Internet Gateway and attach to VPC
- [ ] Create NAT Gateway in public subnet
- [ ] Configure route tables:
  - [ ] Public route table (0.0.0.0/0 → IGW)
  - [ ] Private route table (0.0.0.0/0 → NAT Gateway)

**Deliverable:** `exercise-1.2-vpc-diagram.md` - Document VPC architecture with CIDR blocks and routing

---

#### Exercise 1.3: Launch EC2 Instances (Day 4-5)
**Objective:** Deploy compute resources

**Tasks:**
- [ ] Create security group for web servers (allow SSH, HTTP, HTTPS)
- [ ] Create security group for database (allow MySQL from web SG only)
- [ ] Launch EC2 instance in public subnet:
  - [ ] AMI: Amazon Linux 2023
  - [ ] Instance type: t2.micro (free tier)
  - [ ] Security group: web servers SG
  - [ ] Key pair: Create new or use existing
- [ ] Launch second EC2 instance in different AZ (us-east-1b)
- [ ] SSH into both instances and verify connectivity
- [ ] Install nginx on both: `sudo yum install -y nginx && sudo systemctl start nginx`

**Deliverable:** `exercise-1.3-ec2-setup.md` - Document instance IDs, IPs, and nginx access URLs

---

#### Exercise 1.4: IAM Policies and Roles (Day 6-7)
**Objective:** Implement least privilege access

**Tasks:**
- [ ] Create IAM policy `EC2ReadOnly` (read-only EC2 access)
- [ ] Create IAM policy `S3FullAccess` (S3 full access)
- [ ] Create IAM role `EC2S3Role` with both policies
- [ ] Create IAM user `devops-engineer` with `EC2ReadOnly` policy
- [ ] Create IAM user `developer` with no permissions initially
- [ ] Attach `EC2S3Role` to EC2 instances
- [ ] Test: SSH into EC2 and verify S3 access: `aws s3 ls` (should work without credentials)

**Deliverable:** `exercise-1.4-iam-policies.json` - Export IAM policies as JSON files

---

#### Exercise 1.5: Network ACLs and Security Hardening (Day 8-10)
**Objective:** Add network-level security

**Tasks:**
- [ ] Create custom Network ACL for public subnets
- [ ] Deny inbound traffic on port 22 (SSH) from 0.0.0.0/0
- [ ] Allow inbound traffic on port 22 only from your IP
- [ ] Allow HTTP (80) and HTTPS (443) from anywhere
- [ ] Create custom Network ACL for private subnets
- [ ] Deny all inbound from internet
- [ ] Test: Try SSH from different IP (should fail)
- [ ] Document security group vs NACL differences

**Deliverable:** `exercise-1.5-security-hardening.md` - Document NACL rules and security best practices

---

#### Exercise 1.6: Cost Monitoring and Cleanup (Day 11-14)
**Objective:** Understand AWS costs and cleanup

**Tasks:**
- [ ] Set up AWS Cost Explorer
- [ ] Create billing alert (threshold: $5)
- [ ] Tag all resources (Environment: learning, Project: sre-curriculum)
- [ ] Document all resources created
- [ ] Create cleanup script to terminate all resources
- [ ] Test cleanup script (verify it works)
- [ ] **DO NOT RUN** cleanup yet (needed for next exercises)

**Deliverable:** `exercise-1.6-cost-monitoring.md` - Screenshot of Cost Explorer and list of all resources with tags

---

### Weeks 5–6: Terraform

#### Exercise 2.1: Terraform Setup and First Resource (Day 15-16)
**Objective:** Get started with Terraform

**Tasks:**
- [ ] Install Terraform: `brew install terraform` or [download](https://www.terraform.io/downloads)
- [ ] Verify: `terraform version`
- [ ] Create directory: `mkdir terraform-basics && cd terraform-basics`
- [ ] Create `main.tf` with S3 bucket resource
- [ ] Run `terraform init`
- [ ] Run `terraform plan`
- [ ] Run `terraform apply` (type `yes`)
- [ ] Verify bucket in AWS Console
- [ ] Run `terraform destroy`

**Deliverable:** `terraform-basics/main.tf` - Working Terraform file that creates and destroys S3 bucket

---

#### Exercise 2.2: Terraform Variables and Outputs (Day 17-18)
**Objective:** Learn Terraform configuration

**Tasks:**
- [ ] Create `variables.tf` with:
  - [ ] `bucket_name` (string, description)
  - [ ] `environment` (string, default: "dev")
- [ ] Create `outputs.tf` with:
  - [ ] Bucket ARN
  - [ ] Bucket domain name
- [ ] Create `terraform.tfvars` with values
- [ ] Refactor `main.tf` to use variables
- [ ] Run `terraform plan` and verify variables are used
- [ ] Apply and verify outputs

**Deliverable:** `terraform-basics/` directory with `main.tf`, `variables.tf`, `outputs.tf`, `terraform.tfvars`

---

#### Exercise 2.3: Terraform Modules - VPC Module (Day 19-21)
**Objective:** Create reusable modules

**Tasks:**
- [ ] Create directory structure:
  ```
  terraform-modules/
  ├── modules/
  │   └── vpc/
  │       ├── main.tf
  │       ├── variables.tf
  │       └── outputs.tf
  └── environments/
      ├── dev/
      └── prod/
  ```
- [ ] Create VPC module that creates:
  - [ ] VPC
  - [ ] 2 public subnets
  - [ ] 2 private subnets
  - [ ] Internet Gateway
  - [ ] NAT Gateway
  - [ ] Route tables
- [ ] Use module in `environments/dev/main.tf`
- [ ] Apply and verify resources

**Deliverable:** `terraform-modules/` directory with working VPC module

---

#### Exercise 2.4: Terraform State Management (Day 22-23)
**Objective:** Understand and manage Terraform state

**Tasks:**
- [ ] Create S3 bucket for state: `terraform-state-bucket-<your-name>`
- [ ] Create DynamoDB table: `terraform-state-lock`
- [ ] Configure backend in `main.tf`:
  ```hcl
  backend "s3" {
    bucket = "terraform-state-bucket-<your-name>"
    key    = "dev/terraform.tfstate"
    region = "us-east-1"
    dynamodb_table = "terraform-state-lock"
  }
  ```
- [ ] Run `terraform init` (migrate state)
- [ ] Verify state file in S3
- [ ] Test state locking (try `terraform apply` from two terminals)

**Deliverable:** `terraform-state-setup.md` - Document S3 bucket name, DynamoDB table, and state locking test results

---

#### Exercise 2.5: EKS Cluster with Terraform (Day 24-28)
**Objective:** Provision Kubernetes cluster

**Tasks:**
- [ ] Use [terraform-aws-modules/eks/aws](https://registry.terraform.io/modules/terraform-aws-modules/eks/aws/latest)
- [ ] Create `eks-cluster.tf`:
  - [ ] EKS cluster (version 1.28+)
  - [ ] Managed node group (t3.medium, 2 nodes)
  - [ ] IAM roles for cluster and nodes
- [ ] Create `kubeconfig.tf` to output kubeconfig
- [ ] Apply Terraform
- [ ] Configure kubectl: `aws eks update-kubeconfig --name <cluster-name>`
- [ ] Verify: `kubectl get nodes`
- [ ] Test: Deploy nginx and verify it works

**Deliverable:** `terraform-eks/` directory with EKS cluster Terraform code and `kubectl get nodes` output

---

#### Exercise 2.6: Multi-Environment Setup (Day 29-30)
**Objective:** Manage multiple environments

**Tasks:**
- [ ] Create `environments/dev/` with:
  - [ ] `terraform.tfvars` (small instance types, 2 nodes)
  - [ ] `backend.tf` (state path: `dev/terraform.tfstate`)
- [ ] Create `environments/staging/` with:
  - [ ] `terraform.tfvars` (medium instance types, 3 nodes)
  - [ ] `backend.tf` (state path: `staging/terraform.tfstate`)
- [ ] Create `environments/prod/` with:
  - [ ] `terraform.tfvars` (larger instance types, 4 nodes)
  - [ ] `backend.tf` (state path: `prod/terraform.tfstate`)
- [ ] Deploy dev environment
- [ ] Verify resources in AWS Console
- [ ] Document differences between environments

**Deliverable:** `multi-env-setup/` directory with dev/staging/prod configurations and comparison document

---

### Weeks 7–8: Kubernetes

#### Exercise 3.1: Local Kubernetes Setup (Day 31-32)
**Objective:** Set up local K8s cluster

**Tasks:**
- [ ] Install Docker Desktop (or Docker + kind)
- [ ] Install kubectl: `brew install kubectl`
- [ ] Option A - Docker Desktop: Enable Kubernetes in settings
- [ ] Option B - kind: `brew install kind && kind create cluster`
- [ ] Verify: `kubectl cluster-info`
- [ ] Install k9s (optional but helpful): `brew install k9s`
- [ ] Test: `kubectl run nginx --image=nginx --port=80`

**Deliverable:** `kubectl get nodes` output showing local cluster running

---

#### Exercise 3.2: Deploy Nginx with Deployment (Day 33-34)
**Objective:** Deploy application using Deployment

**Tasks:**
- [ ] Create `nginx-deployment.yaml`:
  - [ ] Deployment with 3 replicas
  - [ ] Container: nginx:latest
  - [ ] Resource limits: CPU 100m, Memory 128Mi
  - [ ] Resource requests: CPU 50m, Memory 64Mi
- [ ] Apply: `kubectl apply -f nginx-deployment.yaml`
- [ ] Verify: `kubectl get pods`, `kubectl get deployments`
- [ ] Scale: `kubectl scale deployment nginx --replicas=5`
- [ ] Verify scaling: `kubectl get pods`
- [ ] Delete a pod: `kubectl delete pod <pod-name>`
- [ ] Verify auto-recovery: `kubectl get pods`

**Deliverable:** `k8s-exercises/nginx-deployment.yaml` and screenshots of `kubectl get pods` showing 5 replicas

---

#### Exercise 3.3: Services - Expose Application (Day 35-36)
**Objective:** Configure networking

**Tasks:**
- [ ] Create `nginx-service.yaml`:
  - [ ] Service type: ClusterIP
  - [ ] Selector matching nginx deployment
  - [ ] Port: 80 → 80
- [ ] Apply service
- [ ] Verify: `kubectl get svc`
- [ ] Test connectivity: `kubectl run curl --image=curlimages/curl -it --rm -- curl http://nginx-service:80`
- [ ] Create NodePort service:
  - [ ] Change type to NodePort
  - [ ] Apply and note the NodePort (30000-32767)
  - [ ] Access via `localhost:<nodeport>`
- [ ] Create LoadBalancer service (if on cloud):
  - [ ] Change type to LoadBalancer
  - [ ] Get external IP: `kubectl get svc`

**Deliverable:** `k8s-exercises/nginx-service.yaml` with all three service types and test results

---

#### Exercise 3.4: Horizontal Pod Autoscaler (Day 37-38)
**Objective:** Implement auto-scaling

**Tasks:**
- [ ] Install metrics-server (if not installed):
  ```bash
  kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
  ```
- [ ] Create `nginx-hpa.yaml`:
  - [ ] HPA targeting nginx deployment
  - [ ] Min replicas: 2
  - [ ] Max replicas: 10
  - [ ] Target CPU: 70%
- [ ] Apply HPA: `kubectl apply -f nginx-hpa.yaml`
- [ ] Verify: `kubectl get hpa`
- [ ] Generate load:
  ```bash
  kubectl run load-generator --image=busybox --restart=Never -- /bin/sh -c "while true; do wget -q -O- http://nginx-service; done"
  ```
- [ ] Watch HPA: `kubectl get hpa -w`
- [ ] Watch pods scaling: `kubectl get pods -w`
- [ ] Stop load generator: `kubectl delete pod load-generator`
- [ ] Verify scale-down

**Deliverable:** `k8s-exercises/nginx-hpa.yaml` and screenshots showing HPA scaling up and down

---

#### Exercise 3.5: Pod Disruption Budget (Day 39-40)
**Objective:** Ensure availability during disruptions

**Tasks:**
- [ ] Create `nginx-pdb.yaml`:
  - [ ] PDB for nginx deployment
  - [ ] Min available: 2 pods
  - [ ] Or max unavailable: 1 pod
- [ ] Apply PDB: `kubectl apply -f nginx-pdb.yaml`
- [ ] Verify: `kubectl get pdb`
- [ ] Scale deployment to 5 replicas
- [ ] Try to drain node (or delete pods):
  ```bash
  kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data
  ```
- [ ] Observe: Only 1 pod should be terminated at a time
- [ ] Verify: `kubectl get pods` (should always have at least 2 running)

**Deliverable:** `k8s-exercises/nginx-pdb.yaml` and documentation of PDB behavior during node drain

---

#### Exercise 3.6: ConfigMaps and Secrets (Day 41-42)
**Objective:** Manage configuration and secrets

**Tasks:**
- [ ] Create ConfigMap:
  ```bash
  kubectl create configmap nginx-config --from-literal=server_name=example.com
  ```
- [ ] Create Secret:
  ```bash
  kubectl create secret generic nginx-secret --from-literal=api-key=secret123
  ```
- [ ] Update deployment to use ConfigMap and Secret:
  - [ ] Mount ConfigMap as environment variable
  - [ ] Mount Secret as environment variable
  - [ ] Or mount as volumes
- [ ] Apply updated deployment
- [ ] Verify: `kubectl exec <pod-name> -- env | grep SERVER_NAME`
- [ ] Verify: `kubectl exec <pod-name> -- env | grep API_KEY`

**Deliverable:** `k8s-exercises/configmap-secret-example.yaml` showing deployment using ConfigMap and Secret

---

### Weeks 9–10: Helm + Kustomize

#### Exercise 4.1: Helm Installation and First Chart (Day 43-44)
**Objective:** Get started with Helm

**Tasks:**
- [ ] Install Helm: `brew install helm`
- [ ] Verify: `helm version`
- [ ] Add stable repo: `helm repo add stable https://charts.helm.sh/stable`
- [ ] Search charts: `helm search repo nginx`
- [ ] Install nginx: `helm install my-nginx stable/nginx`
- [ ] List releases: `helm list`
- [ ] Get values: `helm get values my-nginx`
- [ ] Upgrade: `helm upgrade my-nginx stable/nginx --set replicaCount=3`
- [ ] Uninstall: `helm uninstall my-nginx`

**Deliverable:** Screenshots of `helm list` and `helm get values` output

---

#### Exercise 4.2: Create Custom Helm Chart (Day 45-47)
**Objective:** Build your own chart

**Tasks:**
- [ ] Create chart: `helm create myapp`
- [ ] Explore chart structure:
  - [ ] `Chart.yaml` - Chart metadata
  - [ ] `values.yaml` - Default values
  - [ ] `templates/` - Kubernetes manifests
- [ ] Customize `values.yaml`:
  - [ ] Image repository and tag
  - [ ] Replica count
  - [ ] Resource limits
  - [ ] Service type
- [ ] Customize templates:
  - [ ] Update deployment template
  - [ ] Update service template
  - [ ] Add ConfigMap template
- [ ] Lint: `helm lint myapp`
- [ ] Dry run: `helm install myapp ./myapp --dry-run --debug`
- [ ] Install: `helm install myapp ./myapp`
- [ ] Verify: `kubectl get all`

**Deliverable:** `helm-charts/myapp/` directory with custom Helm chart

---

#### Exercise 4.3: Helm Chart with Dependencies (Day 48-49)
**Objective:** Use chart dependencies

**Tasks:**
- [ ] Create parent chart: `helm create parent-app`
- [ ] Add dependency in `Chart.yaml`:
  ```yaml
  dependencies:
    - name: postgresql
      version: "12.1.2"
      repository: "https://charts.bitnami.com/bitnami"
  ```
- [ ] Update dependencies: `helm dependency update parent-app`
- [ ] Verify: Check `charts/` directory
- [ ] Install with dependencies: `helm install parent-app ./parent-app`
- [ ] Verify both applications running

**Deliverable:** `helm-charts/parent-app/` with working dependencies

---

#### Exercise 4.4: Kustomize Basics (Day 50-51)
**Objective:** Learn Kustomize

**Tasks:**
- [ ] Install Kustomize: `brew install kustomize`
- [ ] Create base directory:
  ```
  kustomize-example/
  ├── base/
  │   ├── kustomization.yaml
  │   ├── deployment.yaml
  │   └── service.yaml
  ```
- [ ] Create `base/kustomization.yaml`:
  ```yaml
  resources:
    - deployment.yaml
    - service.yaml
  ```
- [ ] Create overlay for dev:
  ```
  overlays/
  └── dev/
      └── kustomization.yaml
  ```
- [ ] Configure dev overlay:
  ```yaml
  bases:
    - ../../base
  replicas:
    - name: myapp
      count: 2
  ```
- [ ] Build: `kustomize build overlays/dev`
- [ ] Apply: `kubectl apply -k overlays/dev`

**Deliverable:** `kustomize-example/` directory with base and overlay structure

---

#### Exercise 4.5: Helm vs Kustomize Comparison (Day 52-54)
**Objective:** Understand when to use each tool

**Tasks:**
- [ ] Deploy same application using Helm
- [ ] Deploy same application using Kustomize
- [ ] Compare:
  - [ ] Ease of use
  - [ ] Flexibility
  - [ ] Template capabilities
  - [ ] Dependency management
- [ ] Document use cases for each
- [ ] Create decision matrix

**Deliverable:** `helm-vs-kustomize-comparison.md` with analysis and recommendations

---

#### Exercise 4.6: Package and Publish Helm Chart (Day 55-56)
**Objective:** Share charts

**Tasks:**
- [ ] Package chart: `helm package myapp`
- [ ] Create chart repository:
  - [ ] Create GitHub repo: `helm-charts`
  - [ ] Initialize: `helm repo index . --url https://<username>.github.io/helm-charts`
  - [ ] Enable GitHub Pages
- [ ] Add to local repos: `helm repo add myrepo https://<username>.github.io/helm-charts`
- [ ] Install from repo: `helm install myapp myrepo/myapp`
- [ ] Document chart in README.md

**Deliverable:** Published Helm chart repository on GitHub with working installation

---

### Weeks 11–12: CI/CD with GitHub Actions

#### Exercise 5.1: First GitHub Actions Workflow (Day 57-58)
**Objective:** Create basic CI pipeline

**Tasks:**
- [ ] Create `.github/workflows/ci.yml`:
  ```yaml
  name: CI
  on: [push, pull_request]
  jobs:
    test:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v3
        - name: Run tests
          run: echo "Tests would run here"
  ```
- [ ] Push to GitHub
- [ ] Verify workflow runs in Actions tab
- [ ] Add more steps:
  - [ ] Lint code
  - [ ] Run tests
  - [ ] Build artifacts

**Deliverable:** `.github/workflows/ci.yml` with working CI pipeline

---

#### Exercise 5.2: Terraform Plan/Apply Pipeline (Day 59-61)
**Objective:** Automate Terraform

**Tasks:**
- [ ] Create `.github/workflows/terraform.yml`:
  - [ ] On PR: Run `terraform plan`
  - [ ] On merge to main: Run `terraform apply`
  - [ ] Use `hashicorp/setup-terraform@v2`
  - [ ] Configure AWS credentials via secrets
- [ ] Add secrets to GitHub:
  - [ ] `AWS_ACCESS_KEY_ID`
  - [ ] `AWS_SECRET_ACCESS_KEY`
- [ ] Create PR and verify plan runs
- [ ] Merge PR and verify apply runs
- [ ] Add plan output as PR comment

**Deliverable:** `.github/workflows/terraform.yml` with plan on PR and apply on merge

---

#### Exercise 5.3: Helm Deployment Pipeline (Day 62-63)
**Objective:** Automate Helm deployments

**Tasks:**
- [ ] Create `.github/workflows/helm.yml`:
  - [ ] Install Helm
  - [ ] Lint charts on PR
  - [ ] Deploy to dev on merge to main
  - [ ] Deploy to staging/prod with manual approval
- [ ] Configure Kubernetes credentials:
  - [ ] Create service account in K8s
  - [ ] Create kubeconfig secret in GitHub
- [ ] Test deployment pipeline
- [ ] Verify application deployed correctly

**Deliverable:** `.github/workflows/helm.yml` with automated Helm deployments

---

#### Exercise 5.4: Branch Protection and Secrets (Day 64-65)
**Objective:** Secure the pipeline

**Tasks:**
- [ ] Configure branch protection:
  - [ ] Require PR reviews
  - [ ] Require status checks
  - [ ] Require up-to-date branches
- [ ] Review secret management:
  - [ ] Use GitHub Secrets (not hardcoded)
  - [ ] Rotate secrets regularly
  - [ ] Use least privilege IAM roles
- [ ] Add security scanning:
  - [ ] `trivy` for container scanning
  - [ ] `tfsec` for Terraform security
- [ ] Document security practices

**Deliverable:** `security-practices.md` documenting branch protection and secret management

---

#### Exercise 5.5: Multi-Environment Deployment (Day 66-68)
**Objective:** Deploy to multiple environments

**Tasks:**
- [ ] Create workflow with environments:
  ```yaml
  environment:
    name: production
    url: https://myapp.example.com
  ```
- [ ] Configure environment protection rules:
  - [ ] Required reviewers for prod
  - [ ] Deployment branches (only main)
- [ ] Create deployment workflow:
  - [ ] Deploy to dev automatically
  - [ ] Deploy to staging with approval
  - [ ] Deploy to prod with approval
- [ ] Test deployment to each environment

**Deliverable:** `.github/workflows/deploy.yml` with multi-environment support

---

#### Exercise 5.6: Complete CI/CD Pipeline (Day 69-70)
**Objective:** Integrate everything

**Tasks:**
- [ ] Create comprehensive pipeline:
  - [ ] Lint and test on PR
  - [ ] Terraform plan on PR
  - [ ] Helm lint on PR
  - [ ] Terraform apply on merge
  - [ ] Helm deploy on merge
  - [ ] Notify on success/failure (Slack)
- [ ] Add status badges to README
- [ ] Document pipeline flow
- [ ] Create runbook for common issues

**Deliverable:** Complete `web3-infra-bootstrap` repository with full CI/CD pipeline

---

## Phase 2 – Blockchain Node Ops (Weeks 13–24)

### Weeks 13–14: Ethereum (Geth)

#### Exercise 6.1: Install and Configure Geth (Day 71-72)
**Objective:** Set up Ethereum node

**Tasks:**
- [ ] Install Geth: `brew install ethereum` or [download](https://geth.ethereum.org/downloads)
- [ ] Verify: `geth version`
- [ ] Create data directory: `mkdir -p ~/ethereum-node/data`
- [ ] Start Geth in sync mode:
  ```bash
  geth --syncmode snap --datadir ~/ethereum-node/data --http --http.addr "0.0.0.0" --http.port 8545 --http.api "eth,net,web3"
  ```
- [ ] Monitor sync: `geth attach --exec eth.syncing`
- [ ] Check sync status: `geth attach --exec eth.blockNumber`

**Deliverable:** `geth-setup.md` with installation steps and initial sync status

---

#### Exercise 6.2: Dockerize Geth Node (Day 73-74)
**Objective:** Containerize Ethereum node

**Tasks:**
- [ ] Create `Dockerfile`:
  ```dockerfile
  FROM ethereum/client-go:latest
  COPY entrypoint.sh /entrypoint.sh
  ENTRYPOINT ["/entrypoint.sh"]
  ```
- [ ] Create `entrypoint.sh` with Geth command
- [ ] Create `docker-compose.yml`:
  - [ ] Geth service
  - [ ] Volume for data persistence
  - [ ] Port mapping (8545, 30303)
- [ ] Build and run: `docker-compose up -d`
- [ ] Verify: `docker-compose logs -f geth`
- [ ] Test RPC: `curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' http://localhost:8545`

**Deliverable:** `docker-compose.yml` and `Dockerfile` for Geth node

---

#### Exercise 6.3: NAT Traversal Configuration (Day 75-76)
**Objective:** Configure node behind NAT

**Tasks:**
- [ ] Enable UPnP in Geth: `--nat extip:<your-public-ip>`
- [ ] Or configure port forwarding:
  - [ ] Router: Forward port 30303 (TCP/UDP)
  - [ ] Router: Forward port 30303 (UDP for discovery)
- [ ] Test connectivity:
  ```bash
  geth attach --exec admin.nodeInfo.enode
  ```
- [ ] Verify peers: `geth attach --exec net.peerCount`
- [ ] Check if node is discoverable

**Deliverable:** `nat-configuration.md` documenting NAT setup and peer count verification

---

#### Exercise 6.4: Monitor Node Sync Status (Day 77-78)
**Objective:** Track synchronization

**Tasks:**
- [ ] Create monitoring script:
  ```bash
  #!/bin/bash
  BLOCK_NUMBER=$(geth attach --exec eth.blockNumber | tr -d '"')
  PEER_COUNT=$(geth attach --exec net.peerCount)
  SYNCING=$(geth attach --exec eth.syncing)
  echo "Block: $BLOCK_NUMBER, Peers: $PEER_COUNT, Syncing: $SYNCING"
  ```
- [ ] Set up cron job to run every 5 minutes
- [ ] Log sync status to file
- [ ] Create alert if sync lag > 100 blocks
- [ ] Visualize sync progress

**Deliverable:** `monitor-sync.sh` script with logging and alerting

---

#### Exercise 6.5: Optimize Node Performance (Day 79-80)
**Objective:** Improve node efficiency

**Tasks:**
- [ ] Benchmark current performance:
  - [ ] CPU usage
  - [ ] Memory usage
  - [ ] Disk I/O
  - [ ] Network bandwidth
- [ ] Tune Geth parameters:
  - [ ] `--cache` (memory for caching)
  - [ ] `--maxpeers` (number of peers)
  - [ ] `--syncmode` (snap vs full)
- [ ] Compare performance before/after
- [ ] Document optimal settings

**Deliverable:** `performance-tuning.md` with benchmarks and optimal configuration

---

#### Exercise 6.6: Node Health Checks (Day 81-84)
**Objective:** Implement health monitoring

**Tasks:**
- [ ] Create health check endpoint:
  ```bash
  #!/bin/bash
  RESPONSE=$(curl -s -X POST -H "Content-Type: application/json" \
    --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' \
    http://localhost:8545)
  if [ $? -eq 0 ]; then
    echo "healthy"
  else
    echo "unhealthy"
  fi
  ```
- [ ] Create Kubernetes liveness probe
- [ ] Create Kubernetes readiness probe
- [ ] Test health checks
- [ ] Integrate with monitoring system

**Deliverable:** `health-check.sh` and Kubernetes probe configurations

---

### Weeks 15–16: Polygon/Arbitrum

#### Exercise 7.1: Polygon Bor Node Setup (Day 85-87)
**Objective:** Deploy Polygon validator node

**Tasks:**
- [ ] Install Polygon: Follow [Polygon docs](https://docs.polygon.technology/docs/validate/validate/getting-started)
- [ ] Configure Bor node
- [ ] Configure Heimdall node
- [ ] Start both services
- [ ] Verify node is syncing
- [ ] Check validator status

**Deliverable:** `polygon-setup.md` with installation and configuration steps

---

#### Exercise 7.2: Arbitrum Nitro Node (Day 88-90)
**Objective:** Deploy Arbitrum node

**Tasks:**
- [ ] Clone Arbitrum Nitro: `git clone https://github.com/OffchainLabs/nitro.git`
- [ ] Follow [Arbitrum setup guide](https://docs.arbitrum.io/node-running/node-operator)
- [ ] Configure L1 and L2 connections
- [ ] Start Nitro node
- [ ] Verify synchronization
- [ ] Test RPC endpoints

**Deliverable:** `arbitrum-setup.md` with working Nitro node configuration

---

#### Exercise 7.3: Multi-Node Cluster (Day 91-94)
**Objective:** Create high-availability setup

**Tasks:**
- [ ] Deploy 2 Ethereum nodes (or Polygon/Arbitrum)
- [ ] Configure load balancer (Nginx)
- [ ] Set up health checks
- [ ] Test failover (stop one node)
- [ ] Verify traffic routes to healthy node
- [ ] Document cluster architecture

**Deliverable:** `multi-node-cluster/` directory with configuration and architecture diagram

---

### Weeks 17–18: RPC Load Balancing

#### Exercise 9.1: Nginx Load Balancer (Day 101-103)
**Objective:** Set up Nginx for RPC load balancing

**Tasks:**
- [ ] Install Nginx: `brew install nginx` or `apt install nginx`
- [ ] Configure upstream:
  ```nginx
  upstream ethereum_nodes {
    server node1:8545;
    server node2:8545;
  }
  ```
- [ ] Configure proxy:
  ```nginx
  location / {
    proxy_pass http://ethereum_nodes;
    proxy_set_header Host $host;
  }
  ```
- [ ] Test load balancing
- [ ] Monitor backend health

**Deliverable:** `nginx.conf` with working load balancer configuration

---

#### Exercise 9.2: HAProxy Configuration (Day 104-106)
**Objective:** Configure HAProxy

**Tasks:**
- [ ] Install HAProxy: `brew install haproxy`
- [ ] Create `haproxy.cfg`:
  ```haproxy
  backend ethereum_nodes
    balance roundrobin
    option httpchk GET /health
    server node1 node1:8545 check
    server node2 node2:8545 check
  ```
- [ ] Configure health checks
- [ ] Test failover
- [ ] Monitor statistics

**Deliverable:** `haproxy.cfg` with health checks and failover

---

#### Exercise 9.3: Health Check Implementation (Day 107-110)
**Objective:** Implement comprehensive health checks

**Tasks:**
- [ ] Create health check endpoint for each node
- [ ] Check sync status
- [ ] Check peer count
- [ ] Check RPC responsiveness
- [ ] Configure Nginx/HAProxy to use health checks
- [ ] Test automatic failover
- [ ] Document health check criteria

**Deliverable:** `health-checks/` directory with scripts and configuration

---

### Weeks 19–20: Monitoring

#### Exercise 10.1: Prometheus Setup (Day 111-113)
**Objective:** Install and configure Prometheus

**Tasks:**
- [ ] Install Prometheus: `brew install prometheus` or Docker
- [ ] Create `prometheus.yml`:
  ```yaml
  scrape_configs:
    - job_name: 'ethereum-node'
      static_configs:
        - targets: ['node1:9090']
  ```
- [ ] Start Prometheus
- [ ] Access UI: http://localhost:9090
- [ ] Query metrics: `up`, `node_cpu_seconds_total`

**Deliverable:** `prometheus-setup/` with configuration and sample queries

---

#### Exercise 10.2: Node Exporter (Day 114-115)
**Objective:** Export system metrics

**Tasks:**
- [ ] Install node_exporter: `brew install node_exporter`
- [ ] Start node_exporter
- [ ] Configure Prometheus to scrape
- [ ] Query system metrics:
  - [ ] CPU usage
  - [ ] Memory usage
  - [ ] Disk I/O
  - [ ] Network traffic

**Deliverable:** Prometheus configuration scraping node_exporter

---

#### Exercise 10.3: Custom Ethereum Metrics (Day 116-118)
**Objective:** Export blockchain-specific metrics

**Tasks:**
- [ ] Create custom exporter or use existing:
  - [ ] Block number
  - [ ] Peer count
  - [ ] Sync status
  - [ ] RPC request rate
- [ ] Expose metrics endpoint
- [ ] Configure Prometheus to scrape
- [ ] Create queries for:
  - [ ] Sync lag
  - [ ] Block propagation time
  - [ ] RPC latency

**Deliverable:** Custom exporter with Ethereum-specific metrics

---

#### Exercise 10.4: Grafana Dashboards (Day 119-121)
**Objective:** Visualize metrics

**Tasks:**
- [ ] Install Grafana: `brew install grafana` or Docker
- [ ] Add Prometheus data source
- [ ] Create dashboard with panels:
  - [ ] Sync lag graph
  - [ ] Peer count gauge
  - [ ] CPU/Memory usage
  - [ ] Disk I/O
  - [ ] Network traffic
  - [ ] RPC requests per second
- [ ] Export dashboard JSON
- [ ] Share dashboard

**Deliverable:** `grafana-dashboards/` with JSON exports and screenshots

---

### Weeks 21–22: Alerting

#### Exercise 11.1: Alertmanager Setup (Day 122-124)
**Objective:** Configure alerting

**Tasks:**
- [ ] Install Alertmanager
- [ ] Create `alertmanager.yml`:
  ```yaml
  route:
    receiver: 'slack-notifications'
  receivers:
    - name: 'slack-notifications'
      slack_configs:
        - api_url: '<slack-webhook>'
  ```
- [ ] Configure Prometheus to use Alertmanager
- [ ] Test alert delivery

**Deliverable:** `alertmanager.yml` with Slack integration

---

#### Exercise 11.2: Alert Rules (Day 125-127)
**Objective:** Define alert conditions

**Tasks:**
- [ ] Create `alerts.yml`:
  - [ ] Sync lag > 5 minutes
  - [ ] Node down
  - [ ] High CPU (> 80%)
  - [ ] High memory (> 90%)
  - [ ] Disk space low (< 10%)
  - [ ] Peer count < 5
- [ ] Configure Prometheus to load rules
- [ ] Test each alert
- [ ] Verify alert firing

**Deliverable:** `alerts.yml` with all alert rules and test results

---

#### Exercise 11.3: PagerDuty Integration (Day 128-130)
**Objective:** Set up on-call alerts

**Tasks:**
- [ ] Create PagerDuty account (free tier)
- [ ] Create service
- [ ] Get integration key
- [ ] Configure Alertmanager:
  ```yaml
  receivers:
    - name: 'pagerduty'
      pagerduty_configs:
        - service_key: '<integration-key>'
  ```
- [ ] Test alert delivery
- [ ] Configure escalation policies

**Deliverable:** PagerDuty integration working with test alerts

---

#### Exercise 11.4: Complete Monitoring Stack (Day 131-140)
**Objective:** Integrate everything

**Tasks:**
- [ ] Deploy complete stack:
  - [ ] Prometheus
  - [ ] Grafana
  - [ ] Alertmanager
  - [ ] Node exporters
  - [ ] Custom exporters
- [ ] Create comprehensive dashboards
- [ ] Configure all alert rules
- [ ] Test alerting end-to-end
- [ ] Document runbook

**Deliverable:** Complete `eth-node-ha` repository with monitoring and alerting

---

## Phase 3 – SRE Fundamentals (Weeks 23–34)

### Weeks 23–24: Google SRE Book

#### Exercise 12.1: Read SRE Book Chapters 1-7 (Day 141-148)
**Objective:** Understand SRE principles

**Tasks:**
- [ ] Read Chapter 1: Introduction
- [ ] Read Chapter 2: The Production Environment at Google
- [ ] Read Chapter 3: Embracing Risk
- [ ] Read Chapter 4: Service Level Objectives
- [ ] Read Chapter 5: Eliminating Toil
- [ ] Read Chapter 6: Monitoring Distributed Systems
- [ ] Read Chapter 7: The Evolution of Automation at Google
- [ ] Take notes on key concepts
- [ ] Create summary document

**Deliverable:** `sre-book-notes.md` with key takeaways from each chapter

---

#### Exercise 12.2: Define SLIs for Ethereum Node (Day 149-151)
**Objective:** Create service level indicators

**Tasks:**
- [ ] Identify user journeys:
  - [ ] RPC request success
  - [ ] RPC latency
  - [ ] Node availability
  - [ ] Data freshness (sync lag)
- [ ] Define SLIs for each:
  - [ ] Availability: `successful_requests / total_requests`
  - [ ] Latency: `p99_response_time`
  - [ ] Freshness: `current_block - synced_block`
- [ ] Document SLI definitions
- [ ] Create SLI queries in Prometheus

**Deliverable:** `slis.md` with SLI definitions and Prometheus queries

---

#### Exercise 12.3: Set SLO Targets (Day 152-154)
**Objective:** Define service level objectives

**Tasks:**
- [ ] Set SLO targets:
  - [ ] Availability: 99.9% (3 nines)
  - [ ] Latency: p99 < 500ms
  - [ ] Freshness: sync lag < 5 minutes
- [ ] Calculate error budgets:
  - [ ] 99.9% availability = 43.2 minutes downtime/month
- [ ] Document SLO rationale
- [ ] Create SLO dashboard in Grafana

**Deliverable:** `slos.md` with SLO targets, error budgets, and dashboard

---

#### Exercise 12.4: Implement SLO Monitoring (Day 155-160)
**Objective:** Track SLO compliance

**Tasks:**
- [ ] Create Prometheus recording rules for SLIs
- [ ] Calculate SLO compliance
- [ ] Create Grafana dashboard:
  - [ ] Current SLO status
  - [ ] Error budget remaining
  - [ ] Burn rate
- [ ] Set up alerts for error budget exhaustion
- [ ] Document SLO tracking process

**Deliverable:** SLO monitoring dashboard and alerting configuration

---

### Weeks 25–26: Observability Triad

#### Exercise 13.1: Loki Setup (Day 161-163)
**Objective:** Aggregate logs

**Tasks:**
- [ ] Install Loki: Docker or Helm
- [ ] Configure Promtail to scrape logs
- [ ] Send node logs to Loki
- [ ] Query logs: `{job="ethereum-node"}`
- [ ] Create log dashboards in Grafana
- [ ] Correlate logs with metrics

**Deliverable:** Loki setup with log aggregation working

---

#### Exercise 13.2: Tempo Setup (Day 164-166)
**Objective:** Distributed tracing

**Tasks:**
- [ ] Install Tempo
- [ ] Instrument application with OpenTelemetry
- [ ] Send traces to Tempo
- [ ] Query traces in Grafana
- [ ] Create trace dashboards
- [ ] Correlate traces with logs and metrics

**Deliverable:** Tempo setup with distributed tracing

---

#### Exercise 13.3: Unified Observability (Day 167-170)
**Objective:** Integrate metrics, logs, traces

**Tasks:**
- [ ] Configure Grafana with all data sources:
  - [ ] Prometheus (metrics)
  - [ ] Loki (logs)
  - [ ] Tempo (traces)
- [ ] Create unified dashboards
- [ ] Enable correlation:
  - [ ] Click metric → see logs
  - [ ] Click log → see trace
- [ ] Document observability stack

**Deliverable:** Unified observability setup with correlation working

---

### Weeks 27–28: Chaos Engineering

#### Exercise 14.1: Install Chaos Mesh (Day 171-173)
**Objective:** Set up chaos engineering platform

**Tasks:**
- [ ] Install Chaos Mesh: `kubectl apply -f https://mirrors.chaos-mesh.org/latest/crd.yaml`
- [ ] Install Chaos Dashboard
- [ ] Verify installation: `kubectl get pods -n chaos-mesh`
- [ ] Access dashboard
- [ ] Review available chaos experiments

**Deliverable:** Chaos Mesh installation verified and dashboard accessible

---

#### Exercise 14.2: Pod Kill Experiment (Day 174-176)
**Objective:** Test resilience

**Tasks:**
- [ ] Create PodChaos experiment:
  ```yaml
  apiVersion: chaos-mesh.org/v1alpha1
  kind: PodChaos
  metadata:
    name: pod-kill-example
  spec:
    action: pod-kill
    mode: one
    selector:
      namespaces:
        - default
      labelSelectors:
        app: ethereum-node
  ```
- [ ] Apply experiment
- [ ] Measure recovery time
- [ ] Verify auto-recovery
- [ ] Document MTTR

**Deliverable:** Pod kill experiment with MTTR measurement

---

#### Exercise 14.3: Network Partition (Day 177-179)
**Objective:** Test network resilience

**Tasks:**
- [ ] Create NetworkChaos experiment
- [ ] Partition network between nodes
- [ ] Observe behavior
- [ ] Measure impact
- [ ] Document findings

**Deliverable:** Network partition experiment results

---

#### Exercise 14.4: Measure MTTR (Day 180-182)
**Objective:** Quantify recovery time

**Tasks:**
- [ ] Run multiple chaos experiments
- [ ] Measure MTTR for each:
  - [ ] Pod kill
  - [ ] Network partition
  - [ ] CPU stress
  - [ ] Memory stress
- [ ] Create MTTR dashboard
- [ ] Document improvement opportunities
- [ ] Implement fixes to reduce MTTR

**Deliverable:** `mttr-analysis.md` with measurements and improvements

---

### Weeks 29–30: Incident Response

#### Exercise 15.1: Run Mock Incident (Day 183-185)
**Objective:** Practice incident response

**Tasks:**
- [ ] Create incident scenario:
  - [ ] Node sync lagging
  - [ ] High error rate
  - [ ] Memory leak
- [ ] Simulate incident
- [ ] Follow incident response process:
  - [ ] Detect
  - [ ] Respond
  - [ ] Mitigate
  - [ ] Resolve
- [ ] Document timeline
- [ ] Identify improvements

**Deliverable:** `mock-incident-report.md` with timeline and learnings

---

#### Exercise 15.2: Write Post-Mortem (Day 186-190)
**Objective:** Document incident

**Tasks:**
- [ ] Use post-mortem template:
  - [ ] Summary
  - [ ] Timeline
  - [ ] Root cause
  - [ ] Impact
  - [ ] Resolution
  - [ ] Action items
- [ ] Write comprehensive post-mortem
- [ ] Review with team (or self-review)
- [ ] Publish post-mortem
- [ ] Track action items

**Deliverable:** `post-mortem-template.md` and example post-mortem

---

### Weeks 31–32: On-Call Rotation

#### Exercise 16.1: Opsgenie Setup (Day 191-193)
**Objective:** Configure on-call management

**Tasks:**
- [ ] Create Opsgenie account (free tier)
- [ ] Create team
- [ ] Add team members
- [ ] Create on-call schedule
- [ ] Configure escalation policies
- [ ] Test alert routing

**Deliverable:** Opsgenie account with schedule and escalation configured

---

#### Exercise 16.2: Create Runbooks (Day 194-196)
**Objective:** Document common procedures

**Tasks:**
- [ ] Create runbooks for:
  - [ ] Node sync lag
  - [ ] High CPU usage
  - [ ] Memory leak
  - [ ] Network issues
  - [ ] RPC errors
- [ ] Include:
  - [ ] Symptoms
  - [ ] Diagnosis steps
  - [ ] Resolution steps
  - [ ] Escalation criteria
- [ ] Publish runbooks

**Deliverable:** `runbooks/` directory with common incident procedures

---

### Weeks 33–34: Error Budgets

#### Exercise 17.1: Calculate Error Budgets (Day 197-199)
**Objective:** Understand error budgets

**Tasks:**
- [ ] Calculate error budgets from SLOs:
  - [ ] 99.9% availability = 43.2 min/month
  - [ ] Track actual downtime
  - [ ] Calculate remaining budget
- [ ] Create error budget dashboard
- [ ] Set up budget tracking

**Deliverable:** Error budget calculation and dashboard

---

#### Exercise 17.2: Implement Budget Enforcement (Day 200-202)
**Objective:** Auto-pause deploys when budget low

**Tasks:**
- [ ] Create GitHub Action to check error budget
- [ ] Query Prometheus for budget status
- [ ] Block deployment if budget < 10%
- [ ] Notify team when budget low
- [ ] Test budget enforcement

**Deliverable:** GitHub Action workflow that enforces error budgets

---

#### Exercise 17.3: Complete SRE Playbook (Day 203-210)
**Objective:** Finalize SRE practices

**Tasks:**
- [ ] Compile all deliverables:
  - [ ] SLO definitions
  - [ ] Observability dashboards
  - [ ] Chaos tests
  - [ ] Post-mortem templates
  - [ ] Runbooks
  - [ ] Error budget enforcement
- [ ] Create comprehensive README
- [ ] Document SRE practices
- [ ] Publish `sre-playbook` repository

**Deliverable:** Complete `sre-playbook` repository with all components

---

## Phase 4 – Web3-Specific DevOps (Weeks 35–36)

### Weeks 35–36: Ponder

#### Exercise 20.1: Ponder Setup (Day 231-233)
**Objective:** Create Ponder project

**Tasks:**
- [ ] Install Ponder: `npm create ponder@latest`
- [ ] Initialize project: Follow Ponder setup wizard
- [ ] Configure Ponder:
  - [ ] Contract address
  - [ ] Network (Sepolia)
  - [ ] Events to index
- [ ] Define schema (tables)
- [ ] Write event handlers in TypeScript

**Deliverable:** Ponder project initialized

---

#### Exercise 20.2: Index Contract Events (Day 234-237)
**Objective:** Index contract events

**Tasks:**
- [ ] Define schema in `src/schema.ts`:
  ```typescript
  export const Transfer = {
    id: "string",
    from: "string",
    to: "string",
    value: "bigint",
    timestamp: "bigint",
  }
  ```
- [ ] Write event handler:
  - [ ] Handle Transfer events
  - [ ] Insert Transfer records into database
- [ ] Test locally: `npm run dev`
- [ ] Verify indexing in local database
- [ ] Deploy Ponder indexer

**Deliverable:** Ponder indexer indexing contract events

---

#### Exercise 20.3: Query Ponder API (Day 238-240)
**Objective:** Query indexed data

**Tasks:**
- [ ] Start Ponder API server
- [ ] Create API queries:
  - [ ] Get all events
  - [ ] Get events by address
  - [ ] Get event statistics
- [ ] Test queries via HTTP API or GraphQL
- [ ] Integrate queries in application
- [ ] Document API endpoints and examples

**Deliverable:** API queries with examples and documentation

---

#### Exercise 21.1: Complete Web3 CI/CD (Day 241-254)
**Objective:** Finalize monorepo

**Tasks:**
- [ ] Compile all components:
  - [ ] Ponder indexer
  - [ ] CI/CD pipeline
- [ ] Create comprehensive CI/CD pipeline
- [ ] Document entire workflow
- [ ] Publish `web3-cicd-monorepo`

**Deliverable:** Complete `web3-cicd-monorepo` with all features

---

## Phase 5 – Senior-Level Capstone (Weeks 37–48+)

Choose one option and follow the detailed requirements from the main curriculum document. Each option should result in a production-ready, open-source project that demonstrates senior-level SRE/DevOps skills.

**Options:**
- **Option A:** Open-Source Validator Fleet
- **Option B:** L2 Rollup Infrastructure
- **Option C:** SRE Consulting
- **Option D:** Safe Smart Contract Indexing with Ponder (Recommended: Combines node operations from Phase 2 and Ponder indexing from Phase 4)

---

## Progress Tracking Template

Create a file `PROGRESS.md` to track your progress:

```markdown
# Progress Tracking

## Phase 1 - Core Cloud & IaC
- [ ] Exercise 1.1: AWS Account Setup
- [ ] Exercise 1.2: Create VPC with Subnets
- [ ] Exercise 1.3: Launch EC2 Instances
- [ ] Exercise 1.4: IAM Policies and Roles
- [ ] Exercise 1.5: Network ACLs
- [ ] Exercise 1.6: Cost Monitoring
- [ ] Exercise 2.1: Terraform Setup
- [ ] Exercise 2.2: Terraform Variables
- [ ] Exercise 2.3: Terraform Modules
- [ ] Exercise 2.4: Terraform State
- [ ] Exercise 2.5: EKS Cluster
- [ ] Exercise 2.6: Multi-Environment
- [ ] Exercise 3.1: Local Kubernetes
- [ ] Exercise 3.2: Deploy Nginx
- [ ] Exercise 3.3: Services
- [ ] Exercise 3.4: HPA
- [ ] Exercise 3.5: PDB
- [ ] Exercise 3.6: ConfigMaps/Secrets
- [ ] Exercise 4.1: Helm Installation
- [ ] Exercise 4.2: Custom Helm Chart
- [ ] Exercise 4.3: Helm Dependencies
- [ ] Exercise 4.4: Kustomize
- [ ] Exercise 4.5: Helm vs Kustomize
- [ ] Exercise 4.6: Publish Helm Chart
- [ ] Exercise 5.1: GitHub Actions CI
- [ ] Exercise 5.2: Terraform Pipeline
- [ ] Exercise 5.3: Helm Pipeline
- [ ] Exercise 5.4: Branch Protection
- [ ] Exercise 5.5: Multi-Environment
- [ ] Exercise 5.6: Complete Pipeline

## Phase 2 - Blockchain Node Ops
- [ ] Exercise 6.1: Install Geth
- [ ] Exercise 6.2: Dockerize Geth
- [ ] Exercise 6.3: NAT Configuration
- [ ] Exercise 6.4: Monitor Sync
- [ ] Exercise 6.5: Optimize Performance
- [ ] Exercise 6.6: Health Checks
- [ ] Exercise 7.1: Polygon Bor
- [ ] Exercise 7.2: Arbitrum Nitro
- [ ] Exercise 7.3: Multi-Node Cluster
- [ ] Exercise 9.1: Nginx Load Balancer
- [ ] Exercise 9.2: HAProxy
- [ ] Exercise 9.3: Health Checks
- [ ] Exercise 10.1: Prometheus Setup
- [ ] Exercise 10.2: Node Exporter
- [ ] Exercise 10.3: Custom Metrics
- [ ] Exercise 10.4: Grafana Dashboards
- [ ] Exercise 11.1: Alertmanager
- [ ] Exercise 11.2: Alert Rules
- [ ] Exercise 11.3: PagerDuty
- [ ] Exercise 11.4: Complete Stack

## Phase 3 - SRE Fundamentals
- [ ] Exercise 12.1: Read SRE Book
- [ ] Exercise 12.2: Define SLIs
- [ ] Exercise 12.3: Set SLOs
- [ ] Exercise 12.4: SLO Monitoring
- [ ] Exercise 13.1: Loki Setup
- [ ] Exercise 13.2: Tempo Setup
- [ ] Exercise 13.3: Unified Observability
- [ ] Exercise 14.1: Chaos Mesh
- [ ] Exercise 14.2: Pod Kill
- [ ] Exercise 14.3: Network Partition
- [ ] Exercise 14.4: Measure MTTR
- [ ] Exercise 15.1: Mock Incident
- [ ] Exercise 15.2: Post-Mortem
- [ ] Exercise 16.1: Opsgenie
- [ ] Exercise 16.2: Runbooks
- [ ] Exercise 17.1: Error Budgets
- [ ] Exercise 17.2: Budget Enforcement
- [ ] Exercise 17.3: Complete Playbook

## Phase 4 - Web3 DevOps
- [ ] Exercise 20.1: Ponder Setup
- [ ] Exercise 20.2: Index Contract Events
- [ ] Exercise 20.3: Query Ponder API
- [ ] Exercise 21.1: Complete Monorepo

## Phase 5 - Capstone
- [ ] Choose option (A, B, or C)
- [ ] Complete capstone project
```

---

**Good luck with your exercises! Track your progress and celebrate each milestone! 🎉**

