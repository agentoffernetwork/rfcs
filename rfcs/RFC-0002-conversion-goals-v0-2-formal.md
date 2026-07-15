# RFC-0002: Conversion Goals v0.2 Formal Contract

**Status:** Accepted · formal · stable source  
**Date:** 2026-07-10  
**Runtime:** not available before SVC-PLATFORM WS-15-S4

## Summary

Promote the rev.2 conversion-goal model into the public v0.2 Offer contract.
This RFC records the conversion-goal portion of the v0.2 source set: a complete
independent Schema, type snapshot, formal spec, and response example replace
the draft overlay. RFC-0003 extends the same v0.2 contract with optional card
display fields.

## Proposal

Offer v0.2 fixes `version` to `"2.0"`, requires one or more goals, and keeps
all v0.1 fields not listed in the formal difference set. Each goal is an event
with closed CPA or CPS pricing and optional advisory description. Event names
are exact-string unique. The `/v1/` HTTP shell remains; the
`AON-Protocol-Version` header selects the complete payload contract.

## Compatibility

Omitted or explicit `0.1` resolves to v0.1. Explicit `0.2` is unsupported until
the S4 runtime gate passes; unknown versions fail without downgrade. Existing
v0.1 Schema and fixtures remain unchanged. Draft assets are historical and
superseded, not primary integration sources.

## Migration

Fixed pricing maps to CPA; revenue share maps to CPS without currency; hybrid
requires manual reconstruction. The event set is the `goals[].event` set.
Serving-layer compatibility and event/postback execution remain follow-up
work, not public fields of this contract.

## Decision and rollout

Accepted as a stable source artifact in PROTO. Downstream source-follow and
runtime-follow states are tracked in `contracts.json`; S4 is the sole gate for
changing runtime support to supported.
