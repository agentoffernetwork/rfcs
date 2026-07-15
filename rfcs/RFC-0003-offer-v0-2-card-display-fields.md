# RFC-0003: Offer v0.2 Card Display Fields

**Status:** Accepted / stable  
**Date:** 2026-07-15  
**Contract:** Offer v0.2

## Summary

Extend the formal Offer v0.2 contract with optional user-facing card display
fields. The change covers rating, price unit, fulfillment note, structured
properties, material alt text, and display-ready recommendation reason
semantics.

These fields improve partner-supplied offer cards without changing offer
identity, targeting, action, attribution, or conversion goals.

## Problem

Offer v0.2 already has a complete conversion-goal model, but consumers also
need consistent display inputs for cards. Without protocol-level fields,
partners either overload description text or send custom extension keys that
clients cannot render predictably.

The protocol needs a compact display layer that supports:

- Native rendering by clients when a property type is known.
- Safe plain-text fallback when the producer supplies a display pattern.
- Accessibility text for creative assets.
- Clear separation between user-facing copy and internal ranking signals.

## Proposal

Add the following optional fields to the Offer v0.2 snapshot:

| Field | Meaning |
|---|---|
| `offer_info.rating` | Display rating from 0 to 5 with optional count and source |
| `offer_info.commercial.price.unit` | Display unit for price recurrence or scope |
| `offer_info.commercial.fulfillment_note` | Concise transaction or fulfillment note |
| `offer_info.properties[]` | Ordered structured card highlights |
| `material[].alt_text` | Accessible replacement text for creative assets |
| `offer_info.recommendation_reason` | User-readable reason copy that consumers may display |

`offer_info.properties[]` is intentionally open. The schema validates the
`type` token format, while the protocol document lists recommended property
types for consistent producer behavior. Unknown property types remain valid for
round-trip and future extension.

`properties[].display_pattern` is plain text, not executable markup. It
supports only exact same-item placeholders: `${type}`, `${value}`, and
`${unit}`. Unknown tokens, property access, spaced tokens, and unclosed tokens
are rejected by the semantic validator.

## Compatibility Impact

All new fields are optional. Existing v0.1-compatible payloads are unaffected.
Consumers that do not understand the new fields can ignore them.

Consumers should render known property types natively when possible. For an
unknown property type, consumers may render a valid `display_pattern`; if no
pattern exists, they should hide the property rather than guessing display copy.

## Alternatives Considered

1. Make property types a closed enum.
   Rejected because partner offer highlights vary by vertical, and a closed
   enum would force frequent schema releases.

2. Put all highlights into free-form description text.
   Rejected because clients need structured data for native cards, sorting, and
   compact comparison surfaces.

3. Treat `display_pattern` as the standardization field.
   Rejected because the semantic identifier is `type`; display patterns are
   localized copy and fallback rendering hints.

## Rollout

1. Publish protocol spec updates for Offer v0.2.
2. Publish JSON Schema, TypeScript types, and semantic validator updates.
3. Publish a canonical response example with card display fields.
4. Update docs, SDK guidance, and partner UI surfaces from these source
   artifacts.
