# `HelloApp`

Purpose: deploy the demonstration Hello workload directly into the cluster where
KRO runs—normally the CSOC management cluster.

Linkages: it owns the `csoc-apps` Namespace, HTML ConfigMap, Deployment, and a
separate internal OpenStack LoadBalancer Service. It has no CAPI or spoke
references. The fleet `csoc/hello-app.yaml` instance is reconciled by CSOC Argo.
`spec.target: csoc` is required for resources to exist. The legacy `spoke` enum
value remains only because KRO prevents a breaking CRD enum removal; it creates
nothing. Use `SpokeHelloApp` for central spoke delivery.

Lifecycle: scale through `spec.replicas`. Delete this instance to remove the
CSOC workload; never use it for a spoke.
