# rgds

KRO `ResourceGraphDefinition` library. `kustomization.yaml` is the single
entrypoint for the Argo `rgds` Application. The tested Jetstream2 profile is
grouped below `v1-samples/`; its generated kinds remain reusable across any
number of `SpokeIdentity` account instances.

## Second-generation hub APIs

`v2-hubs/` contains the current `infra.csoc.js2.org`,
`delivery.csoc.js2.org`, and `services.csoc.js2.org` APIs. Every RGD is a
single YAML document under `infrastructure/`, `bindings/`, or `services/`
and is listed explicitly by Kustomize. These APIs run beside `v1-samples/`;
renaming the package does not change any API group, Kind, or RGD identity.

## Subdirectories

### v1-samples/configmaps/

Write-once configuration blocks that produce immutable ConfigMaps consumed by downstream graphs.

| File | Kind | Emits |
|------|------|-------|
| `immutable-spoke-config.rgd.yaml` | `ImmutableSpokeConfig` | Account, compute, network, storage, LB, and Kubernetes service blocks |
| `spoke-environment-config.rgd.yaml` | `SpokeEnvironmentConfig` | Per-spoke allocation and cluster connection blocks |
| `spoke-network-import-config.rgd.yaml` | `SpokeNetworkImportConfig` | Exact OpenStack IDs supplied manually or by an external inventory job |
| `spoke-shared-network-config.rgd.yaml` | `SpokeSharedNetworkConfig` | Exact shared/provider network and subnet IDs |

`ImmutableSpokeConfig` emits `<identity>-account-config`,
`-compute-service-config`, `-network-service-config`,
`-storage-service-config`, `-loadbalancer-service-config`, and
`-kubernetes-config`. `SpokeIdentity` copies those immutable blocks into the
account namespace. `SpokeEnvironmentConfig` emits `<spoke>-network-config` and
`<spoke>-cluster-config`; every network graph emits `<spoke>-connection`.

Write-once fields include project/image/external-network IDs, keypair, flavors,
Kubernetes version/control-plane count, API source CIDR, volume type, LB
policy/provider, node/pod/service CIDRs, MTU, DHCP, and port security. The only
ordinary mutable fleet fields are `SpokeCluster.spec.kubernetes.minNodes` and
`maxNodes`; workload replica and repository connection fields are explicit in
their workload graph instances.

### v1-samples/cluster/v1/

| File | Kind | Purpose |
|------|------|---------|
| `spoke-cluster.rgd.yaml` (and peers) | `SpokeIdentity`, `SpokeCluster` | Account namespace, `OpenStackClusterIdentity`, CAPI/CAPO cluster with autoscaler |

`SpokeIdentity` creates the account namespace and a namespace-restricted `OpenStackClusterIdentity`. `SpokeCluster` exposes only mutable `minNodes`/`maxNodes`; all other inputs are read from the immutable ConfigMaps produced above.

### v1-samples/network/

| File | Kind | ORC management |
|------|------|---------------|
| `auto-allocated-spoke-network.rgd.yaml` | `AutoAllocatedSpokeNetwork` | Imports the provider allocation topology as `unmanaged` |
| `imported-spoke-network.rgd.yaml` | `ImportedSpokeNetwork` | Imports exact network/subnet/router IDs as `unmanaged` |
| `isolated-openstack-network.rgd.yaml` | `IsolatedOpenStackNetwork` | Owns an L2 network and subnet with no router |
| `dedicated-spoke-network.rgd.yaml` | `DedicatedSpokeNetwork` | Owns network/subnet/interface on an imported shared router |
| `routed-spoke-network.rgd.yaml` | `RoutedSpokeNetwork` | Owns network/subnet/router/interface; imports only the external network |
| `fully-managed-spoke-network.rgd.yaml` | `FullyManagedSpokeNetwork` | Independent fully owned topology variant |
| `shared-provider-network.rgd.yaml` | `SharedProviderNetwork` | Imports exact shared network/subnet IDs without a router owner |

Managed ORC resources use `managedOptions.onDelete: delete`. Imported objects
use exact IDs where provided (or the provider's reviewed auto-allocation names)
and `managementPolicy: unmanaged`, so graph deletion cannot update or delete
them. Public floating-IP and DNS-record graphs are intentionally absent because
they would expand exposure beyond the reviewed internal-only policy. Optional
Nova server-group, Neutron security-group, and Cinder volume graphs live under
`compute/`, `security/`, and `storage/`, but are not composed into
`SpokeCluster`; CAPO/CCM/CSI retain cluster infrastructure ownership.

### v1-samples/workloads/

| File | Kind | Delivery mechanism |
|------|------|-------------------|
| `hello-app.rgd.yaml` | `HelloApp` | Direct resources in the CSOC cluster |
| `spoke-hello-app.rgd.yaml` | `SpokeHelloApp` | CSOC-managed CAPI `ClusterResourceSet` delivery |
| `spoke-gitops.rgd.yaml` | `SpokeGitOps` | CAPI addon installs spoke-local Argo CD and a repository root |
| `spoke-argocd.rgd.yaml` | `SpokeArgoCD` | CAPI addon installs spoke-local Argo CD independently of applications |
| `spoke-argo-application.rgd.yaml` | `SpokeArgoApplication` | Reconciled CAPI delivery of one pinned root Application after Argo is ready |

The APIs are deliberately separate so one object has one ownership model. The
CSOC Hello uses an internal load balancer. Centrally delivered spoke Hello uses
a separate source-restricted public load balancer. `SpokeGitOps` points only to
a public HTTPS repository; private-repository credentials remain an explicit
out-of-band extension.
