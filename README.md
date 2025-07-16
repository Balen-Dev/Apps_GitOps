# GitOps Greeting APIs

A GitOps-based deployment system for multiple greeting API applications using FluxCD, Helm, and Kustomize.

## 📋 Overview

This repository contains the GitOps configuration for deploying and managing four greeting API applications:

- **dotnet-greeting-api** (Port 8082)
- **go-greeting-api** (Port 8080)
- **nodejs-greeting-api** (Port 8081)
- **rails-greeting-api** (Port 3000)

## 🏗️ Architecture

### Technology Stack

- **GitOps**: FluxCD for continuous deployment
- **Templating**: Helm charts for application templates
- **Patching**: Kustomize for environment-specific configurations
- **Image Automation**: FluxCD Image Controller for automated image updates

### Key Features

- ✅ **Single Helm Chart**: Reusable template for all greeting APIs
- ✅ **Environment-Specific Overrides**: Easy replica count adjustments per environment
- ✅ **Automated Image Updates**: Automatic deployment of new container images
- ✅ **GitOps Workflow**: All changes tracked in Git
- ✅ **Consistent Deployment**: Standardized configuration across all applications

## 📁 Project Structure

```
Apps_GitOps/
├── charts/
│   └── greeting-api/                    # Reusable Helm chart
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
│           ├── deployment.yaml
│           ├── service.yaml
│           └── _helpers.tpl
├── apps/
│   ├── dotnet_greeting_api/
│   │   ├── base/
│   │   │   ├── release.yaml             # HelmRelease resource
│   │   │   └── kustomization.yaml       # Base kustomization
│   │   ├── dev/
│   │   │   ├── patches.yaml             # Environment-specific patches
│   │   │   └── kustomization.yaml       # Dev overlay
│   │   ├── prod/
│   │   │   ├── patches.yaml             # Production patches
│   │   │   └── kustomization.yaml       # Prod overlay
│   │   └── image-automation/
│   │       ├── imagepolicy.yaml         # Image versioning policy
│   │       ├── imagerepo.yaml           # Image repository scanning
│   │       └── imageupdateautomation.yaml # Automated image updates
│   ├── go_greeting_api/                 # Same structure as dotnet
│   ├── nodejs_greeting_api/             # Same structure as dotnet
│   └── rails_greeting_api/              # Same structure as dotnet
└── README.md
```

## 🚀 How It Works

### 1. Application Deployment

Each application uses a **HelmRelease** resource that:

- References the shared `greeting-api` Helm chart
- Provides app-specific values (image, ports, environment variables)
- Deploys to the `greeting-apis` namespace

### 2. Environment Management

**Kustomize patches** handle environment-specific configurations:

### 3. Image Automation

FluxCD automatically updates applications when new images are available:

- **ImageRepository**: Scans Docker registry for new images
- **ImagePolicy**: Determines which image version to use (semver)
- **ImageUpdateAutomation**: Updates the HelmRelease with new image tags

## 📊 Application Configuration

| Application    | Image Repository                   | Port | Environment Variables |
| -------------- | ---------------------------------- | ---- | --------------------- |
| **dotnet-api** | `balenabdalla/dotnet-greeting-api` | 8082 | None                  |
| **go-api**     | `balenabdalla/go-greeting-api`     | 8080 | None                  |
| **nodejs-api** | `balenabdalla/nodejs-greeting-api` | 8081 | None                  |
| **rails-api**  | `balenabdalla/rails-greeting-api`  | 3000 | `SECRET_KEY_BASE`     |

**Common Configuration:**

- Namespace: `greeting-apis`
- Service Port: `80` (external)
- Image Pull Policy: `IfNotPresent`
- Service Type: `ClusterIP`

## 🔧 Prerequisites

- Kubernetes cluster with FluxCD installed
- Helm 3.x
- kubectl configured for your cluster
- Access to the container registry (`balenabdalla/*`)

## 📈 Monitoring & Management

### Check Application Status

```bash
# Check HelmRelease status
flux get helmreleases -n greeting-apis

# Check application pods
kubectl get pods -n greeting-apis

# Check services
kubectl get services -n greeting-apis
```

### Monitor Image Automation

```bash
# Check image policies
flux get image policy -n flux-system

# Check image repositories
flux get image repository -n flux-system

# Check image update automations
flux get image update -n flux-system
```

## 🔄 Adding New Environments

To add a new environment (e.g., `staging`):

1. **Create staging directory**:

   ```bash
   mkdir -p apps/dotnet_greeting_api/staging
   ```

2. **Create patches.yaml**:

   ```yaml
   # apps/dotnet_greeting_api/staging/patches.yaml
   - op: replace
     path: /spec/values/replicaCount
     value: 1
   ```

3. **Create kustomization.yaml**:

   ```yaml
   # apps/dotnet_greeting_api/staging/kustomization.yaml
   resources:
     - ../base

   patches:
     - target:
         kind: HelmRelease
         name: dotnet-greeting-api
       path: patches.yaml
   ```

4. **Deploy**:
   ```bash
   kubectl apply -k apps/dotnet_greeting_api/staging
   ```

## 🛠️ Customization

### Modify Application Configuration

Edit the `base/release.yaml` file for each application to change:

- Resource limits/requests
- Environment variables
- Service configuration
- Image repository

### Update Helm Chart

Modify files in `charts/greeting-api/` to:

- Add new Kubernetes resources
- Change default values
- Update template logic

### Environment-Specific Changes

Edit `patches.yaml` files to override:

- Replica counts
- Resource limits
- Environment variables
- Any other HelmRelease values

## 🔍 Troubleshooting

### Common Issues

1. **HelmRelease not deploying**:

   ```bash
   kubectl describe helmrelease <app-name> -n greeting-apis
   ```

2. **Image automation not working**:

   ```bash
   flux logs --follow --tail=50 --all-namespaces
   ```

3. **Kustomize patches not applying**:
   ```bash
   kubectl kustomize apps/<app-name>/dev
   ```

### Useful Commands

```bash
# Test Helm chart locally
helm template greeting-api charts/greeting-api --values charts/greeting-api/values.yaml

# Validate kustomization
kubectl kustomize apps/dotnet_greeting_api/dev

# Force image update
flux reconcile image update dotnet-greeting-api -n flux-system

# Check FluxCD logs
flux logs --follow --tail=50 --all-namespaces
```

---

**Note**: This is a GitOps repository. All changes should be made through Git commits, not direct kubectl commands, to maintain the GitOps workflow.
