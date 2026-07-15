<p align="center">
  <h1 align="center">AgentOffer RFCs</h1>
  <p align="center">
    The proposal process for changing <a href="https://github.com/agentoffernetwork/protocol">AgentOffer Protocol</a>.
  </p>
</p>

<p align="center">
  <a href="https://creativecommons.org/licenses/by/4.0/"><img src="https://img.shields.io/badge/license-CC--BY--4.0-blue.svg" alt="License" /></a>
  <a href="https://github.com/agentoffernetwork/rfcs/issues"><img src="https://img.shields.io/github/issues/agentoffernetwork/rfcs.svg" alt="Issues" /></a>
  <a href="https://github.com/agentoffernetwork/rfcs/actions/workflows/lint.yml"><img src="https://github.com/agentoffernetwork/rfcs/actions/workflows/lint.yml/badge.svg" alt="Lint" /></a>
</p>

---

## What Is an RFC?

An RFC (Request for Comments) is a lightweight public proposal for a change that affects the AgentOffer Protocol contract.

Use an RFC when a change would alter wire-level behavior, field semantics, compatibility, or governance. Use a direct PR for wording, examples, links, and readability improvements that do not change the contract.

## When to Open an RFC

| Scenario | RFC Required? |
|----------|--------------|
| New protocol fields or objects | Yes |
| Renaming or removing existing fields | Yes |
| Changes that affect backward compatibility | Yes |
| Governance or contribution process changes | Yes |
| Typo fixes and editorial improvements | No -- open a PR directly |
| Clarity improvements (no semantic change) | No -- open a PR directly |

## PR or RFC?

Use this shortcut before changing public protocol content:

| Change type | Open a direct PR? | Open an RFC first? |
|-------------|-------------------|--------------------|
| Typo, grammar, broken link, heading cleanup | Yes | No |
| Better examples using existing fields | Yes | No |
| More readable wording with no semantic change | Yes | No |
| Reorganizing README/spec navigation | Yes | No |
| New field, object, enum value, or identifier semantics | No | Yes |
| Renaming, removing, or deprecating an existing field | No | Yes |
| Changing compatibility, lifecycle, or governance rules | No | Yes |
| Changing schema validation behavior | No | Yes |

When in doubt, open an issue first and describe whether the change affects wire-level behavior. Maintainers can help route it to a direct PR or RFC.

## How to Submit an RFC

1. Fork this repository
2. Copy [`templates/rfc-template.md`](templates/rfc-template.md) to `rfcs/RFC-NNNN-short-title.md`
3. Fill in the template sections: Summary, Problem, Proposal, Compatibility Impact, Alternatives, Rollout
4. Open a pull request with the title `RFC: <short title>`
5. Engage in discussion on the PR
6. Maintainers will label the RFC as `accepted`, `rejected`, or `deferred`

## Recommended Change Order

For accepted semantic changes, update public surfaces in this order:

1. RFC accepted.
2. Protocol spec updated.
3. JSON Schema and TypeScript types updated.
4. Examples updated.
5. Docs, SDKs, websites, and OpenAPI surfaces follow the updated source reference.

Pure documentation readability changes can go directly to the relevant repository PR and do not need this full sequence.

## RFC Lifecycle

```text
Draft  -->  Under Review  -->  Accepted  -->  Implemented
                           -->  Rejected
                           -->  Deferred
```

The governance process already exists even though the RFC corpus is still empty. In other
words: **the path is available now, while the public proposal history is still young**.

## RFC Index

| RFC | Title | Status |
|-----|-------|--------|
| [RFC-0003](./rfcs/RFC-0003-offer-v0-2-card-display-fields.md) | Offer v0.2 Card Display Fields | Accepted / stable |
| [RFC-0002](./rfcs/RFC-0002-conversion-goals-v0-2-formal.md) | Conversion Goals v0.2 Formal Contract | Accepted / stable |
| [RFC-0001](./rfcs/RFC-0001-conversion-goals-v0.2-draft.md) | Conversion Goals v0.2 Draft | Historical / superseded |

## Current Governance Status

- This repository is the canonical path for breaking changes and new protocol features.
- The public RFC index is intentionally small because AgentOffer Protocol is still early.
- Editorial fixes, broken links, and non-semantic readability improvements should go directly to PRs.

## Status

- **Governance posture:** `Live, early-stage public beta`
- **Adoption note:** The process is ready for use even though the public RFC index is still empty.

## Template

See [`templates/rfc-template.md`](templates/rfc-template.md) for the standard RFC structure:

- **Summary** -- one paragraph overview
- **Problem** -- what issue does this address
- **Proposal** -- the detailed design
- **Compatibility Impact** -- what breaks, what doesn't
- **Alternatives** -- other approaches considered
- **Rollout** -- migration path and timeline

## Related Repositories

| Repository | Purpose |
|------------|---------|
| [`agentoffernetwork/protocol`](https://github.com/agentoffernetwork/protocol) | The specification that RFCs modify |
| [`agentoffernetwork/schema`](https://github.com/agentoffernetwork/schema) | Machine-readable contracts affected by RFCs |
| [`agentoffernetwork/examples`](https://github.com/agentoffernetwork/examples) | Examples updated after RFC implementation |

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on writing effective proposals.

## License

This repository is licensed under [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/).
