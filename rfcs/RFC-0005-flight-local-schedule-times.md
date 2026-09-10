# RFC-0005: Flight Local Schedule Times

**Decision status:** Accepted / implemented
**Date:** 2026-09-10
**Contract:** Protocol v1.0
**Current applicability:** Current canonical v1.0 source

## Summary

Replace the Flight profile's offset-bearing endpoint `at` value with the
airport-local `local_at` value, fixed to `YYYY-MM-DDTHH:mm:ss`, and require a
positive source-provided `duration_minutes` for every Flight segment.

This preserves published airline schedule facts without requiring a producer
or consumer to infer an airport timezone, construct an absolute instant, or
derive elapsed duration from endpoint clocks.

## Problem

The protected v1.0 r15 Flight profile used endpoint `at` values with UTC
offsets. Some supply sources provide only airport-local scheduled values. An
integration that invents a missing offset can silently serialize the wrong
instant, particularly across daylight-saving changes and date-line crossings.

Subtracting local endpoint values also cannot reliably determine a flight's
elapsed duration because departure and arrival clocks can use different
timezones. The contract needs to preserve the source facts without turning
timezone inference into an integration requirement.

## Proposal

Each Flight segment has closed `departure` and `arrival` endpoints with an
uppercase IATA `airport_code` and a required `local_at` string in exact
`YYYY-MM-DDTHH:mm:ss` form. `local_at` contains neither a UTC offset nor a
timezone identifier. A producer may normalize a source space separator to
`T`, but must not infer a timezone or convert the value to an instant.

Every segment also has required positive integer `duration_minutes`, supplied
by the source. It is the authoritative scheduled elapsed duration and must
not be calculated by subtracting two airport-local values.

Structural validation rejects the legacy `at` endpoint member, offsets,
missing duration, zero duration, and unknown properties. Semantic validation
rejects impossible calendar values. It retains ordered segments and
connecting-airport continuity. At the same connecting airport, the next
departure `local_at` must not precede the previous arrival `local_at`.
Different airports' local clocks are not compared: an arrival can be
lexically earlier than its departure on a westbound or date-line-crossing
flight.

## Compatibility Impact

This is a breaking Flight profile shape change within the next protected v1.0
release candidate. The transport selector remains exact
`AON-Protocol-Version: 1.0`; no selector, profile-version field, or Query
request member is added.

Public Offer, Partner Offer, and OfferProvider success carriers share this
supply profile. Hosted Query and MCP continue to use the Generic Offer
projection and reject `offer_info.details`; this RFC does not certify a
runtime rollout.

## Alternatives Considered

1. Retain offset-bearing `at` and require producers to resolve airport
   timezones. Rejected because sources can lack the information needed for a
   trustworthy conversion.
2. Make `duration_minutes` optional and derive it from endpoint clocks.
   Rejected because local clocks at different airports do not define elapsed
   time.
3. Add an airport-timezone registry to the Flight profile. Rejected because it
   creates a new public authority and freshness obligation without improving
   the source schedule facts.
4. Create a new protocol selector or Flight profile version. Rejected because
   the closed profile remains on the protected v1.0 line and no independent
   negotiation dimension is needed.

## Rollout

1. Publish this accepted RFC with the Schema registry, TypeScript projection,
   semantic validator, fixtures, and Protocol specifications.
2. Bind the changed source set to the next unused protected v1.0 candidate
   after r15; do not rewrite the sealed r15 manifest or evidence.
3. Publish and audit the candidate through the governed repository map.
4. Align runtime and SDK consumers separately; canonical publication does not
   certify Hosted Query, MCP, or deployment availability.
