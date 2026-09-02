# `RoutedSpokeNetwork`

Purpose: create network, subnet, router, and interface with a reviewed external
gateway.

Linkages: it imports the external network as unmanaged, owns the remaining ORC
topology, and emits `<spoke>-connection` for `SpokeCluster`.

Lifecycle: consumers must disappear before the owned topology. Use this when a
dedicated router is required; use `DedicatedSpokeNetwork` to share the allocation
router instead.
