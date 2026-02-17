# ArgoApplications-KaiserGuru
Helm charts for ArgoCD on rancher.kaiser.guru

## WordPress ApplicationSet

This repository contains an ArgoCD ApplicationSet that deploys multiple WordPress instances using the Bitnami WordPress Helm chart.

### Structure

```
wordpress-applicationset.yaml         # ArgoCD ApplicationSet definition
wordpress-instances/                  # Directory containing WordPress configurations
├── wordpress/                        # Configuration for 'wordpress' namespace
│   └── values.yaml
├── wordpress-flche/                  # Configuration for 'wordpress-flche' namespace
│   └── values.yaml
└── wordpress-catkaiserart/          # Configuration for 'wordpress-catkaiserart' namespace
    └── values.yaml
```

### How It Works

The ApplicationSet uses the **Git Directory Generator** to automatically discover WordPress instances:

1. It scans the `wordpress-instances/*` directory for subdirectories
2. For each subdirectory found, it creates an ArgoCD Application
3. Each Application:
   - Deploys Bitnami WordPress Helm chart (version 21.0.0)
   - Uses the `values.yaml` from the corresponding directory
   - Creates a namespace matching the directory name
   - Uses automatic sync with prune and self-heal enabled

### Deployment

To deploy this ApplicationSet to your ArgoCD instance:

```bash
kubectl apply -f wordpress-applicationset.yaml
```

This will create 3 WordPress instances in the following namespaces:
- `wordpress`
- `wordpress-flche`
- `wordpress-catkaiserart`

### Adding New WordPress Instances

To add a new WordPress instance:

1. Create a new directory under `wordpress-instances/` with your desired namespace name
2. Add a `values.yaml` file with your configuration
3. Commit and push the changes
4. ArgoCD will automatically detect and deploy the new instance

### Security Notice

⚠️ **IMPORTANT**: The provided `values.yaml` files contain placeholder passwords (`changeme`). These are **NOT** suitable for production use.

Before deploying to production:
- Change all passwords to strong, unique values
- Consider using Kubernetes secrets and `existingSecret` parameters
- Review and adjust all configuration values for your environment
