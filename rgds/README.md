# rgds

KRO `ResourceGraphDefinition` library. `kustomization.yaml` is the single
entrypoint for the Argo `rgds` Application. The tested Jetstream2 profile is
grouped below `test-poc/`; its generated kinds remain reusable across any
number of `SpokeIdentity` account instances.

## Subdirectories

### test-poc/configmaps/

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
`maxNodes`; `HelloApp.spec.replicas` is mutable workload scale.

### test-poc/cluster/v1/

| File | Kind | Purpose |
|------|------|---------|
| `spoke-cluster.rgd.yaml` (and peers) | `SpokeIdentity`, `SpokeCluster` | Account namespace, `OpenStackClusterIdentity`, CAPI/CAPO cluster with autoscaler |

`SpokeIdentity` creates the account namespace and a namespace-restricted `OpenStackClusterIdentity`. `SpokeCluster` exposes only mutable `minNodes`/`maxNodes`; all other inputs are read from the immutable ConfigMaps produced above.

### test-poc/network/

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

### test-poc/workloads/

| File | Kind | Delivery mechanism |
|------|------|-------------------|
| `hello-app.rgd.yaml` | `HelloApp` | `target: csoc` creates direct resources; `target: spoke` uses a CAPI `ClusterResourceSet` |

There is exactly one Hello RGD. Both targets expose an internal OpenStack load
balancer only. No floating IPs.
