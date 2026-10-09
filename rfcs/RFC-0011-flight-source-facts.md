# RFC-0011: Flight Source Facts and Stop Language

**Decision status:** Accepted / implemented
**Publication status:** Candidate source; protected v1.0 release pending

## Summary

Extend the existing stable v1.0 Flight profile with optional source-reported
airport full names, terminal identifiers, planned aircraft names, and stop-name
language. Keep the existing leg duration and segment stops fields.

## Problem

The Flight profile can describe a segment's airport code, local schedule,
duration and stops, but cannot carry a source airport full name, terminal or
planned equipment name without misusing a translated display field. A stop
name can be source text of unverified language. A leg with unknown or reported
stops must not be called nonstop.

## Proposal

An endpoint MAY carry `airport_full_name: {text,language}` and nonblank
`terminal`. A segment MAY carry
`planned_aircraft: {name:{text,language},source:"supplier_reported"}`. A stop
MAY carry `name_language`, which requires the existing `name`. Source text is
nonblank and its language uses the stable v1.0 BCP 47 syntax; `und` means
unverified source language. Source text is independent of Offer
`content_language`. These objects remain closed.

The existing `legs[].duration_minutes` is used only for a positive source
total at least the sum of segment durations. Existing `segments[].stops`
preserves three states: omitted means unknown, `[]` means confirmed no stops,
and a nonempty array reports ordered intermediate stops. A non-array value or
an array with an invalid stop location is unknown in full, never a filtered
partial truth. An invalid optional duration alone does not erase a valid stop
location. A name-only stop remains recognizable. `nonstop_only` requires each
leg to have exactly one segment with an explicit empty stops array; an outbound
and return leg can both satisfy this rule. Source-reported stopover dwell time
MAY populate the existing optional `duration_minutes` only after its unit is
verified as minutes and its value is a positive integer. An absent or invalid
duration does not erase a valid source stop location.

## Compatibility Impact

Old Offers remain valid under the expanded profile. A strict old v1.0 reader
may reject new optional keys because Flight objects are closed. The exact
`AON-Protocol-Version: 1.0` selector remains unchanged. AON readers must
upgrade before producers emit the new keys; third parties using old strict
readers must upgrade. No capability negotiation or dual payload is introduced.

## Alternatives

Using airport or aircraft names as English display names would falsely assert
translation. Deriving an aircraft code or inventing a stop duration when the
source value is absent or invalid would assert facts the supplier has not
established. Neither is adopted.

## Rollout

The accepted `flight_source_facts` contract-extension class must pass protected
admission before publication. Bind this RFC, Schema, types, semantic validator,
specifications and examples to one candidate source commit. Use the next unused
protected `protocol-v1.0.0-rN` through the
existing release flow; do not rewrite historical evidence. Separately verify
source definitions and route/date coverage, upgrade AON strict readers, and
obtain product-owner approval of the observed nonstop result loss before
activating producer emission. Source acceptance, protected publication,
reader deployment and production activation are distinct states.
