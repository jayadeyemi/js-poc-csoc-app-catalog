# `SpokeGitOps`

Purpose: install Argo CD inside a CAPI spoke and point its root Application at a
public HTTPS Git repository/path.

Linkages: it references the selected CAPI Cluster and owns one continuously
reconciled `HelmChartProxy`. The Argo chart installs its CRDs/controllers and an
`Application` that targets the spoke-local API. After bootstrap, that spoke Argo
instance—not CSOC Argo—owns repository workloads.

Lifecycle: do not combine it with central delivery for the same workload. This
version intentionally supports public repositories only; private credentials
must be supplied through a separately reviewed Secret design. Remove root
workloads, then this graph, before retiring the spoke.
