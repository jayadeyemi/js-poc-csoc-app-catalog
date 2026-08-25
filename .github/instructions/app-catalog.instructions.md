---
applyTo: "**"
---
# App catalog conventions

The app catalog contains only reusable KRO ResourceGraphDefinitions under
`rgds/`. CSOC-local graph instances belong in `js-poc-csoc-fleet/csoc/`;
account-specific instances belong in `js-poc-csoc-fleet/accounts/<identity>/`.

- Keep `rgds/kustomization.yaml` as the single Argo source entrypoint.
- Use `SpokeIdentity`, never `CSOCIdentity`, for spoke OpenStack accounts.
- Use KRO/CAPI addon resources for spoke workloads; do not create
  `ApplicationSet`-selected application directories.
- Use direct KRO resources for CSOC-local workloads. Keep application load
  balancers internal-only and separate from Kubernetes API load balancers.
- Keep secret names and credential values out of every RGD schema.
- Keep one approved general worker flavor in immutable account configuration;
  keep mutable `minNodes` and `maxNodes` on `SpokeCluster`, with no GPU,
  high-memory, or worker-class selector fields.
- Validate with `kubectl kustomize rgds` and the authoritative workspace gate.
