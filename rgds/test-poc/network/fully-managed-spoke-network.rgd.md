# `FullyManagedSpokeNetwork`

Purpose: create an independent routed spoke topology while importing only the
external network.

Linkages: it owns private Network, Subnet, Router, and RouterInterface ORC
objects and publishes `<spoke>-connection` for `SpokeCluster`.

Lifecycle: this is a full ownership boundary. Retire workloads/CAPI before the
router interface, router, subnet, and network are allowed to delete.
