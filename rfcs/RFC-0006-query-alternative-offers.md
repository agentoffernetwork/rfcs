# RFC-0006: Query Alternative Offers

**Decision status:** Accepted
**Date:** 2026-09-11
**Contract:** Protocol v1.0
**Current applicability:** Current canonical v1.0 source extension; public release and runtime rollout require separate evidence

## Summary

Add optional `alternative_offers` to an otherwise empty Query response without
changing request parameters. Keep main `offers: []` and `empty_reason`, and
provide 1–3 complete Generic Query Offers with truthful, request-specific
reasons explaining their independent same-country popularity basis. Protocol
assets and documentation define the capability; service, SDK, and Agent
adaptation remain separate implementation work.

## Problem

An empty intent search explains why nothing matched but cannot carry executable
alternatives independently of the failed query. Putting unrelated Offers into
main results would conceal that failure. Existing `force_offer` behavior also
has callers that must not silently lose or relocate successful fallback results.

## Proposal

The closed response gains one optional non-null array, `alternative_offers`,
containing 1–3 closed `{basis, selection_reason, offer}` objects. All three
members are required. The initial `basis` enum contains only
`regional_popularity`. Each `selection_reason` has 1–500 Unicode code points
and at least one character outside ECMAScript `\s`; no trimming or normalization
is implied. It uses response `language` and truthfully explains why this is an
alternative, not a direct query match. It must not expose private ranking data
or internal chain-of-thought.

The nested `offer` reuses the complete current Generic Query projection and
must omit `match_reason` in both thinking modes. `thinking_mode=false` does not
remove `selection_reason`. Static Partner/provider `recommendation_reason` may
remain, but cannot replace that request-specific disclosure. Stable `offer_id`
must be unique across alternatives, including equivalent UUID case variants;
different `offer_instance_id` or reasons do not create distinct stable Offers.

The normative contract is the
[Query API](https://github.com/agentoffernetwork/protocol/blob/main/v1.0/specs/query-api.md#optional-alternative-offers)
and its
[field semantics](https://github.com/agentoffernetwork/protocol/blob/main/v1.0/specs/offer-field-semantics.md#query-response).
The complete
[request/response example](https://github.com/agentoffernetwork/examples/blob/main/v1.0/http/offer-query-alternative-offers.json)
is a synthetic conformance illustration, not an observed service response or
proof of popularity and qualification.

### Selection and safety boundaries

- Finish the existing main Query path first, including `force_offer`. A
  successful fallback stays in main `offers`. Nonempty main `offers` and
  `alternative_offers` are mutually exclusive.
- Keep the five `empty_reason` values and their precedence unchanged. Only
  `below_relevance_threshold` and `no_material` allow alternatives;
  `consent_missing`, `scene_suppressed`, and `frequency_capped` forbid them.
- Evaluate real internal causes and global gates. A collapsed public
  `below_relevance_threshold` is not permission to bypass an internal block.
- Require genuine, current same-country popularity evidence. Never infer the
  country from `language`. Unknown country, no applicable popularity list, no
  qualified candidate, or alternative-branch failure means omit the field;
  do not emit null or an empty list.
- Relax only natural-language relevance. Preserve explicit included and
  excluded categories, budget, other explicit exclusions, eligibility,
  freshness, sensitive-category and action gates. Cross-category alternatives
  require no explicit included-category restriction and still honor exclusions.
  Every alternative needs usable action and presentation resources even when
  the main reason is `no_material`.
- Exclude Catalog/placement-scoped queries, Browse pagination exhaustion, and
  the public test sandbox from global-popularity refill. A provided request
  with `placement_id` or `test_mode=true` forbids alternatives. Internal routing
  and Browse state require producer evidence, not merely response validation.

### Existing Offer and response behavior

Generic supply exclusions (`offer_info.details`, `commercial.quote`, and
`price.tax_status`) remain. Dispatch identity, action, attribution, and
response-owned display-price semantics remain unchanged. `engagement` is
unchanged. Hooks still reference main `offers` only, never alternatives.
Consumers must not merge alternatives into main results or main counts.
API return is not proof of actual display and creates no new charging event.

Schema plus semantic validation establishes payload constraints. It cannot
prove actual country, popularity, qualification, internal gates, or whether
natural-language explanations are truthful and in the declared language.

## Compatibility Impact

This is optional at the new-contract level: unchanged requests and existing
responses without alternatives remain valid. It is **not universally
backward-compatible** with deployed readers: an old closed response schema may
reject the field, while a permissive reader may discard it.

Retain exact `AON-Protocol-Version: 1.0`, body `protocol_version: "1.0"`, and
Offer document marker `"3.0"`. Use the next unused protected v1.0 rN; do not add
a selector, capability-negotiation parameter, or new request field. Upgrade and
certify consumers before enabling producers. First-party readiness does not
certify unknown third-party readers.

`force_offer` retains its default and existing successful fallback behavior.
Retirement is a separate future decision after the alternative capability is
deployed and callers have migrated; this RFC does not deprecate or remove it.

## Alternatives Considered

1. Reuse main `offers` for unrelated suggestions. Rejected because consumers
   would lose the distinction between a match and an alternative.
2. Return only text or follow-up queries. Rejected because they do not provide
   complete actionable Offers for developer or Agent presentation.
3. Immediately replace `force_offer`. Rejected because it changes existing
   caller behavior before migration.
4. Add arbitrary recommendation bases, a request opt-in, or a new selector.
   Rejected because the bounded regional-popularity response extension needs
   none of those additional contracts.

## Rollout

1. Land the accepted RFC, Query specification, field semantics, response
   Schema, validator, protocol types, examples, and documentation sources.
2. Include the RFC and fourth extension fact set in the existing protected
   release admission. Bind the next unused rN to immutable sources and publish
   schema, examples, protocol, and RFC repositories through the existing map.
   Do not rewrite sealed historical release evidence.
3. Audit public commits and follow the same release in documentation, recording
   source/build, protocol publication, and actual documentation deployment
   separately. Local preparation does not prove public delivery.
4. Deployment owners separately implement candidates/gates, service response
   assembly, SDK/Agent preservation and presentation, identity/statistics, and
   consumer-before-producer rollout with real interoperability evidence.
   Protocol publication alone does not claim runtime support.
