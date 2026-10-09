# RFC-0012: Game Supply Profile

**Decision status:** Accepted / implemented
**Publication status:** Candidate source; protected v1.0 release pending

## Summary

Register a third stable v1.0 supply Offer profile, `game`, whose closed
`details.data` carries a single required fact: `downloads`, the
source-reported cumulative download or install count.

## Problem

Game Offers have no structured way to state how widely a game is installed.
`offer_info.rating.count` counts ratings, not downloads, and
`offer_info.properties[]` is a bounded presentation list that must not carry
profile-standard facts. Producers therefore either omit download counts or
place them in free-form copy that consumers cannot read reliably.

## Proposal

Add `game` to the v1.0 Supply Offer Profile Registry:

```json
{
  "offer_info": {
    "category": { "id": "hobbies_games_leisure.toys_games.games.online_games_puzzles" },
    "details": { "profile": "game", "data": { "downloads": 1000000 } }
  }
}
```

`details.data` is closed and requires `downloads`, a positive integer. When the
source publishes a range or threshold such as `1M+`, the producer sends its
lower bound (`1000000`); it must not round up, estimate, or combine counts from
different sources. The value is descriptive content, not an AON endorsement or
ranking signal.

The semantic validator binds `game` to `hobbies_games_leisure.toys_games.games`
and its descendants. Unlike `flight` and `hotel_rate`, `game` adds no
`offer_type`, `action.consumer_action`, or commercial requirement.

## Compatibility Impact

Existing Offers are unchanged and remain valid. The exact
`AON-Protocol-Version: 1.0` selector is unchanged. A strict v1.0 reader that
validates `details.profile` against the previous two-value list rejects a
`game` Offer, so AON SDKs and other strict readers must upgrade before
producers emit `game` details. No capability negotiation or dual payload is
introduced.

## Alternatives

A `downloads` entry in `offer_info.properties[]` needs no protocol change but
is untyped and conflicts with the rule that properties do not duplicate profile
facts. A generic `offer_info.install_count` would apply to every category
without a binding. A broader `app` profile was deferred until more app facts
are needed.

## Rollout

The accepted `game` profile must pass protected profile and contract-extension
admission before publication. Bind this RFC, the registry Schema, types,
semantic validator and specifications to one candidate source commit and use
the next unused protected `protocol-v1.0.0-rN` through the existing release
flow. Release upgraded AON SDKs before any producer emits `game` details.
Source acceptance, protected publication, reader deployment and producer
activation are distinct states.
