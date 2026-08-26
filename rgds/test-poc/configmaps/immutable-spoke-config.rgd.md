# `ImmutableSpokeConfig`

Purpose: establish the reviewed, cluster-scoped provider contract for one
identity. Inputs pin project, Glance image, SSH public key, flavors, external
network, API CIDR, storage/LB policy, Kubernetes version, and control-plane size.

Linkages: it owns a private configuration namespace and six immutable
ConfigMaps. `SpokeIdentity` imports those blocks and copies them into
`spokeclusters-<identity>`. Network, compute, storage, and `SpokeCluster` graphs
consume the copies, never this object directly.

Lifecycle: changing write-once data requires retiring all consumers and
replacing the instance; do not patch generated immutable ConfigMaps.
