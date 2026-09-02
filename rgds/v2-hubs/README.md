# Second-generation RGD contract

The three v2 directories are compiled together by KRO 0.9.3. Every manifest
contains exactly one `ResourceGraphDefinition` and is registered explicitly in
its directory Kustomization:

- `infrastructure/`: `SpokeAccount`, `MachineProfile`, `SpokeNetwork`,
  `WorkloadCluster`, `SpokeNodePool`, `ClusterFoundation`, and
  `SpokeRegistration`.
- `bindings/`: `ApplicationBoundary`, `SecretBundle`, `CinderStorageBinding`,
  `CephFSAddon`, `CephFSVolumeBinding`, `S3CSIAddon`, `S3VolumeBinding`,
  `GpuRuntimeAddon`, `EndpointBinding`, and `HubAuthBinding`.
- `services/`: `SmokeApplication`, `JupyterHubInstance`,
  `MonitoringInstance`, `RegistryCacheInstance`, `BinderBuildInstance`, and
  `JupyterOutpostInstance`.

Binding and service file names are the lowercase Kind followed by
`.rgds.yaml`. Keep definitions separate: do not reintroduce multi-document
binding or service bundles.

All references contain only `name` and are resolved in the instance namespace.
ORC imports use exact IDs with `managementPolicy: unmanaged`; managed network
resources are limited to Network, Subnet, Router, and RouterInterface. Octavia,
Designate, Manila shares, and S3 buckets stay explicit external prerequisites.

Machine profiles, network/cluster identity, topology, source commits, and
endpoint contracts are immutable through the bootstrap admission policy.
Only `SpokeNodePool.minNodes` and `maxNodes` may change in place. The generated
`MachineDeployment` deliberately omits `spec.replicas`: CAPI initializes a new
deployment from the minimum autoscaler annotation and preserves the
autoscaler-owned replica count on later KRO reconciliations. `system` pools
start at one or more; all pool classes publish scale-from-zero capacity, stable
`csoc.js2.org/pool-*` labels, and a class taint.

`ClusterFoundation` is the only owner of Calico, OpenStack CCM, Cinder CSI,
the retained/deleting StorageClasses, and one per-cluster Cluster Autoscaler.
It creates only central Argo `AppProject`/`Application` objects; every rendered
foundation resource lives in the spoke cluster. `SpokeRegistration` runs as
soon as the control-plane API and CAPI kubeconfig exist, before the foundation.
The bootstrap broker decodes the wrapper workload Secret and copies only its
nested `cloud.conf`, issues separate application/platform/monitoring Argo
certificates, and copies a namespace-scoped CSOC kubeconfig for the spoke-local
autoscaler. A reviewed `SpokeRegistration.spec.rotationRequest` changes all
four certificates and their recorded hashes. It never stores the CAPI
administrative kubeconfig in Argo.

The CSOC-to-spoke boundary is strict: KRO instances, CAPI/CAPO/ORC objects,
Argo objects, and the registration broker live in CSOC; OpenStack resources
live in the selected spoke project; Kubernetes addons, storage objects, and
applications live in the registered spoke. V2 must not add a
`ClusterResourceSet` or `HelmChartProxy` delivery path.

Typed service graphs create central Argo Applications at exact config commits.
They omit cascading finalizers, report Argo health/revision, and use automated
sync/self-heal. Persistent contracts carry `Prune=false`; infrastructure fleet
pruning stays disabled. Retirement is workload-first and separately approved.

The initial supported set is smoke, dummy-auth/ClusterIP/no-TLS JupyterHub,
monitoring without Grafana or Alertmanager, and fixed or dynamic retained
Cinder policy. Registry cache is render-only. Other endpoint/TLS/auth modes
have fixtures but admission denies them. GPU/MIG, CephFS, S3, Binder, and
Outpost identities remain published and documented but fail closed before an
Application can be created.
