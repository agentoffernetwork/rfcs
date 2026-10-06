# RFC-0010: Query Constraint Filters

**Decision status:** Accepted

## Summary

Extend current v1.0 Query `constraints` with optional `offer_types` and
`listing_source_names` arrays. Define their structure, inference precedence,
matching and no-match semantics in canonical protocol sources. Do not add
`listing_source_kinds`. This decision covers protocol upgrade and release
preparation only; downstream implementation is separate work.

## Problem

Category constraints cannot express a caller's required public Offer type or
listing source. Inferring a source from free text also cannot express an
explicit override or distinguish omission from clearing a prior filter.

## Proposal

- `offer_types` accepts the existing public enum: `physical_product`,
  `digital_goods`, `content`, `online_service`, `offline_service`.
- `listing_source_names` accepts strings of 1–160 Unicode code points before
  normalization. Matching trims only ASCII whitespace U+0009–U+000D and U+0020
  and folds only ASCII A–Z to a–z on both names. Other characters remain exact.
  A normalized blank is invalid. No aliases, substring matching, translation
  or platform-family expansion is performed.
- Both arrays reject duplicate original strings, `null`, scalar values and
  invalid members. Distinct original strings that normalize equally remain
  valid and have set semantics.
- Omission preserves existing behavior and inference. A nonempty array
  overrides same-dimension inference. Explicit `[]` clears that dimension's
  AON filtering and prevents inference reintroducing it within the request.
  This does not control a third-party Provider's internal search algorithms.
- Members combine with OR; dimensions combine with AND, including existing
  category constraints. Missing, unknown or invalid public Offer fields cannot
  satisfy an enabled filter. A valid unknown source name remains a constraint
  and can yield an ordinary no-match result; it is never silently discarded.
- The rules apply to Query results, including forced and alternative offers;
  those paths cannot relax explicit filters. Query Helper patches preserve
  omitted fields, replace provided arrays (including `[]`), and reject `null`.
- The OfferProvider request shares the Query constraint structure. Contract
  version alone does not prove a Provider supports the extension.

The canonical
[Query specification](https://github.com/agentoffernetwork/protocol/blob/main/v1.0/specs/query-api.md)
is the normative behavior reference.

## Compatibility Impact

Existing requests that omit these fields remain valid with their existing
semantics. New fields extend a closed object, so old closed readers may reject
them. The exact `1.0` selector is unchanged; this is not a guarantee that every
existing v1.0 runtime or Provider accepts or enforces the extension. Historical
v0.3 contracts are unchanged. Source conformance tests do not certify runtime
recall, inference or filtering behavior.

## Alternatives

A source-kind constraint is deferred. Alias expansion would make identity
matching dependent on a mutable registry, so explicit source names use the
bounded normalization above. Treating empty arrays as omission would remove
the caller's ability to suppress inference and is not adopted.

## Rollout

Update canonical Schema, types, semantic validators, fixtures, specifications
and this RFC together. The `query_constraint_filters` entry in contract-extension
set format v8 binds the sources and RFC closure to one immutable candidate
commit using existing protected admission. Use the next unused protected
`protocol-v1.0.0-rN` only during a separately authorized publication; retain all
historical manifests and receipts unchanged.

This work does not modify services, SDKs, MCP, Action, OpenAPI, websites,
databases, search indexes or downstream release followers. Their owners must
establish support separately before clients emit new fields. Source completion,
public publication and runtime deployment are distinct evidence states.
