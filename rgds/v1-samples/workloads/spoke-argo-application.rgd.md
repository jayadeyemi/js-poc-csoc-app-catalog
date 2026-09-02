# `SpokeArgoApplication`

Purpose: continuously deliver one root Argo CD `Application` to a CAPI spoke
after a separately owned `SpokeArgoCD` installation is ready. The root must use
a public HTTPS repository and an immutable 40-character Git commit revision.

Linkages: it references the CAPI `Cluster` and the `HelmChartProxy` owned by
`SpokeArgoCD`. It owns one manifest `ConfigMap` and one reconciled
`ClusterResourceSet`; the root Application then owns the selected fork path in
the spoke cluster.

Readiness means CAPI applied the Application manifest. Operational acceptance
must additionally verify the root and child Applications are `Synced` and
`Healthy` inside the spoke and that retained PVCs remain bound across repeat
and interrupted reconciliation.

Lifecycle: remove child applications and preserve/backup PVCs before removing
the root graph. The graph does not authorize deleting application data or the
spoke infrastructure.
