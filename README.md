# js-poc-csoc-app-catalog

Definitions-only library of reusable KRO `ResourceGraphDefinition` objects. All graph instances live in `js-poc-csoc-fleet`.

## Layout

```
rgds/
  kustomization.yaml        single Argo source entrypoint
  test-poc/                 tested OpenStack profile library
    configmaps/             write-once provider and spoke configuration blocks
    cluster/v1/             SpokeIdentity and SpokeCluster graphs
    compute/                Nova placement graphs
    network/                imported, isolated, shared-router, and routed graphs
    security/               Neutron security graphs
    storage/                Cinder storage graphs
    workloads/              conditional CSOC-local/CAPI addon workload graph
```

## RGD summary

| RGD | Kind | Purpose |
|-----|------|---------|
| `test-poc/configmaps/` | four config APIs | Account/service, spoke environment, exact topology, and shared-network write-once blocks |
| `test-poc/cluster/v1/` | `SpokeIdentity`, `SpokeCluster` | Account namespace, `OpenStackClusterIdentity`, and CAPI/CAPO spoke cluster |
| `test-poc/network/` | seven network kinds | Auto/shared/exact imports, isolated L2, shared-router subnet, or dedicated routed topologies |
| `test-poc/compute/` | `SpokeServerGroup` | Nova placement group using the immutable approved policy |
| `test-poc/security/` | `SpokeSecurityGroup` | Mutable, bounded Neutron workload ingress policy |
| `test-poc/storage/` | `SpokeVolume` | Managed Cinder volume using immutable type/AZ restrictions |
| `test-poc/workloads/hello-app.rgd.yaml` | `HelloApp` | Direct CSOC workload or CAPI `ClusterResourceSet`, selected by `spec.target` |

## Boundaries

- No RGD instances, credentials, account IDs, or fleet inventory here.
- No Argo `Application`, `ApplicationSet`, baseline, or observability packages.
- Imported OpenStack resources must use exact filters and `managementPolicy: unmanaged`.
- Application Services are internal OpenStack load balancers only — no floating IPs, no reuse of CAPO API load balancers.
- Worker `minNodes`/`maxNodes` are mutable `SpokeCluster` inputs; do not bake them into immutable config.
- An immutable ConfigMap is a handoff block, not a mutable settings store. Replace
  its graph instance only after every consumer has been retired.
- Every `kubectl kustomize rgds` render and `make validate` must pass before merging.
