# `SpokeEnvironmentConfig`

Purpose: hold per-spoke mutable allocation choices: environment, node/pod/service
CIDRs, MTU, DHCP, and port security.

Linkages: it creates immutable `<spoke>-network-config` and
`<spoke>-cluster-config` ConfigMaps. Managed network graphs consume the network
block. `SpokeCluster` consumes the cluster block and the network connection name
recorded in it.

Lifecycle: create it before the network and cluster. Replace it only after
retiring both consumers because its outputs are immutable.
