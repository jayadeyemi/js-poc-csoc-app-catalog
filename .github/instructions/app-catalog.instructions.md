---
applyTo: "**"
---
# App catalog conventions

The app catalog contains only reusable KRO ResourceGraphDefinitions under
`rgds/`. Graph instances and account-specific values belong in
`js-poc-csoc-fleet/accounts/<identity>/`.

- Keep `rgds/kustomization.yaml` as the single Argo source entrypoint.
- Use `SpokeIdentity`, never `CSOCIdentity`, for spoke OpenStack accounts.
- Use KRO/CAPI addon resources for spoke workloads; do not create
  `ApplicationSet`-selected application directories.
- Keep secret names and credential values out of every RGD schema.
- Validate with `kubectl kustomize rgds` and the authoritative workspace gate.
