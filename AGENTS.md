# js-poc-csoc-app-catalog

This repository is definitions-only. It publishes reusable KRO
`ResourceGraphDefinition` objects; all graph instances live in
`js-poc-csoc-fleet/`.

GitHub: `github.com/jayadeyemi/js-poc-csoc-app-catalog`

## Layout

```
rgds/
  test-poc/            tested reusable OpenStack profile library
    configmaps/        write-once provider and spoke configuration blocks
    cluster/v1/        SpokeIdentity and SpokeCluster graphs
    compute/           ORC-managed keypairs and optional Nova placement graphs
    network/           imported, isolated, shared-router, and routed graphs
    security/          optional Neutron policy graphs
    storage/           optional Cinder graphs
    workloads/         conditional direct CSOC/CAPI addon workload graphs
  kustomization.yaml   the only Argo source entrypoint
```

`SpokeIdentity` is the restricted OpenStack account boundary. It creates an
account namespace, a namespace-restricted `OpenStackClusterIdentity`, copied
immutable configuration, and account-specific admission restrictions.

`SpokeCluster` provisions Kubernetes resources in an existing OpenStack cloud
through CAPI/CAPO. It never installs OpenStack. ORC manages or imports Neutron
resources according to each network graph's management policy.

`SpokeKeypair` creates the account's Nova keypair through ORC from the public
key stored in immutable configuration. `SpokeCluster` consumes only its
generated connection ConfigMap; it never accepts a fleet-supplied keypair name.

`HelloApp` is the only Hello API. `target: csoc` deploys directly; `target:
spoke` creates a CAPI `ClusterResourceSet` in the management cluster. Both
expose only an internal OpenStack load balancer. Do not add Argo
ApplicationSets or raw spoke packages.

## Boundaries

- Do not put RGD instances, credentials, account IDs, or fleet inventory here.
- Do not add Argo `Application`, `ApplicationSet`, baseline, security, or
  observability packages.
- Immutable provider restrictions belong in graph-produced ConfigMaps.
- Mutable operator choices belong in the narrow schema of the consuming RGD.
- Spokes use one approved general worker flavor from immutable configuration;
  do not add GPU, high-memory, or per-cluster worker-class selection.
- Worker `minNodes` and `maxNodes` are mutable `SpokeCluster` inputs and must
  not be duplicated in immutable configuration.
- Imported OpenStack resources must use exact filters and
  `managementPolicy: unmanaged`.
- Application Services must remain internal-only unless a separate reviewed
  public-access design supplies a restricted source CIDR and proves that the
  cloud enforces it. Never reuse a CAPO-owned API load balancer for workloads.
- Every Kustomize render and the workspace validation gate must pass.
