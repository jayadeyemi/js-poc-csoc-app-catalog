# `SpokeSecurityGroup`

Purpose: create one optional, bounded Neutron ingress security group.

Linkages: it discovers the namespace `SpokeIdentity` and owns one ORC
`SecurityGroup` with the declared protocol, port range, and source CIDR. It is
not implicitly attached to CAPO machines or Services.

Lifecycle: add an explicit consumer before expecting policy effect. Detach all
ports/consumers before deleting it; public CIDRs require separate review.
