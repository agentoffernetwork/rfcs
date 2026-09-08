# RFC-0004: Offer Display Price

**Decision status:** Accepted / implemented
**Date:** 2026-09-07
**Contract:** Protocol v1.0
**Current applicability:** Current canonical v1.0 source

## Summary

Add one optional, closed `offer_info.commercial.display_price` object to the
Public Offer and Generic Query response carriers. It carries the amount and
currency that a response producer selected for presentation in the current
Query context, while `commercial.price` remains the original Offer price and
the fallback when the complete display-price object is absent.

The field is response-owned. Partner Offers and OfferProvider success Offers
must reject it, and its presence never changes checkout, settlement,
commission, ranking, or transaction-authoritative price semantics.

## Problem

A Query runtime may identify a user's preferred currency from internal intent,
session, or preference processing and derive a corresponding amount. Before
this RFC, a consumer receiving an Offer could not distinguish that response-
specific presentation value from the original Offer-authored price without an
out-of-contract convention.

The protocol needs one deterministic location for that presentation value. It
must preserve the source price, avoid introducing a second target-currency
input in the Query request, and prevent Partners from submitting an AON-derived
value as a supply fact.

## Proposal

### Wire shape

A Public Offer or Generic Query Offer may include:

```json
{
  "price": {
    "amount": "99.00",
    "currency": "USD",
    "unit": "night"
  },
  "display_price": {
    "amount": "718.42",
    "currency": "CNY"
  }
}
```

`display_price` is optional and is one object, not an array. When present, it
requires exactly `amount` and `currency`; additional members are forbidden.
It does not carry `unit`, `tax_status`, `quote`, `fulfillment_note`, or an
extension object.

`amount` uses the canonical non-negative decimal grammar
`^(?:0|[1-9][0-9]{0,11})(?:\.[0-9]{1,6})?$`. `currency` uses exactly three
uppercase ASCII letters. This syntax does not establish ISO 4217 registry
membership.

### Ownership and carriers

`display_price` is owned by the AON Query response projection. The Public
Offer and Generic Query Offer carriers allow it. Partner Offer and
OfferProvider success carriers prohibit it at their structural boundaries;
Partner semantic validation also rejects it as an AON projection field.

The Query request remains unchanged. In particular,
`response_options.price_currency` is not defined. Target-currency selection
and intent parsing remain implementation-owned inputs to a response producer.

### Production and validation rules

When `display_price` is present:

1. `commercial.price` must also be present.
2. `display_price.currency` must differ from `price.currency`.
3. A zero original amount requires a zero display amount.
4. A strictly positive original amount requires a strictly positive display
   amount. A producer must omit `display_price` when its reliable, rounded
   result would be zero.
5. Both values must describe the same good or service, price unit, and tax
   basis. The producer is responsible for that equivalence.

A producer must omit the complete field when it has no reliable derived amount,
no target display currency, or a target currency equal to the original
currency. Only complete absence permits fallback to `price`. A present `null`,
partial object, malformed value, unknown member, or cross-field violation is a
contract error and must not be treated as absence.

### Consumption rule

Consumers derive a presentation object as follows:

```text
effective_display_price =
  display_price exists
    ? { ...price, amount: display_price.amount, currency: display_price.currency }
    : price
```

`effective_display_price` is explanatory notation, not another wire field.
Only `amount` and `currency` are overlaid. An existing `price.unit` and an
available `price.tax_status` retain only their source-price meaning; the
currency overlay creates no new unit, tax, fee, or total-price guarantee.
`commercial.quote` and `commercial.fulfillment_note` remain sibling data and
are not copied into `display_price`.

### Authority and freshness boundary

`display_price` is a response-scoped presentation value. It is never a
checkout, settlement, or transaction-authoritative price and must not affect
eligibility, budget evaluation, ranking, tracking, Goal commission, or
settlement.

The original `commercial.quote.observed_at` and `valid_until` describe only
the source `price`; they do not establish `display_price` or FX freshness. The
wire object intentionally carries no FX source, FX observation time, rounding
method, or validity evidence. Consumers must treat that absence as a known
contract boundary, not as proof that a conversion is correct, current, or
available for transaction.

## Compatibility Impact

The field is optional, so every conforming v1.0 Offer that omits it remains
valid and consumers continue to use `price`. The transport selector remains
exact `AON-Protocol-Version: 1.0`, the Query request shape is unchanged, and
the Offer document marker remains `"3.0"`.

Because Public Offer objects are closed, strict consumers need an updated
contract before accepting the new field. Downstream implementation is outside
this changeset and follows this accepted contract.

## Alternatives Considered

1. Add `response_options.price_currency` to the Query request.
   Rejected because target currency is already selected by internal intent and
   preference processing; a public request member would create another source
   of truth.

2. Add `price_presentations[]`.
   Rejected because one Offer has at most one target presentation price in one
   response. An array adds selection and ordering semantics without a current
   use case.

3. Replace `commercial.price` with the converted value.
   Rejected because it destroys the original Offer-authored amount and makes
   source-price, attribution, and transaction boundaries ambiguous.

4. Add FX source, timestamp, rounding, and validity members now.
   Rejected for this contract change because no cross-runtime evidence model
   has been selected. Their absence is explicitly documented instead of being
   represented by optional fields with undefined authority.

5. Create Protocol v1.1.
   Rejected because v1.0 is not yet broadly promoted and the additive field is
   being introduced through the next protected v1.0 release candidate.

## Rollout

1. Accept this RFC before admitting the contract change.
2. Update the v1.0 Protocol specifications, public/Partner schemas,
   TypeScript projections, semantic validators, examples, and governance
   inventory as one source-bound change.
3. Publish the source set through the next unused protected v1.0 candidate
   without rewriting historical release evidence.
4. Align Runtime, API/SDK, and consumer surfaces in follow-up changes against
   this accepted contract.
