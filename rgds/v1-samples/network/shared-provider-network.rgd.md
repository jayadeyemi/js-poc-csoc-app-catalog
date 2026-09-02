# `SharedProviderNetwork`

Purpose: attach a spoke to exact shared/provider network and subnet IDs without
owning a router.

Linkages: it consumes `SpokeSharedNetworkConfig`, creates unmanaged ORC Network
and Subnet imports, and publishes the standard connection.

Lifecycle: provider routing, DHCP, and security are external prerequisites.
Deleting the graph removes only imports, never shared OpenStack resources.
