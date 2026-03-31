# Contributing

Thanks for helping shape the future of AgentOffer Protocol.

## Scope

This repository manages the RFC (Request for Comments) process for breaking changes, governance updates, and major design proposals.

## Where to Send Changes

- **New RFC proposals**: fork, copy the [template](templates/rfc-template.md), and open a pull request
- **Amendments to open RFCs**: comment on the existing PR
- **Process improvements** (template changes, lifecycle updates): direct pull request
- **Typo fixes in accepted RFCs**: direct pull request

## Writing an Effective RFC

A good RFC should:

1. **Start with the problem** -- explain what doesn't work today and why it matters
2. **Propose a concrete design** -- include field names, types, and example payloads where applicable
3. **Assess compatibility impact** -- will this break existing implementations? How can they migrate?
4. **Consider alternatives** -- show you've thought about other approaches and explain why this one is better
5. **Define rollout** -- how should the change be introduced? Deprecation period? Feature flag?

## RFC Lifecycle

| Status | Meaning |
|--------|---------|
| `draft` | PR opened, discussion in progress |
| `under-review` | Core team is actively evaluating |
| `accepted` | Approved for implementation |
| `rejected` | Not accepted (with rationale documented) |
| `deferred` | Good idea, but not the right time |
| `implemented` | Merged into the protocol spec |

## Issue Routing

- `proposal` -- early-stage idea that may become an RFC
- `process` -- suggestions for improving the RFC workflow itself
- `question` -- clarification about the RFC process or existing proposals

## Before Opening an RFC

1. Check [existing issues](https://github.com/agentoffernetwork/rfcs/issues) and [open PRs](https://github.com/agentoffernetwork/rfcs/pulls) for related proposals
2. Consider opening an issue first to gauge interest before investing in a full RFC
3. Review the [protocol specification](https://github.com/agentoffernetwork/protocol) to understand the current design

## Code of Conduct

By participating in this project, you agree to follow our [Code of Conduct](CODE_OF_CONDUCT.md).
