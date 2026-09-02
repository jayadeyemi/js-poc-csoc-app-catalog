# `SpokeSharedNetworkConfig`

Purpose: record exact shared/provider network and subnet UUIDs when no router
object belongs to the spoke composition.

Linkages: it emits an immutable ConfigMap consumed by `SharedProviderNetwork`;
that graph imports both objects as unmanaged and emits the standard connection.

Lifecycle: validate provider reachability and security externally. Replace only
after all clusters using the connection are retired.
