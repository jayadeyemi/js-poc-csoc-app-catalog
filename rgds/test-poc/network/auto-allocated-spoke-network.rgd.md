# `AutoAllocatedSpokeNetwork`

Purpose: attach to the provider-created auto-allocation network, subnet, and
router selected by reviewed names.

Linkages: it imports `SpokeIdentity` and network service config, creates three
unmanaged ORC imports, and publishes `<spoke>-connection` for `SpokeCluster`.

Lifecycle: graph deletion removes only Kubernetes import objects. It never
updates or deletes provider allocation resources.
