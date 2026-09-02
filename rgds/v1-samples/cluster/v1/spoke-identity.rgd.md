# `SpokeIdentity`

Purpose: create the account boundary for a restricted OpenStack credential.

Linkages: it imports the six trusted ConfigMaps from `ImmutableSpokeConfig`,
creates `spokeclusters-<identity>`, copies configuration into that namespace,
requires both out-of-band credential Secrets, creates the namespace-restricted
`OpenStackClusterIdentity`, and installs an admission policy/binding. Every
account-scoped graph discovers this object from the namespace label and uses its
generated identity/configuration. `Ready` remains false until both Secrets exist.

Lifecycle: load the two credential Secrets out of band. Delete this object only
after all account-scoped graphs and OpenStack resources have been retired.
