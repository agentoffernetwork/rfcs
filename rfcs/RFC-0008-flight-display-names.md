# RFC-0008: Flight Display Names

**Decision status:** Accepted

## Summary

Add optional Flight endpoint `city_code`, `city_name`, and optional
`marketing_carrier.name` to the existing v1.0 Flight details profile.

## Problem

Airport and airline codes alone cannot supply localized city titles and airline
labels. Consumers need translated source names without guessing from codes.

## Proposal

Endpoints retain required `airport_code` and `local_at`; optional `city_code`
contains three uppercase letters. City codes and names are independent optional
facts and do not prove airport membership. The marketing carrier retains
required `code`; `name` names that marketing airline, not its operating carrier.
Names are non-null strings with a character outside Unicode White_Space plus
U+FEFF. All affected objects remain closed. See the canonical
[semantics](https://github.com/agentoffernetwork/protocol/blob/main/v1.0/specs/offer-field-semantics.md#flight-city-and-marketing-carrier-display-names).

Suppliers provide translated text. An actual content-language change omits each
new name lacking a target translation; unchanged content language preserves
source names. Request-language mismatch alone does not strip remote names.
Existing code identity, scheduling, matching and price semantics are unchanged.

## Compatibility Impact

New readers accept omission. Old strict readers can reject additions. The exact
v1.0 selector remains unchanged under the existing protected revision policy.

## Alternatives

Code-derived dictionaries cannot establish supplier language or marketing
identity. Reusing airport names mislabels cities. Neither is adopted.

## Rollout

Upgrade readers before enabling producer emission. Bind this RFC, Schema,
types, semantic validator and examples to the same candidate commit through
`flight_display_names` contract-extension admission; use the next unused
protected `protocol-v1.0.0-rN`. Historical release evidence remains immutable.
Source acceptance does not claim publication, SDK distribution, deployment,
external adapter name supply or UI adoption. Each requires separate evidence.
