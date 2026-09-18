# Ruhi's Tetris — Docker & Kubernetes Deployment Project

A DevOps practice project demonstrating containerization, Kubernetes deployment, and CI/CD automation — built as a second, independent application proving that the shared AWS EKS infrastructure (originally built for [ruhi-devops-pipeline](https://github.com/AlRuh-ux/ruhi-devops-pipeline)) is genuinely reusable across multiple apps.

**App deployed:** A JavaScript Tetris clone (see credits below) — containerized and deployed as the workload for this project.

---

## What This Project Demonstrates

- **Containerization:** Docker image built and pushed to AWS ECR
- **Kubernetes deployment:** Helm chart deploying the app to an existing, shared AWS EKS cluster
- **Multi-app infrastructure:** Deployed alongside a separate app ([ruhi-2048](https://github.com/AlRuh-ux/ruhi-devops-pipeline)) on the **same** EKS cluster and **same** Load Balancer, routed by hostname via Ingress — with **zero changes** to the underlying Terraform infrastructure
- **CI/CD automation:** Independent Jenkins pipeline (Source → Build → Test → Deploy), configured via "Pipeline script from SCM" so the pipeline definition lives with the code

## Tech Stack

| Layer | Tool |
|---|---|
| Cloud Provider | AWS (shared cluster from [ruhi-devops-pipeline](https://github.com/AlRuh-ux/ruhi-devops-pipeline)) |
| Container Runtime | Docker |
| Container Registry | AWS ECR |
| Orchestration | Kubernetes (AWS EKS) |
| Package Manager | Helm |
| Ingress / Load Balancing | NGINX Ingress Controller + AWS Load Balancer (shared with the 2048 app) |
| CI/CD | Jenkins (Pipeline script from SCM) |
| Version Control | Git / GitHub |

## Repository Layout

```
.
├── Dockerfile           → builds the app container (nginx serving static files)
├── Jenkinsfile          → 4-stage CI/CD pipeline definition
├── deployment.yaml      → original raw Kubernetes manifest (kept for reference)
├── helm/
│   └── my-tetris-game/   → Helm chart used for the actual EKS deployment
├── index.html, stats.js, texture.jpg → the app itself (Tetris game)
```

## Pipeline Stages (Jenkinsfile)

1. **Source** — pulls the latest code from this GitHub repo
2. **Build** — builds the Docker image, authenticates to ECR, pushes the image
3. **Test** — runs the built image in a temporary container and verifies it serves the expected content
4. **Deploy** — runs `helm upgrade` against the shared EKS cluster, into the `my-tetris` namespace

## Status

✅ Complete — deployed successfully alongside a second independent app on the same cluster, full CI/CD pipeline verified working end-to-end.

## Why This Project Exists

This repo was built specifically to **prove infrastructure reusability**: rather than provisioning a brand-new cluster for every app, this project deploys a second, completely independent application to an already-existing EKS cluster — demonstrating a genuinely realistic, cost-efficient multi-tenant setup, exactly as real companies run many small services on shared infrastructure.

---

## Credits (original app)

This project deploys a **JavaScript Tetris** clone, originally created by [Jake Gordon](https://jakesgordon.com/). The game itself is largely unmodified — this project's focus is the containerization and deployment pipeline around it, not the game's code.

- [Play the original](https://jakesgordon.com/games/tetris/)
- [Read the original blog article](https://jakesgordon.com/writing/javascript-tetris/)
- [Original source](https://github.com/jakesgordon/javascript-tetris)

Licensed under the [MIT license](./LICENSE).