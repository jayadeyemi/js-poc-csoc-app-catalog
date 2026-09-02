# `SpokeServerGroup`

Purpose: create an optional Nova placement group using the approved policy in
immutable compute configuration.

Linkages: it imports `SpokeIdentity` and compute config and owns one ORC
`ServerGroup`. It is intentionally independent of `SpokeCluster`; adding it does
not silently alter CAPO placement.

Lifecycle: activate only with a consumer designed to reference its reported ID.
Remove consumers before deleting the graph instance.
