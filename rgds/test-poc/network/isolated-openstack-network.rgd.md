# `IsolatedOpenStackNetwork`

Purpose: create an L2 network and subnet with no router attachment.

Linkages: it consumes identity/network configuration, owns ORC Network and
Subnet objects, and emits a connection with no router. It is suitable for
network-only use; a normal `SpokeCluster` needs reviewed external reachability.

Lifecycle: confirm no ports use the subnet before deleting the graph. Route it
through a different reviewed composition rather than mutating this one in place.
