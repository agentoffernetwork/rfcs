# RFC-0009: Agent Conversion Webhook Click, Application, Placement and Net USD Amount

**Decision status:** Accepted

## Summary

Revise the AON → Agent conversion webhook payload in place for wire versions
`0.2`, `0.3` and `1.0`: carry the AON click id, the Developer Application id
and an optional placement id, and redefine `amount` as the Developer's
expected net earning in USD. This is a **BREAKING** change to the v1.0 Agent
webhook payload with no compatibility window.

## Problem

WS-52 hosted offerwall points adapters credit end users from the Agent
webhook. They need the attributed click and placement to find the user and
surface that earned the conversion, and they need the Developer's own net
earning, not the gross Partner amount, to size the reward. The previous
payload carried a dispatch-level `aon_tracking_id`, an `agent_id` that is not
the Developer Application identity, and a gross `amount` in the Partner's
currency that CPA conversions could omit.

## Proposal

The frozen body serializes these fields in this order; `?` marks a field that
is omitted, never `null`, when absent:

`event_id`, `event_type`, `aon_click_id?`, `offer_id`, `application_id`,
`placement_id?`, `event_name`, `amount`, `currency`, `sub_id?`,
`sub_id_2?` … `sub_id_5?`, `timestamp`.

- `aon_tracking_id` is replaced by optional `aon_click_id`: an opaque AON
  click id that begins with `aci_`, omitted when the conversion has no
  attributed click. Receivers treat it as an opaque string.
- `agent_id` is replaced by required `application_id`, the Developer
  Application id.
- Optional `placement_id` is added.
- `amount` is the Developer's expected net earning: settlement commission
  minus platform fee, converted to USD with the settlement's frozen FX rate,
  rounded to 2 decimal places and rendered as a canonical JSON number.
  `currency` is always `USD`. Both are always present; a non-billable
  conversion sends `"amount":0`. A later risk freeze or reversal is not
  re-sent.
- Receiver idempotency is keyed by `(application_id, event_id)`.

Goal grammar, signing, headers, size bound and retry schedule are unchanged.
See the canonical
[Postback specification](https://github.com/agentoffernetwork/protocol/blob/main/v1.0/specs/postback.md).

## Compatibility Impact

**BREAKING.** Receivers that read `aon_tracking_id` or `agent_id`, or that key
idempotency on `agent_id`, must change. Closed v1.0 receivers reject the new
fields. There is no dual-field or compatibility window because there are no
production receivers of this webhook; the product owner confirmed this. The
exact v1.0 selector is unchanged; the payload revision is recorded under the
protected revision policy as an incompatible contract revision.

## Alternatives

Adding the new fields beside the old ones would keep a misleading gross
`amount` and a second identity key. Publishing a new wire version would add a
selector with no receivers to migrate. Neither is adopted.

## Rollout

Ship the Schema, types, fixtures, examples, specification and SDK verifiers
together with the producer. Bind the revised Agent webhook sources to the same
candidate commit through `agent_conversion_webhook_payload` contract-extension
admission; use the next unused protected `protocol-v1.0.0-rN`. Historical
release evidence remains immutable. Source acceptance does not claim
publication, SDK distribution, deployment or receiver adoption; each requires
separate evidence.
