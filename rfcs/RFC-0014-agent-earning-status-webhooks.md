# RFC-0014: Agent Earning Status Webhooks

**Decision status:** Accepted / implemented
**Date:** 2026-10-09
**Protocol line:** v1.0

The approved Developer earning-status webhook feature adds four independently
subscribed snapshots to the existing Agent callback endpoint. Existing conversion
payload constraints and signing bytes remain unchanged. The exact v1.0 selector
is retained; closed receivers must upgrade before enabling the new subscriptions.

The existing `postback-agent-payload-v1.0.json` entry becomes `oneOf` with five
mutually exclusive closed branches. No separate earning schema or verifier is
introduced. Python `verify_agent_conversion_webhook` and TypeScript
`verifyAgentConversionWebhook` verify raw bytes first and return the conversion
or earning payload; TypeScript callers narrow on `eventType`.


## Earning status events

The existing Agent payload schema uses five closed `oneOf` branches, selected by
`event_type`: `conversion`, `earning_confirmed`, `earning_available`,
`earning_paid_out`, and `earning_rejected`. The conversion branch and its signed
bytes are unchanged. All events use the same endpoint, headers, raw-body HMAC,
2 MiB limit, durable idempotency rule and five-attempt retry ladder.

| Event | Meaning |
| --- | --- |
| `earning_confirmed` | The associated Partner statement has been confirmed. |
| `earning_available` | The earning has become available for payout. |
| `earning_paid_out` | AON has paid out this earning in full; this does not certify receipt in the developer's bank account. This state is terminal: AON does not register further earning status events for it. |
| `earning_rejected` | The earning was rejected by the merchant, reversed, or marked refunded in a review. |

Earning bodies have this exact field order. Optional fields are omitted, never null:

```text
event_id, event_type, earning_id, sequence, conversion_event_id, offer_id,
application_id, placement_id?, amount, currency, reason?, timestamp
```

| Field | Meaning |
| --- | --- |
| `event_id` | `earning:` followed by the immutable outbox UUID; unchanged on retry. |
| `event_type` | One of the four earning event names above. |
| `earning_id` | Lowercase hyphenated settlement UUID, identical to the Earnings API id. |
| `sequence` | Positive JSON integer increasing within this earning; gaps are allowed. |
| `conversion_event_id` | The originating conversion's event id, even if its webhook was never delivered. |
| `offer_id` | The Earnings API Offer identity; canonical resolution may differ from the conversion's Offer id. Join events using `conversion_event_id`. |
| `application_id` | Developer Application id. |
| `placement_id` | Optional placement identity. |
| `amount` | Net earning in USD, including review adjustments; rejection carries the last observed live amount, or the original amount when none was observed. |
| `currency` | Always `USD`. |
| `reason` | Required only for `earning_rejected`; forbidden on the other earning branches. |
| `timestamp` | Business time of this state, RFC3339 seconds in UTC (`...Z`); not a sorting key. |

The `reason` closed set is `merchant_cancelled`, `duplicate`, `invalid_order`,
`fraud`, `returned`, `reversed`, or `refund`; operator free text is never sent.
Conversion-only fields (`event_name`, `aon_click_id`, `sub_id` through `sub_id_5`)
are forbidden in earning bodies.

Use `(application_id, event_id)` for deduplication. Track the largest `sequence`
per `earning_id` for the latest state; delivery can be duplicated or arrive out
of order, and timestamps need not increase. Do not add the amounts of these
snapshots together. Amounts can differ between events because of review
adjustments or USD rounding (even by one cent); use the amount of the earning
event with the largest sequence. If an adjustment and rejection occur between
two observations, the rejection may carry the amount before the adjustment.
In rare cases a rejection is withdrawn and the earning continues.

Each event has an independent subscription. The first earning subscription
establishes a baseline at the configuration transaction's commit time: existing
states are not replayed. Reopening subscriptions after all four were off also
establishes a baseline; changes while all were off are not backfilled. While any
earning event is enabled, all earning status changes are observed, but only
currently subscribed events are registered. Opening another switch does not
rebuild that baseline. Disabling a switch stops new event registration; already
registered events continue delivery. Skipped registrations may consume sequence
numbers. A risk freeze emits no earning event. Available may return to confirmed;
unsubscribed intermediate states are not guaranteed to arrive.

## Conformance and publication

`postback-agent-earning-webhook-v1.0.json` contains synthetic input objects,
exact raw bodies and fixed HMAC signatures for all four events. Core renders the
same inputs byte for byte; SDKs verify the original signed bytes. Contract tests
reject cross-branch fields, unknown event types, noninteger sequences, invalid
UUIDs and unknown rejection reasons. Existing conversion fixtures remain intact.

This decision authorizes source implementation. Registry publication, protected
protocol admission and deployed-runtime certification remain separate release
steps; this RFC does not claim any of them has completed.
