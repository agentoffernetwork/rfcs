# RFC-0007: Flight Query Profile

**Decision status:** Accepted
**Implementation status:** Implemented in current canonical protocol sources
**Date:** 2026-09-16
**Contract:** Protocol v1.0
**Availability:** Public release and runtime rollout require separate evidence

## Summary

Add optional closed `intent.details` with profile `flight` to existing Query
and OfferProvider requests. Distinguish real-time reference_search from
traveler_quote, return required Flight details and explicit price_basis, and
separate complete no-match, partial results, unsupported capability and failures.

## Problem

Generic Query intentionally excludes supply details. Natural language cannot
reliably express or validate exact itinerary, passenger and stop constraints.
A price with unknown passenger scope must not become an invented family total;
a failed supplier lookup must not become a successful no-ticket response.

## Proposal

The normative [typed Flight Query](https://github.com/agentoffernetwork/protocol/blob/main/v1.0/specs/query-api.md#typed-flight-query)
defines ordered city/airport legs and explicit local dates, optional strict
cabins, connections and nonstop constraints, and sufficient traveler age/seat
facts. Flight projections require explicit reference or itinerary_total,
complete ordered segments and matching request facts. Reference forbids
travelers and can omit quote when observation is unknown; legacy supply with
omitted price_basis retains its traveler-composed itinerary quote semantics.
Price and explicit tax state remain mandatory. Name-only source stops are
preserved without fabricated airport identity; absent stops remain unknown.

Success requires flight_search with paired query_kind, complete/partial and
batch fetched_at. Typed responses forbid empty_reason and alternatives. At
least one capable source must actually complete for success. All participating
sources failing is INTERNAL_ERROR/upstream_failure; inability to honor hard
conditions is BAD_REQUEST/unsupported_capability; malformed input is
BAD_REQUEST/invalid_query. Existing authentication, policy and rate limits
retain their codes. There is no automatic fallback or retry.

Provider shares requests, matching and execution/error semantics, but keeps
Partner Offer identities and never receives public display_price. Public typed
Flight can use the established response-owned display_price without changing
price basis, tax or quote semantics. JSON Schema then pure semantic validation
uses the complete paired request and independent trusted airport-city facts;
it cannot prove real supplier calls, stock, source truth or booking guarantees.

The [Ctrip mapping](https://github.com/agentoffernetwork/protocol/blob/main/v1.0/specs/flight-query-ctrip-mapping.md)
records supplied historical tool evidence and its gaps, including absent
traveler/multi-leg capabilities, preferred-cabin fallback, limited results,
name-only stops and uncertain price scope. It does not certify a live adapter.

## Compatibility Impact

Generic request and projection rules remain. Old valid Flight supply retains
its existing quote semantics. New typed instances can fail older strict
validators. Exact header/body 1.0 and Offer marker 3.0 remain; there is no new
endpoint, profile_version or runtime capability-discovery platform. Each
deployment must explicitly declare support for this revision before clients
use it; absence of declaration means unsupported.

## Alternatives Considered

- Arbitrary details in Generic results: rejected because it destroys the
  existing projection boundary.
- Assume one adult from an upstream URL: rejected because navigation data
  cannot establish quote composition.
- Treat failed suppliers as empty inventory: rejected because it conceals
  uncertainty and changes the meaning of no match.
- Combine independent one-way fares into a guaranteed multi-leg total:
  rejected without supplier evidence of one executable complete itinerary.

## Rollout

Land the protocol, schema, types, semantic helpers, examples and source tests
as one canonical extension. Admit all new assets and RFC-0007 in the existing
protected next-unused v1.0 rN workflow, binding exact reviewed source digests.
Never rewrite sealed releases or historical evidence. Source implementation,
public publication, documentation deployment, service/Provider/SDK adaptation
and live execution certification are distinct milestones. Accepted/implemented
here describes the source contract only; it does not claim any public release
or production flight-search capability.
