# `SpokeArgoCD`

Purpose: install a pinned Argo CD release inside one CAPI spoke without also
creating application ownership. This additive API separates controller
lifecycle from repository/application lifecycle; the older `SpokeGitOps` API
remains available for compatibility.

Linkages: it references the selected CAPI `Cluster` and owns one continuously
reconciled `HelmChartProxy`. The Argo server remains a spoke-internal
`ClusterIP`, and the unused ApplicationSet controller is disabled.

Lifecycle: remove every `SpokeArgoApplication` that consumes this installation
before removing the graph. Removing the graph uninstalls spoke Argo CD; it does
not authorize deleting retained application PVCs or retiring the spoke.
