# `SpokeKeypair`

Purpose: create the Nova keypair used by CAPO machines from the reviewed public
key in immutable identity configuration.

Linkages: it imports `SpokeIdentity` and compute config, owns an ORC `KeyPair`,
and publishes `<spoke>-keypair-connection`. `SpokeCluster` requires that
connection and never accepts a raw fleet keypair name.

Lifecycle: rotate by retiring all machines, replacing the graph, then creating
a new cluster. The private key never enters Kubernetes or Git.
