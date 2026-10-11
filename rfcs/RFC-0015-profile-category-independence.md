# RFC-0015: Offer Profile and Category Independence

**Decision status:** Accepted / implemented
**Date:** 2026-10-11
**Protocol line:** v1.0

## Decision

Registered Offer profiles are independent of `offer_info.category.id` and
`secondary_category_ids`. Any valid taxonomy category may carry `flight`,
`hotel_rate`, or `game` when the supplied facts satisfy that profile. A profile
selects a closed data shape; category classifies the Offer for discovery.
Producers must not rewrite category to enable a profile.

Remove exact Flight/Hotel category checks and the Games subtree check. This
supersedes the category binding in RFC-0012 and the earlier travel profiles.
The profile selector remains a closed registry; arbitrary profile names and
extra data keys are not accepted. Category remains required and must be valid.
Profile data, action, offer_type, commercial, and paired-request constraints
remain unchanged. Query category filters continue to apply independently.

`game` still describes a game and retains its source-reported positive integer
cumulative downloads/installs rule. This change does not rename it or turn it
into a generic downloads profile for all software. Unknown facts are omitted.

## Compatibility and rollout

The exact v1.0 selector and existing valid payloads are unchanged. This relaxes
acceptance, but older semantic validators may reject a newly valid cross-category
Offer. Upgrade validators before producers use cross-category profiles. Existing
TypeScript/Python parsers already validate category independently; regression
coverage proves preservation of all three registered profiles.

Historical release manifests and approvals remain immutable. This revision
requires its own protected contract-extension admission and public release;
merging candidate code does not certify production rollout.

## Warehouse integration

Select category from the actual Offer subject and profile from the available
facts. Remove Games subtree eligibility checks for game details. Preserve
source evidence upstream, exact/lower-bound count conversion and unknown-value
omission. Do not sum sources, estimate counts, or emit unregistered data fields.

## Acceptance

All three profiles pass Public, Partner, Provider and Generic Query validation
under multiple valid independent categories. Invalid/missing categories and
invalid profile facts still fail. SDKs preserve both category and profile facts.
Documentation identifies category as independent instead of a profile gate.
