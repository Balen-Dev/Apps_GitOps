# 🚀 GitOps Apps Repository

This repository contains application configurations and source code for my GitOps deployment pipeline.

## 📁 Repository Structure

```
GitOps_Apps/
├── apps/
│ ├── base/
│ │ ├── dotnet/
│ │ ├── go/
│ │ ├── nodejs/
│ │ └── rails/
│ ├── dev/
│ │ ├── dotnet/
│ │ ├── go/
│ │ ├── nodejs/
│ │ └── rails/
│ └── image-automation/
│ ├── go-imagepolicy.yaml
│ └── go-imagerepo.yaml
├── services/
│ └── go/
│ ├── api.go
│ ├── Dockerfile
│ └── go.mod
├── .github/
│ └── workflows/
│ └── go.yaml
└── README.md
```

## 🔄 How It Works

1. **Application Source Code**: Located in `services/` directory
2. **Kubernetes Configurations**: Located in `apps/` directory using Kustomize
3. **CI/CD Pipeline**: GitHub Actions builds and pushes Docker images
4. **GitOps Deployment**: FluxCD automatically detects new images and updates deployments

## 🚀 Deployment Flow

1. Code changes pushed to `main` branch
2. GitHub Actions runs tests and builds Docker image
3. Image pushed to Docker Hub with latest tag and commit SHA
4. FluxCD (running in the infrastructure repository) detects new image
5. FluxCD automatically updates the deployment in the Kubernetes cluster

   ## 🔗 Related Repository

- [GitOps_Infra](https://github.com/Balen-Dev/GitOps_Infra) - Infrastructure and cluster management
