---
applyTo: "**"
---
# js-poc-csoc-app-catalog conventions

## Directory = capability = ApplicationSet selector

Every top-level directory maps 1:1 to an Argo CD ApplicationSet in `js-poc-csoc-bootstrap`. The directory name determines the label suffix.

| Directory | Selector |
|-----------|----------|
| `baseline/` | `csoc.js2.org/type: spoke` (all spokes — non-optional) |
| `security/` | `csoc.js2.org/security: enabled` |
| `observability/` | `csoc.js2.org/observability: enabled` |
| `gen3/` | `csoc.js2.org/gen3: enabled` |

## kustomization.yaml is required

Every top-level capability directory must have a `kustomization.yaml` — this is the Argo CD Kustomize source entrypoint.

## Validation gate

```bash
kustomize build <capability>/
```

No clean build = no merge.

## Content boundaries

- This repo: application definitions (what to install)
- `js-poc-csoc-fleet`: which clusters get which capability (via labels)
- `js-poc-csoc-bootstrap/argocd/applicationsets/`: the ApplicationSets that wire them together
