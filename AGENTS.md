# js-poc-csoc-app-catalog

## API evolution invariants

- Branches promote `environment/dev` to staging to prod through coordinated
  pull requests; do not point one CSOC at another environment's catalog branch.
- Additive schema changes retain old defaults and old-instance fixtures.
- KRO ResourceGraphDefinition API identity is treated as immutable. A breaking
  schema or ownership change gets a new Kind/RGD, dual-run fixtures, a migration
  procedure, a rollback window, and a separately reviewed retirement.
- Workload graphs deploy into the spoke through reconciled CAPI addon resources
  or spoke-local GitOps. They must not put application workloads on a CSOC.
- Pin charts/images/revisions and expose readiness from real downstream status.

This repository is definitions-only. It publishes reusable KRO
`ResourceGraphDefinition` objects; all graph instances live in
`js-poc-csoc-fleet/`.

GitHub: `github.com/jayadeyemi/js-poc-csoc-app-catalog`

## Layout

```
rgds/
  v2-hubs/               current one-file-per-RGD hub APIs
    infrastructure/     account, machine, network, cluster, pool, foundation
    bindings/           application, secret, storage, endpoint, and auth links
    services/           pinned workload service APIs
  v1-samples/            tested reusable OpenStack profile library
    configmaps/        write-once provider and spoke configuration blocks
    cluster/v1/        SpokeIdentity and SpokeCluster graphs
    compute/           ORC-managed keypairs and optional Nova placement graphs
    network/           imported, isolated, shared-router, and routed graphs
    security/          optional Neutron policy graphs
    storage/           optional Cinder graphs
    workloads/         direct CSOC, central CAPI, and spoke-local GitOps graphs
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

`HelloApp` deploys only in the CSOC cluster. `SpokeHelloApp` creates a CAPI
`ClusterResourceSet` and a separate public Octavia load balancer restricted to
its mutable `applicationAllowedCIDR`. `SpokeGitOps` installs spoke-local Argo CD
through a `HelmChartProxy` and points a root Application at a public HTTPS
repository. Do not combine those owners for one workload.

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
- Public Application Services must use a separate load balancer, a tracked
  immutable source `/32`, and acceptance evidence that Octavia enforces it.
  Never reuse a CAPO-owned API load balancer for workloads.
- Every Kustomize render and the workspace validation gate must pass.
