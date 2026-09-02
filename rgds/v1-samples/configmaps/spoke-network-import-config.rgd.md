# `SpokeNetworkImportConfig`

Purpose: record exact existing Neutron network, subnet, and router UUIDs for a
no-network-creation composition.

Linkages: it owns one immutable ConfigMap consumed only by
`ImportedSpokeNetwork`. That graph creates unmanaged ORC import objects and the
standard `<spoke>-connection` consumed by `SpokeCluster`.

Lifecycle: IDs must describe one reachable topology in the identity project.
Deleting this graph never authorizes deletion of the imported OpenStack objects.
