# Hello Health Service Manifests

This repository contains Kustomize manifests for the hello-health-service application, inheriting from the platform global configuration.

## Purpose

- Define hello-health-service specific deployment configuration
- Inherit platform-wide bases and components
- Apply service-specific patches for each environment (dev, staging, prod)
- Maintain clean separation between app logic and infrastructure config

## Repository Structure

```text
hello-health-service-manifests/
  base/
    kustomization.yaml
    service-values.yaml
  overlays/
    dev/
      kustomization.yaml
      patches/
        deployment-patch.yaml
        service-patch.yaml
    staging/
      kustomization.yaml
      patches/
        deployment-patch.yaml
        service-patch.yaml
    prod/
      kustomization.yaml
      patches/
        deployment-patch.yaml
        service-patch.yaml
  README.md
```

## Usage

### Local Testing

```bash
kustomize build overlays/dev
kustomize build overlays/staging
kustomize build overlays/prod
```

### Via Argo CD

The ApplicationSet in hello-argocd-bootstrap automatically discovers this repo and creates Applications:

```bash
# This repo will be discovered and synced automatically
# by the ApplicationSet in hello-argocd-bootstrap
```

### Manual Sync

```bash
kubectl apply -k overlays/prod
```

## Environments

### dev
- 1 replica
- Debug logging
- Development image tag
- No resource limits

### staging
- 3 replicas
- Info logging
- Staging image tag
- CPU: 100m-500m, Memory: 128Mi-512Mi

### prod
- 3 replicas
- Warning logging
- Production image tag
- CPU: 100m-500m, Memory: 128Mi-512Mi
- Health checks enabled

## Customization

### Adding Service-Specific Configuration

Edit [base/service-values.yaml](base/service-values.yaml) to configure:
- Service name
- Application name
- Port configuration
- Default environment variables

### Adding Environment-Specific Patches

Add patches in each overlay's `patches/` directory for environment-specific:
- Resource configurations
- Environment variables
- Image registries
- DNS names

## Integration with Platform Config

This repo references [hello-platform-global-config](../hello-platform-global-config) for:
- Base Deployment and Service templates
- Reusable components (labels, replicas, resource-limits, health-checks)
- Environment overlays

```yaml
bases:
  - github.com/geekahmed-yt/hello-platform-global-config/overlays/prod
```

## Best Practices

1. **Use platform components** instead of duplicating them in this repo
2. **Keep patches minimal** - only override what is different per environment
3. **Document environment-specific values** in this README
4. **Version your images** - use explicit tags, not `latest`
5. **Test locally** with kustomize build before pushing

## Prerequisites

- Kustomize 4.0+
- Access to hello-platform-global-config repository
- Argo CD configured with hello-argocd-bootstrap
