<p align="center">
  <h1 align="center">AgentOffer RFCs</h1>
  <p align="center">
    The proposal process for changing <a href="https://github.com/agentoffernetwork/protocol">AgentOffer Protocol</a>.
  </p>
</p>

<p align="center">
  <a href="https://creativecommons.org/licenses/by/4.0/"><img src="https://img.shields.io/badge/license-CC--BY--4.0-blue.svg" alt="License" /></a>
  <a href="https://github.com/agentoffernetwork/rfcs/issues"><img src="https://img.shields.io/github/issues/agentoffernetwork/rfcs.svg" alt="Issues" /></a>
</p>

---

## What Is an RFC?

An RFC (Request for Comments) is a design proposal for a change that affects the AgentOffer Protocol contract. RFCs ensure that breaking changes, new protocol features, and governance updates are discussed openly before implementation.

## When to Open an RFC

| Scenario | RFC Required? |
|----------|--------------|
| New protocol fields or objects | Yes |
| Renaming or removing existing fields | Yes |
| Changes that affect backward compatibility | Yes |
| Governance or contribution process changes | Yes |
| Typo fixes and editorial improvements | No -- open a PR directly |
| Clarity improvements (no semantic change) | No -- open a PR directly |

## How to Submit an RFC

1. Fork this repository
2. Copy [`templates/rfc-template.md`](templates/rfc-template.md) to `rfcs/RFC-NNNN-short-title.md`
3. Fill in the template sections: Summary, Problem, Proposal, Compatibility Impact, Alternatives, Rollout
4. Open a pull request with the title `RFC: <short title>`
5. Engage in discussion on the PR
6. Maintainers will label the RFC as `accepted`, `rejected`, or `deferred`

## RFC Lifecycle

```
Draft  -->  Under Review  -->  Accepted  -->  Implemented
                           -->  Rejected
                           -->  Deferred
```

## RFC Index

| RFC | Title | Status |
|-----|-------|--------|
| -- | No RFCs yet. Be the first! | -- |

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
