# `SpokeHelloApp`

Purpose: let CSOC Argo/KRO centrally own a Hello workload that runs in a CAPI
spoke without installing Argo in that spoke.

Linkages: it references the CAPI `Cluster` and network connection, renders
workload YAML into a ConfigMap, and owns a reconciling `ClusterResourceSet`.
CAPI applies the Namespace, Deployment, and source-restricted public
LoadBalancer Service to the selected spoke.

Lifecycle: use `clusterName` to select one existing spoke. Remove this workload
before cluster retirement so its Octavia load balancer can be cleaned up.
