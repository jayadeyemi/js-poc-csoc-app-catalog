# js-poc-csoc-app-catalog

Helm/Kustomize package definitions for every application the CSOC installs on spoke clusters. ApplicationSets in `js-poc-csoc-bootstrap` reference directories here using label selectors. No cluster inventory, no RGDs, no scripts.

GitHub: `github.com/jayadeyemi/js-poc-csoc-app-catalog`

## Repo layout

```
baseline/             installed on ALL spokes (selector: type=spoke)
security/             installed when security=enabled
observability/        installed when observability=enabled
gen3/                 installed when gen3=enabled
internal-tools/       CSOC-internal tooling only
```

Each top-level directory maps to one Argo CD ApplicationSet selector in `js-poc-csoc-bootstrap/argocd/applicationsets/`.

## Directory conventions

Each capability directory must contain either:

**Kustomize** (preferred for manifests):
```
<capability>/
  kustomization.yaml    base resources list
  <resource>.yaml       individual manifests
```

**Helm** (for third-party charts):
```
<capability>/
  kustomization.yaml    HelmChart resource or helmCharts entry
  values.yaml           chart values
```

Both are valid. Use Kustomize for CSOC-authored resources; use Helm for upstream charts.

## Validation

```bash
# Validate a capability package (requires kustomize)
kustomize build <capability>/

# Helm template dry-run (if using Helm)
helm template <release-name> <chart> -f <capability>/values.yaml
```

## Adding a new capability

1. Create a top-level directory: `<capability>/kustomization.yaml`
2. Add the corresponding ApplicationSet in `js-poc-csoc-bootstrap/argocd/applicationsets/<capability>.yaml`
3. Add the cluster label `csoc.js2.org/<capability>: enabled` to clusters that should receive it (via `js-poc-csoc-fleet` cluster.yaml `registration.labels`)

## Baseline package (required on all spokes)

Minimum contents:
```
baseline/
  kustomization.yaml
  namespaces.yaml        CSOC-managed namespaces
  network-policies.yaml  default deny + allow rules
```

Do not add optional tooling to `baseline/` — put it in a capability directory instead.

## Pitfalls

- **Cluster selection is done by ApplicationSets in `js-poc-csoc-bootstrap`**, not here. Do not add cluster selectors inside this repo.
- **Do not put `SpokeCluster` CRs or fleet inventory here.** That belongs in `js-poc-csoc-fleet`.
- **Application version upgrades** (e.g. a new Gen3 release) are a change to this repo only — they do not affect `js-poc-csoc-platform-apis` or cluster lifecycle. Keep app and cluster lifecycle separate.
- `kustomize build` must succeed with no errors before merging.
