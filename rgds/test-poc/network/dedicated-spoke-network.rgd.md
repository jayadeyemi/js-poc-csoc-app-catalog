# `DedicatedSpokeNetwork`

Purpose: create a dedicated network/subnet and attach them to the existing
allocation router.

Linkages: it imports the router as unmanaged, owns ORC Network, Subnet, and
RouterInterface objects, and publishes the standard connection consumed by
`SpokeCluster` and public spoke workloads.

Lifecycle: delete workloads and CAPI first, then interface/subnet/network. The
shared router remains untouched.
