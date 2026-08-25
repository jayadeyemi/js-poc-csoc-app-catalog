# js-poc-csoc-app-catalog

Shared KRO definitions, trusted CSOC instances/configuration, and application payloads. ApplicationSets in `js-poc-csoc-bootstrap` reference application directories using label selectors. Fleet inventory remains separate.

GitHub: `github.com/jayadeyemi/js-poc-csoc-app-catalog`

## Repo layout

```
rgds/                 grouped identity, network, cluster, and direct-workload RGDs
csoc/                 trusted CSOC instances and immutable configuration blocks
hello-app/            opt-in spoke workload
security/             installed when security=enabled
observability/        installed when observability=enabled
```

The `rgds/` and `csoc/` trees are management-cluster Applications. Workload directories such as `hello-app/` are selected for registered spokes by ApplicationSets.

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

## Trusted CSOC package

Minimum contents:
```
csoc/
  kustomization.yaml
  namespaces.yaml        CSOC-managed namespaces
  network-policies.yaml  default deny + allow rules
  config/                manually applied immutable account/provider blocks
  rgd-apps/              trusted RGD instances
```

Provider UUIDs and account project IDs may appear only in reviewed immutable ConfigMaps under `csoc/config/`; credentials never appear in Git.

## Pitfalls

- **Cluster selection is done by ApplicationSets in `js-poc-csoc-bootstrap`**, not here. Do not add cluster selectors inside this repo.
- **Do not put `SpokeCluster` CRs or fleet inventory here.** That belongs in `js-poc-csoc-fleet`.
- **Application version upgrades** (e.g. a new Gen3 release) are a change to this repo only; shared RGD definitions remain grouped under `rgds/`. Keep application payload and cluster lifecycle changes reviewable independently.
- `kustomize build` must succeed with no errors before merging.
