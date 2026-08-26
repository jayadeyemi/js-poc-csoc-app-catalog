# `SpokeVolume`

Purpose: pre-provision an independent Cinder volume with reviewed type,
availability zone, size, and description.

Linkages: it imports `SpokeIdentity` and storage config, imports the configured
VolumeType as unmanaged, and owns one ORC `Volume`. It neither creates a PVC nor
attaches the volume to a Nova server; dynamic spoke PVCs use Cinder CSI instead.

Lifecycle: data deletion is explicit. Detach/backup the volume and merge removal
intent before deleting the graph instance.
