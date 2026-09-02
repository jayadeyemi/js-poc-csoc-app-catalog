# js-poc-csoc-app-catalog

Definitions-only library of reusable KRO `ResourceGraphDefinition` objects. All graph instances live in `js-poc-csoc-fleet`.

## Layout

```
rgds/
  kustomization.yaml        single Argo source entrypoint
  v2-hubs/                       current one-file-per-RGD API generation
    infrastructure/         account, cluster, network, pool, and foundation APIs
    bindings/               application, data, addon, endpoint, and auth contracts
    services/               typed workload delivery APIs
  v1-samples/                 tested OpenStack profile library
    configmaps/             write-once provider and spoke configuration blocks
    cluster/v1/             SpokeIdentity and SpokeCluster graphs
    compute/                Nova placement graphs
    network/                imported, isolated, shared-router, and routed graphs
    security/               Neutron security graphs
    storage/                Cinder storage graphs
    workloads/              CSOC-local, centrally delivered, and spoke GitOps graphs
```

## RGD summary

| RGD | Kind | Purpose |
|-----|------|---------|
| `v2-hubs/infrastructure/` | seven infrastructure APIs | Account, immutable compute/network contracts, clusters, independent node pools, foundation, and registration |
| `v2-hubs/bindings/` | ten delivery APIs | Application boundary, secret/storage/addon, endpoint, and Hub authentication bindings |
| `v2-hubs/services/` | six service APIs | Smoke, JupyterHub, monitoring, registry cache, Binder, and Jupyter Outpost delivery |
| `v1-samples/configmaps/` | four config APIs | Account/service, spoke environment, exact topology, and shared-network write-once blocks |
| `v1-samples/cluster/v1/` | `SpokeIdentity`, `SpokeCluster` | Account namespace, `OpenStackClusterIdentity`, and CAPI/CAPO spoke cluster |
| `v1-samples/network/` | seven network kinds | Auto/shared/exact imports, isolated L2, shared-router subnet, or dedicated routed topologies |
| `v1-samples/compute/` | `SpokeServerGroup` | Nova placement group using the immutable approved policy |
| `v1-samples/security/` | `SpokeSecurityGroup` | Mutable, bounded Neutron workload ingress policy |
| `v1-samples/storage/` | `SpokeVolume` | Managed Cinder volume using immutable type/AZ restrictions |
| `v1-samples/workloads/` | `HelloApp`, `SpokeHelloApp`, `SpokeGitOps` | Direct CSOC workload, central CAPI delivery, or spoke-local Argo repository root |

## Boundaries

- No RGD instances, credentials, account IDs, or fleet inventory here.
- Argo objects are permitted only inside the explicit `SpokeGitOps` bootstrap
  graph; ordinary catalog and fleet resources must not add orphan Applications.
- Imported OpenStack resources must use exact filters and `managementPolicy: unmanaged`.
- Application Services never reuse CAPO API load balancers. Public spoke
  Services require an exact reviewed source CIDR.
- Worker `minNodes`/`maxNodes` are mutable `SpokeCluster` inputs; do not bake them into immutable config.
- An immutable ConfigMap is a handoff block, not a mutable settings store. Replace
  its graph instance only after every consumer has been retired.
- Every `kubectl kustomize rgds` render and `make validate` must pass before merging.
- Publish both RGD generations in dependency sync waves and require every RGD
  plus its latest GraphRevision to be Active/Ready under KRO 0.9.3 aggregation
  RBAC. The four v1 configuration APIs use separate early waves because KRO's
  default single dynamic-controller worker cannot reliably start them together.
- The first supported service matrix is smoke, dummy/ClusterIP/no-TLS
  JupyterHub, monitoring without Grafana/Alertmanager, and retained Cinder.
  Registry cache is render-only; GPU/MIG, CephFS, S3, Binder, and Outpost fail closed.

Each v2 manifest contains exactly one RGD and is listed explicitly through
the nested v2 Kustomizations; its shared contracts and retirement rules are in
`rgds/v2-hubs/README.md`. Compatibility RGDs retain their paired `.rgd.md`
documentation beside each manifest.
