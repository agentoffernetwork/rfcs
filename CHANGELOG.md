# Changelog

## Flight local schedule times (2026-09-10)

- Accepted and implemented RFC-0005 as the v1.0 source decision for
  airport-local Flight endpoint times and source-provided segment duration.
- Replaced offset-bearing endpoint `at` with `local_at`, required positive
  `duration_minutes`, and kept same-airport connection chronology without
  cross-airport timezone inference.

## Offer display price (2026-09-07)

- Accepted and implemented RFC-0004 as the v1.0 source decision for one
  response-scoped `commercial.display_price` presentation value.
- Kept Query requests and the `1.0` selector unchanged, prohibited the field in
  Partner/Provider supply, and retained the original price as fallback.

## Offer v0.2 card display fields (2026-07-15)

- Accepted RFC-0003 as the stable source for optional card display fields in
  the Offer v0.2 contract.

## Formal v0.2 conversion goals (2026-07-10)

- Accepted RFC-0002 as the stable formal conversion-goals contract.

All notable changes to the AgentOffer RFC process will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [0.1.0] - 2026-03-28

### Added

- RFC template (`templates/rfc-template.md`)
- Contribution guidelines for proposal routing
- Issue templates for feature requests and bug reports
