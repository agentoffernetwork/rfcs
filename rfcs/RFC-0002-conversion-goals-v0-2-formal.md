# RFC-0002: Conversion Goals v0.2 Formal Contract

**Decision status:** Accepted · implemented
**Date:** 2026-07-10
**Contract:** Offer v0.2
**Current applicability:** Explicit v0.2 compatibility; superseded by adopted
v0.3 for new integrations
**Runtime at decision time:** Not available before SVC-PLATFORM WS-15-S4
**Current runtime posture:** Deployment-owned

> This RFC is the durable decision record for the v0.2 conversion-goal model.
> Its accepted status does not make v0.2 the current new-integration contract.

## Summary

Promote the rev.2 conversion-goal model into the public v0.2 Offer contract.
This RFC records the conversion-goal portion of the v0.2 source set: a complete
independent Schema, type snapshot, formal spec, and response example replace
the draft overlay. RFC-0003 extends the same v0.2 contract with optional card
display fields.

## Proposal

Offer v0.2 fixes `version` to `"2.0"`, requires one or more goals, removes the
legacy top-level `bid`, and removes `conversion_rule.accepted_types`. Each goal
is an event with closed CPA or CPS pricing and optional advisory description.
Event names are exact-string unique. The `/v1/` HTTP shell remains; the
`AON-Protocol-Version` header selects the complete payload contract.

## Compatibility

Omitted or explicit `0.2` selects the v0.2 contract and the response echoes
`AON-Protocol-Version: 0.2`. Explicit `0.1` and unknown versions fail closed
without downgrade. The `/v1/` path is an HTTP shell and does not select v0.1.
Runtime support remains `not_available` until the S4 runtime gate passes; that
deployment state does not change the current source-contract selection rule.
Existing v0.1 Schema and fixtures are historical. Draft assets are superseded,
not primary integration sources.

## Migration

Fixed pricing maps to CPA; revenue share maps to CPS without currency; hybrid
requires manual reconstruction. The event set is the `goals[].event` set.
Serving-layer compatibility and event/postback execution remain follow-up
work, not public fields of this contract.

## Decision and rollout

Accepted as a stable source artifact in PROTO. Downstream source-follow and
runtime-follow states are tracked in `contracts.json`; S4 is the sole gate for
changing runtime support to supported.

Revision note: the 2026-07-27 canonical-baseline review aligned this RFC with
the final v0.2 version-selection, no-`bid`, and runtime-boundary decisions.
