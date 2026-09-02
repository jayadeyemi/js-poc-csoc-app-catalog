# `ImportedSpokeNetwork`

Purpose: use an existing exact-ID network/subnet/router topology without
creating OpenStack networking.

Linkages: it consumes `SpokeNetworkImportConfig`, imports all three Neutron
objects through unmanaged ORC resources, and publishes the standard connection
for `SpokeCluster`.

Lifecycle: exact IDs are required to avoid name-based adoption. Removing this
instance cannot delete or mutate the imported topology.
