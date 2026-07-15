# RFC: Conversion Goals v0.2 Draft (Historical / Superseded)

**Status**: `historical / superseded by RFC-0002`
**Author**: AgentOffer Protocol maintainers
**Created**: 2026-07-09
**Revised**: 2026-07-09 (rev. 2 — goal minimized to event + pricing)

## Summary

This RFC proposes a non-GA v0.2 draft overlay for expressing conversion goals on one AgentOffer offer. A goal is the smallest billable statement an offer can make: one recognized occurrence of an event, at a stated price. The draft adds `goals[]`, keeps the active v0.1 schema untouched, and gives downstream projects a shared source reference before runtime implementation.

## Problem

AgentOffer v0.1 represents monetization through one top-level pricing object and a flat list of accepted conversion types. That is not enough for offers that pay on multiple events such as install, lead, sale, subscription, and trial.

Without a protocol draft, SVC-PLATFORM, SVC-CORE, PTR, ADMIN, DOCS, SDK, and AON-ORG could each invent incompatible goal shapes. The platform needs one draft source that marks open questions and keeps v0.1 compatibility intact.

A public protocol also ages badly when it absorbs platform machinery. The first revision of this draft carried per-goal identity fields, alias lists for raw event-name matching, account-scoped custom-event objects, and v0.1 compatibility fields inside the schema itself. Rev. 2 removes all of them: identity collapses into the event, matching and vocabularies move to platform scope, and migration history moves to a non-normative appendix.

## Proposal

Introduce the non-GA Conversion Goals v0.2 Draft (rev. 2):

- `goals[]` is the draft source for multi-goal pricing. At least one goal per offer.
- `goals[].event` is a lowercase slug (`^[a-z0-9][a-z0-9_.-]{0,63}$`) that is both the semantic identity and the reference key of the goal, unique within one offer. The protocol constrains only the syntax; no normative vocabulary is defined. Changing the event replaces the goal.
- `goals[].pricing` uses `cpa` or `cps` as a discriminated union: `cpa` carries a positive decimal `amount` plus ISO 4217 `currency`; `cps` carries a positive decimal `rate` (a percentage — a ratio carries no currency).
- `goals[].description` is an optional advisory note; consumers must not derive billing behavior from it.
- Well-known event names (install, sale, lead, subscription, trial) are published as a non-normative appendix; validation never depends on them.
- Raw event-name mapping, event registries, enrichment, and any v0.1-compatible output derivation are platform responsibilities at the serving layer, outside the protocol contract.

The draft does not change active v0.1 `offer-schema-v0.1.json`.

## Compatibility Impact

This is additive and non-GA. Existing v0.1 consumers continue using the active v0.1 pricing and events/postback fields.

New consumers may follow the draft only when they explicitly opt into `offers.conversion-goals.v0.2-draft`. No runtime service is required to emit or persist the draft until downstream follow-up work lands. Migration guidance from v0.1 (including the explicit non-support of v0.1 `hybrid` pricing, which must be restructured into `cpa` or `cps` goals) lives in the spec's non-normative migration appendix.

## Alternatives Considered

1. Make `goals[]` active immediately and remove the v0.1 top-level pricing field.
   Rejected because that would be a breaking change and would need a separate release gate.

2. Keep a separate per-goal identity field alongside the event.
   Rejected: with event uniqueness and replacement semantics, a separate identity is redundant; industry postback guidance (for example TUNE's own documentation) prefers semantic names over offer-scoped numeric identifiers, and an optional discriminator can be added additively later if identity ever diverges from semantics.

3. Keep alias lists and account-scoped custom-event objects in the protocol.
   Rejected: raw event-name mapping is platform configuration everywhere in the industry; account scoping is positional (an offer already lives in an account context) and does not need declarative fields.

4. Define a normative standard event vocabulary.
   Rejected: a normative enum turns every vocabulary change into a breaking protocol event and creates silent semantic capture of producer-chosen names; a non-normative well-known-names appendix provides convergence pressure without governance burden.

5. Keep v0.1 `hybrid` pricing for minimal compatibility.
   Rejected: hybrid was a legacy placeholder with no structured definition; carrying it forward would freeze an unspecified shape into the draft. v0.1 hybrid offers must be restructured manually, as stated in the migration appendix.

## Rollout

1. Publish this RFC and draft artifacts as non-GA.
2. SVC-PLATFORM WS-15-S4 implements goal management as platform configuration, including any v0.1-compatible output derivation at the serving layer.
3. SVC-CORE WS-15-S6 implements per-goal settlement and public events/postback conformance.
4. PTR WS-15-S5 and ADMIN WS-15-S7 consume the runtime contract.
5. DOCS, SDK, and AON-ORG publish draft-facing documentation and preview surfaces after source refs stabilize.

## Open Questions

- Which public events/postback fields should reference per-goal conversion results in WS-15-S6?
