# ktcloud-msa-values

Per-service Helm **values overlays** for the KTCloud MSA platform. One folder
per microservice; each holds a `values-release.yaml` consumed by the shared
chart (`ktcloud-msa/ktcloud-msa-chart`) through ArgoCD's multi-source `$values`
ref in the argocd manifest repo's `applications/appset.yaml`.

```
_template/                       scaffold for a new service (never deployed)
ktcloud-msa-auth-service/
ktcloud-msa-product-service/
ktcloud-msa-inventory-service/
ktcloud-msa-order-service/
ktcloud-msa-user-api-gateway/
ktcloud-msa-frontend/
```

## Conventions

- **Folder name = the `app:` value** in `applications/appset.yaml`. The ArgoCD
  Application name becomes `<folder>-release`, and inter-service gRPC addressing
  depends on that `-release` suffix.
- `image.tag` is a **commit SHA pushed by the upstream service repo** — to
  deploy a new version, bump it and push; ArgoCD auto-syncs.
- Everything under `appConfig:` is mounted at `/config/application.yaml`; it is
  the only config-injection path into the Spring Boot container.

## Adding a service

1. `cp -r _template ktcloud-msa-<service>` and fill in the placeholders.
2. Add the `app:` entry to `applications/appset.yaml` (argocd repo).
3. Add a `dorny/paths-filter` entry in `.github/workflows/ci.yaml`.
4. Extend the Postgres init script if the service needs its own database.
