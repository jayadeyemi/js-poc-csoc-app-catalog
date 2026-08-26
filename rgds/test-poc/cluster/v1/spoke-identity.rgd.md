# `SpokeIdentity`

Purpose: create the account boundary for a restricted OpenStack credential.

Linkages: it imports the six trusted ConfigMaps from `ImmutableSpokeConfig`,
creates `spokeclusters-<identity>`, copies configuration into that namespace,
creates the namespace-restricted `OpenStackClusterIdentity`, and installs an
admission policy/binding. Every account-scoped graph discovers this object from
the namespace label and uses its generated identity/configuration.

Lifecycle: load the two credential Secrets out of band. Delete this object only
after all account-scoped graphs and OpenStack resources have been retired.
