# `SpokeCluster`

Purpose: provision a Kubernetes spoke on existing OpenStack infrastructure with
CAPI/CAPO, required cloud addons, and bounded autoscaling.

Linkages: it imports `SpokeIdentity`, copied service ConfigMaps,
`SpokeEnvironmentConfig` output, a network graph connection, and `SpokeKeypair`
connection. It owns CAPI Cluster/control-plane/worker objects, CAPO templates,
Calico, CCM and Cinder CSI addons, cloud-config resource sets, and the
namespace-scoped Cluster Autoscaler. `SpokeHelloApp` and `SpokeGitOps` select its
Cluster label. Control-plane and worker machines boot from exact 20-GiB Cinder
root volumes using the immutable storage type and availability zone. `Ready`
includes successful workload cloud-config delivery.

Lifecycle: only worker bounds are mutable. Retire workloads first, then delete
this instance and wait for all CAPI machines/load balancers to disappear before
removing keypair or network owners.
