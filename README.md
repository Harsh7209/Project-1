<div align="center">

# 🏦 AI-Powered Banking App — GitOps on AWS EKS

### Production-Grade DevOps | GitOps | Cloud-Native | CI/CD

[![CI Pipeline](https://img.shields.io/github/actions/workflow/status/yourusername/ai-banking-gitops/ci.yml?branch=main&label=CI%20Pipeline&logo=github-actions&logoColor=white&style=for-the-badge)](https://github.com/yourusername/ai-banking-gitops/actions)
[![ArgoCD](https://img.shields.io/badge/GitOps-ArgoCD-EF7B4D?style=for-the-badge&logo=argo&logoColor=white)](https://argoproj.github.io/cd/)
[![AWS EKS](https://img.shields.io/badge/Cloud-AWS%20EKS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)](https://aws.amazon.com/eks/)
[![Terraform](https://img.shields.io/badge/IaC-Terraform-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)](https://www.terraform.io/)
[![Kubernetes](https://img.shields.io/badge/Orchestration-Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)](https://kubernetes.io/)
[![Spring Boot](https://img.shields.io/badge/Backend-Spring%20Boot-6DB33F?style=for-the-badge&logo=springboot&logoColor=white)](https://spring.io/projects/spring-boot)
[![Docker](https://img.shields.io/badge/Container-Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Prometheus](https://img.shields.io/badge/Monitoring-Prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)](https://prometheus.io/)
[![Grafana](https://img.shields.io/badge/Dashboards-Grafana-F46800?style=for-the-badge&logo=grafana&logoColor=white)](https://grafana.com/)
[![Ollama](https://img.shields.io/badge/AI-Ollama-000000?style=for-the-badge&logo=ollama&logoColor=white)](https://ollama.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

<br/>

> **A fully automated, production-grade GitOps pipeline deploying an AI-powered banking application on AWS EKS — showcasing end-to-end DevOps engineering: Infrastructure as Code, containerization, CI/CD automation, GitOps delivery, and real-time observability.**

<br/>

[🚀 Architecture](#-system-architecture) · [⚙️ CI/CD Pipeline](#️-cicd-pipeline) · [🛠️ Tech Stack](#️-tech-stack) · [📁 Project Structure](#-project-structure) · [🚢 Deployment](#-deployment-guide) · [📊 Monitoring](#-monitoring--observability) · [📚 Learnings](#-key-learnings)

</div>

---

## 📌 Project Overview

This project is a **DevOps and Cloud Engineering showcase** built around an AI-powered banking application. While the original Spring Boot banking application concept served as the base, **the entire DevOps lifecycle was designed and implemented from scratch** — covering containerization, cloud provisioning, Kubernetes orchestration, GitOps-driven continuous delivery, and full-stack observability.

> 💡 **Core Focus:** The primary value of this project lies in its **infrastructure, automation, and operational excellence** — not the application logic.

### What Makes This Project Stand Out

| Area | Implementation |
|---|---|
| **Infrastructure as Code** | 100% Terraform-provisioned AWS infrastructure — zero manual cloud console clicks |
| **GitOps Delivery** | Argo CD watches the Git repo; every merge to `main` triggers an automated EKS deployment |
| **CI Automation** | GitHub Actions builds, tests, and pushes Docker images with semantic versioning |
| **Observability** | Prometheus scrapes live Kubernetes metrics; Grafana dashboards surface cluster health |
| **AI Integration** | Ollama LLM runs as a sidecar-style service, handling natural language banking queries |
| **Production Patterns** | Namespace isolation, resource limits, liveness/readiness probes, rolling updates |

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                          DEVELOPER WORKFLOW                         │
│                                                                     │
│   git push  ──►  GitHub Actions CI  ──►  Docker Hub / ECR          │
│                        │                        │                  │
│                  (Build + Test)          (Image Tagged)             │
│                        │                        │                  │
│                  Updates K8s Manifests           │                  │
│                        │                        │                  │
│                        ▼                        │                  │
│                   Git Repo (Manifests)           │                  │
│                        │                        │                  │
└────────────────────────┼────────────────────────┼──────────────────┘
                         │                        │
                         ▼                        ▼
┌─────────────────────────────────────────────────────────────────────┐
│                         AWS CLOUD (EKS)                             │
│                                                                     │
│   ┌─────────────┐     ┌─────────────────────────────────────────┐  │
│   │   Argo CD   │────►│             EKS Cluster                 │  │
│   │  (GitOps)   │     │  ┌──────────────────────────────────┐  │  │
│   └─────────────┘     │  │          banking-app NS           │  │  │
│                        │  │  ┌────────────┐  ┌────────────┐  │  │  │
│   ┌─────────────┐     │  │  │  Spring    │  │   Ollama   │  │  │  │
│   │ Prometheus  │────►│  │  │  Boot Pod  │  │   AI Pod   │  │  │  │
│   │  + Grafana  │     │  │  └────────────┘  └────────────┘  │  │  │
│   └─────────────┘     │  └──────────────────────────────────┘  │  │
│                        │                                         │  │
│   ┌─────────────┐     │  ┌──────────────────────────────────┐  │  │
│   │  Terraform  │────►│  │    AWS Load Balancer (Ingress)   │  │  │
│   │    (IaC)    │     │  └──────────────────────────────────┘  │  │
│   └─────────────┘     └─────────────────────────────────────────┘  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### GitOps Flow — Mermaid Diagram

```mermaid
flowchart TD
    Dev["👨‍💻 Developer\npushes code"] --> GH["GitHub\nRepository"]

    GH --> CI["⚙️ GitHub Actions\nCI Pipeline"]

    CI --> Build["🔨 Build &\nUnit Test"]
    Build --> Docker["🐳 Build Docker\nImage"]
    Docker --> Push["📦 Push to\nDocker Hub / ECR"]
    Push --> Manifest["📝 Update K8s\nManifest (image tag)"]
    Manifest --> GitCommit["💾 Commit Manifest\nto Git Repo"]

    GitCommit --> ArgoCD["🔄 Argo CD\nDetects Drift"]
    ArgoCD --> Sync["🔁 Auto-Sync\nto EKS"]

    Sync --> EKS["☸️ AWS EKS\nCluster"]

    EKS --> App["🏦 Banking App\n(Spring Boot Pods)"]
    EKS --> AI["🤖 Ollama\nAI Service"]
    EKS --> LB["🌐 AWS Load\nBalancer (Ingress)"]

    EKS --> Prom["📈 Prometheus\nScrapes Metrics"]
    Prom --> Graf["📊 Grafana\nDashboards"]

    style Dev fill:#4A90D9,color:#fff
    style CI fill:#2088FF,color:#fff
    style ArgoCD fill:#EF7B4D,color:#fff
    style EKS fill:#FF9900,color:#fff
    style Graf fill:#F46800,color:#fff
```

---

## 🛠️ Tech Stack

<table>
  <thead>
    <tr>
      <th>Layer</th>
      <th>Technology</th>
      <th>Purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><b>Application</b></td>
      <td>Java 17 + Spring Boot 3</td>
      <td>RESTful banking backend, business logic</td>
    </tr>
    <tr>
      <td><b>AI / LLM</b></td>
      <td>Ollama</td>
      <td>Local LLM inference for AI-powered banking queries</td>
    </tr>
    <tr>
      <td><b>Containerization</b></td>
      <td>Docker + Docker Compose</td>
      <td>Reproducible, portable application packaging</td>
    </tr>
    <tr>
      <td><b>Container Orchestration</b></td>
      <td>Kubernetes (K8s)</td>
      <td>Workload scheduling, scaling, self-healing</td>
    </tr>
    <tr>
      <td><b>Cloud Platform</b></td>
      <td>AWS EKS</td>
      <td>Managed Kubernetes control plane on AWS</td>
    </tr>
    <tr>
      <td><b>Infrastructure as Code</b></td>
      <td>Terraform</td>
      <td>Declarative AWS resource provisioning (VPC, EKS, IAM, etc.)</td>
    </tr>
    <tr>
      <td><b>CI Pipeline</b></td>
      <td>GitHub Actions</td>
      <td>Automated build, test, image push, manifest update</td>
    </tr>
    <tr>
      <td><b>CD / GitOps</b></td>
      <td>Argo CD</td>
      <td>Declarative, Git-driven continuous delivery to EKS</td>
    </tr>
    <tr>
      <td><b>Metrics Collection</b></td>
      <td>Prometheus</td>
      <td>Kubernetes & app-level metrics scraping</td>
    </tr>
    <tr>
      <td><b>Visualization</b></td>
      <td>Grafana</td>
      <td>Real-time dashboards, alerting, SLO tracking</td>
    </tr>
    <tr>
      <td><b>Image Registry</b></td>
      <td>Docker Hub / AWS ECR</td>
      <td>Versioned container image storage</td>
    </tr>
    <tr>
      <td><b>VCS</b></td>
      <td>GitHub</td>
      <td>Source of truth for both app code and K8s manifests</td>
    </tr>
  </tbody>
</table>

---

## 📁 Project Structure

```
ai-banking-gitops/
│
├── 📁 app/                          # Spring Boot Application
│   ├── src/
│   │   ├── main/java/com/banking/
│   │   │   ├── controller/          # REST API endpoints
│   │   │   ├── service/             # Business logic + Ollama AI service
│   │   │   ├── model/               # Domain models
│   │   │   └── config/              # App & AI configuration
│   │   └── resources/
│   │       └── application.yml
│   ├── Dockerfile                   # Multi-stage Docker build
│   └── pom.xml
│
├── 📁 terraform/                    # Infrastructure as Code
│   ├── main.tf                      # Root module — EKS, VPC, IAM
│   ├── variables.tf                 # Input variables
│   ├── outputs.tf                   # Cluster outputs (endpoint, kubeconfig)
│   ├── vpc.tf                       # VPC, subnets, route tables
│   ├── eks.tf                       # EKS cluster + managed node groups
│   └── iam.tf                       # IRSA roles, node instance profiles
│
├── 📁 k8s/                          # Kubernetes Manifests (GitOps source of truth)
│   ├── namespace.yaml
│   ├── deployment.yaml              # App deployment with probes & resource limits
│   ├── service.yaml                 # ClusterIP / LoadBalancer service
│   ├── ingress.yaml                 # AWS ALB Ingress
│   ├── configmap.yaml
│   ├── hpa.yaml                     # Horizontal Pod Autoscaler
│   └── ollama/
│       ├── deployment.yaml          # Ollama AI service deployment
│       └── service.yaml
│
├── 📁 argocd/                       # Argo CD Application manifests
│   └── application.yaml             # Argo CD App-of-Apps config
│
├── 📁 monitoring/                   # Observability stack
│   ├── prometheus/
│   │   ├── values.yaml              # kube-prometheus-stack Helm values
│   │   └── alerting-rules.yaml
│   └── grafana/
│       └── dashboards/
│           ├── kubernetes-cluster.json
│           └── banking-app.json
│
├── 📁 .github/
│   └── workflows/
│       ├── ci.yml                   # CI: Build → Test → Push → Update manifest
│       └── terraform-plan.yml       # Terraform plan on PR
│
├── docker-compose.yml               # Local development environment
└── README.md
```

---

## ⚙️ CI/CD Pipeline

The project implements a **fully automated GitOps delivery pipeline** with a clear separation of concerns between CI (GitHub Actions) and CD (Argo CD).

### Pipeline Overview

```mermaid
sequenceDiagram
    participant Dev as 👨‍💻 Developer
    participant GH  as GitHub
    participant CI  as GitHub Actions
    participant Reg as Docker Registry
    participant Repo as Manifest Repo
    participant Argo as Argo CD
    participant EKS as AWS EKS

    Dev->>GH: git push / merge PR
    GH->>CI: Trigger CI Workflow
    CI->>CI: Run Unit Tests (Maven)
    CI->>CI: Build Docker Image (tag: sha-XXXXXXX)
    CI->>Reg: Push Image to Registry
    CI->>Repo: Update image tag in deployment.yaml
    Repo->>Argo: Drift detected (new commit)
    Argo->>EKS: Sync — Rolling Deployment
    EKS-->>Argo: Deployment Healthy ✅
    Argo-->>Dev: Slack / Email notification
```




## 📊 Monitoring & Observability

The observability stack is built on the **kube-prometheus-stack**, providing full visibility into cluster health and application performance.

### Metrics Collected

| Category | Metrics |
|---|---|
| **Cluster** | Node CPU/Memory utilization, pod restarts, network I/O |
| **Application** | JVM heap, HTTP request rate, response times, error rates |
| **Kubernetes** | Deployment replica status, HPA scaling events |
| **Ollama AI** | Model inference latency, request queue depth |

### Grafana Dashboards

```
📊 Kubernetes Cluster Overview
   ├── Node resource utilization
   ├── Namespace-level CPU/Memory breakdown
   └── Pod status and restart counts

📊 Banking Application Dashboard
   ├── HTTP request throughput (req/s)
   ├── P50 / P95 / P99 response latencies
   ├── JVM metrics (heap, GC pause times)
   └── Active sessions & error rate

📊 AI Service Dashboard
   ├── Ollama inference requests per minute
   └── Model response latency distribution
```


## 📚 Key Learnings

Working through this project end-to-end produced deep, hands-on understanding of:

- **GitOps Mental Model** — Treating Git as the single source of truth for infrastructure and application state enforces discipline, auditability, and rollback simplicity. Argo CD's drift detection is a game-changer for operational stability.

- **Kubernetes at Depth** — Moving beyond basic deployments to configure liveness/readiness probes, HPAs, resource requests/limits, and namespace RBAC revealed how much operational safety Kubernetes can provide when configured correctly.

- **Terraform State Management** — Managing remote state with S3 + DynamoDB locking taught the criticality of state isolation, especially when collaborating or managing multiple environments.

- **CI/CD Separation of Concerns** — Keeping CI (code quality, image building) and CD (deployment) as separate concerns makes pipelines more reliable, auditable, and independently scalable.

- **Observability-First Thinking** — Shipping without metrics is shipping blind. Building the Prometheus + Grafana stack early changed how I thought about deployments — visibility before velocity.

- **IaC Discipline** — Zero manual cloud console interactions enforced a reproducible, version-controlled infrastructure that can be torn down and rebuilt in minutes.

---

## 🔭 Future Improvements

- [ ] **Multi-environment GitOps** — Separate `dev` / `staging` / `prod` Argo CD ApplicationSets with promotion gates
- [ ] **Helm Chart Migration** — Convert raw K8s manifests to parameterized Helm charts for environment-specific overrides
- [ ] **Secrets Management** — Integrate AWS Secrets Manager or HashiCorp Vault via External Secrets Operator (ESO)
- [ ] **Service Mesh** — Add Istio or Linkerd for mTLS, advanced traffic shaping, and distributed tracing
- [ ] **Canary Deployments** — Implement progressive delivery using Argo Rollouts
- [ ] **SLO Alerting** — Define SLIs/SLOs and wire multi-window, multi-burn-rate alerts in Prometheus
- [ ] **Terraform Modules** — Refactor infrastructure into reusable, versioned modules published to Terraform Registry
- [ ] **Cost Optimization** — Integrate AWS Spot instances and Karpenter for node autoprovisioning
- [ ] **DORA Metrics** — Instrument deployment frequency, lead time, MTTR, and change failure rate

---

## 🤝 Attribution

The base banking application logic and Spring Boot codebase were adapted from an existing open-source project. All **DevOps, cloud infrastructure, CI/CD, GitOps, and observability implementation** — including Dockerization, Terraform, EKS provisioning, GitHub Actions pipelines, Argo CD configuration, and the full monitoring stack — were **designed, built, and documented independently** as part of this portfolio project.

---

## 👤 Author

<div align="center">

**Harsh Choubey**




*Open to DevOps, Platform Engineering, and Cloud Infrastructure roles.*

</div>

---



---

<div align="center">

**⭐ If this project helped you learn or served as inspiration, please consider starring the repo!**

*Built with precision. Deployed with confidence. Monitored with clarity.*

</div>
