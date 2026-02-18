# ArgoApplications-KaiserGuru

Helm charts for ArgoCD on rancher.kaiser.guru

## Overview

This repository contains ArgoCD ApplicationSets that deploy multiple instances of different applications using Helm charts. All ApplicationSets use a matrix generator pattern to combine directory discovery with per-instance configuration files.

## Main Application

### applicationsets-application.yaml

An ArgoCD Application that manages all ApplicationSets in this repository. This allows all ApplicationSets to be deployed and kept in sync through a single application.

## ApplicationSets

### WordPress ApplicationSet

**File**: `wordpress-applicationset.yaml`

Deploys multiple WordPress instances using the Bitnami WordPress Helm chart (version 28.1.2).

**Structure**:
```
wordpress-instances/
├── wordpress-kaiserguru/
│   ├── values.yaml
│   └── config.json
├── wordpress-flche/
│   ├── values.yaml
│   └── config.json
└── wordpress-catkaiserart/
    ├── values.yaml
    └── config.json
```

**Features**:
- Each instance specifies its name, destination server, and namespace via `config.json`
- Each instance creates its own namespace
- Automatic namespace creation enabled

### MinIO ApplicationSet

**File**: `minio-applicationset.yaml`

Deploys MinIO object storage instances using the Bitnami MinIO Helm chart (version 15.0.3).

**Structure**:
```
minio-instances/
└── minio/
    ├── values.yaml
    └── config.json
```

**Features**:
- Standalone mode by default
- Each instance specifies its name, destination server, and namespace via `config.json`
- Persistent storage (10Gi by default)
- Uses Bitnami registry (registry-1.docker.io)

### Docker Registry ApplicationSet

**File**: `docker-registry-applicationset.yaml`

Deploys Docker Registry instances using the docker-registry Helm chart (version 3.0.0).

**Structure**:
```
docker-registry-instances/
└── docker-registry/
    ├── values.yaml
    └── config.json
```

**Features**:
- Each instance specifies its name, destination server, and namespace via `config.json`
- Includes ingress configuration for `registry.kaiser.guru`
- Persistent storage (50Gi with NFS StorageClass)
- Garbage collection and deletion enabled
- htpasswd authentication configured

## Configuration

Each instance uses a `config.json` file to specify:

- **name**: The ArgoCD Application name
- **server**: The Kubernetes cluster API server URL (e.g., `https://kubernetes.default.svc`)
- **namespace**: The target namespace for deployment

**Example config.json**:
```json
{
  "name": "my-app-instance",
  "server": "https://kubernetes.default.svc",
  "namespace": "my-app"
}
```

## Adding New Instances

To add a new instance of any application:

1. Create a new directory under the corresponding `*-instances/` folder
2. Add a `values.yaml` file with your Helm chart configuration
3. Add a `config.json` file specifying the name, destination server, and namespace:
   ```json
   {
     "name": "my-instance-name",
     "server": "https://kubernetes.default.svc",
     "namespace": "my-namespace"
   }
   ```
4. Commit and push the changes
5. ArgoCD will automatically detect and deploy the new instance

## Security Notice

⚠️ **IMPORTANT**: The provided configuration files contain default/placeholder credentials:
- WordPress: `admin` / `changeme`
- MinIO: `admin` / `changeme`
- Docker Registry: htpasswd entry for `mkaiser`

These are **NOT** suitable for production use.

Before deploying to production:
- Change all passwords to strong, unique values
- Consider using Kubernetes secrets for sensitive data
- Review and adjust all configuration values for your environment
- Enable TLS for all services
- Implement proper access controls
