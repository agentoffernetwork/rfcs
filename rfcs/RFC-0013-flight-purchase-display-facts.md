# RFC-0013: Flight Purchase Display Facts

**Decision status:** Accepted / implemented
**Implementation status:** Contract candidate in canonical sources
**Contract:** Protocol v1.0
**Availability:** Public release and runtime rollout require separate evidence

## Summary

Extend the closed Flight Offer profile with optional facts commonly shown
while comparing and purchasing flights: carrier identity and brand assets,
airport time zones, connections, standardized planned equipment, cabin
facilities, fare-specific rights, and estimated emissions. These facts can
come from a supplier, a licensed reference source, or a separate enrichment
process. Their placement in the contract does not claim that a current source
has complete coverage.

## Proposal

| Scope | Optional fields | Rule |
| --- | --- | --- |
| Carrier on a segment | `marketing_carrier.logo_url`, `operating_carrier`, `operating_flight_number` | Logos identify their named carrier and require stable HTTPS assets with display rights. Operating identity needs dated-flight evidence; a codeshare hint alone is insufficient. |
| Scheduled segment | `standard_aircraft_type`, `cabin_amenities` | Standard codes and amenities require verified equipment or cabin evidence. The already defined source-reported `planned_aircraft` remains a separate observation. |
| Airport endpoint | `timezone` | Use a verified IANA identifier; keep `local_at` as an offset-free schedule. |
| Leg connection | `connections[]` | One ordered item per adjacent segment pair, indexed from zero. `airport_change` must agree with endpoint codes. A different connecting airport is allowed only with the explicit connection facts. Stopovers within a segment remain `stops[]`. |
| Exact itinerary quote | `fare_details` | Components refer to existing leg and segment indexes and a quoted traveler type. Baggage allowances, fare brand, booking class, change/refund policies, seat selection and self-transfer facts must come from that seller's quote. `price_basis=reference` forbids `fare_details`. |
| Whole itinerary | `emissions` | CO2e is an estimate with methodology and calculation time. A relative comparison is signed against a comparable-route typical flight. |

Optionality is meaningful: an omitted field means it is not established for
that Offer. `false` in facilities or ticketing is an affirmative source fact,
not a default. A baggage allowance of zero pieces explicitly means none;
allowances cannot be inferred from a generic free-luggage flag. A fee is tied
to its quoted conditions, and the original `commercial.price` plus
`commercial.quote` remain the price and freshness authority. This change does
not define a final checkout amount.

Operational status, delays, gates, baggage carousels and boarding passes have
different refresh and identity requirements; they belong to flight-status or
passenger-order capabilities. Search rankings, price calendars and platform
guarantees also remain outside a single Flight Offer's shared facts.

## Compatibility and rollout

Existing Offers remain valid. Older strict v1.0 readers may reject the newly
allowed keys in closed Flight objects. Admit this accepted RFC, Schema,
types, validators, specifications and examples together in the next unused
protected v1.0 rN release. Upgrade readers before emitting new fields.
Publication, source coverage, enrichment rollout and live Offer emission are
independent steps. A dedicated RFC-0013 extension class and fixed source
inventory must pass protected contract-extension admission. Historical release
evidence remains immutable.
