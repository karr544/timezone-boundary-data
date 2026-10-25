# Notice

## The database

`data/tzq1_d9_all.bin` is a compact, offline lookup table from geographic coordinates to IANA time-zone names.

It is a **Derivative Database** of the time-zone boundaries below, and is made available under the **Open Database License (ODbL) 1.0**: https://opendatacommons.org/licenses/odbl/1-0/ (see `LICENSE.md`).

| | |
|---|---|
| Size | 1,987,457 bytes |
| SHA-256 | `bc2cdee33efa560c9a2b3393f41771bb4ab2e3129eccb022dff3eea7c36a69a6` |

## Sources and attribution

**Time-zone boundaries.** © OpenStreetMap contributors (https://www.openstreetmap.org/copyright), from timezone-boundary-builder release 2026e (https://github.com/evansiroky/timezone-boundary-builder). They are used in the version packaged by geo-tz 8.1.10 (the "alike since 1970" product), and are licensed under the ODbL 1.0.

**Place data.** GeoNames (https://www.geonames.org), licensed under CC BY 4.0 (https://creativecommons.org/licenses/by/4.0/). Inhabited places from the `cities500` file were used, modified, to decide the zone of the smallest cells.

## Changes made to the sources

- The boundaries were sampled on a quadtree of the globe, with root cells of 4° and leaf cells down to 0.0078125° (about 0.87 km).
- A mixed leaf cell takes the zone of its most populous GeoNames place, or else its centre's land zone.
- A leaf cell is flagged "ambiguous" when the land zones sampled in it keep different clocks (different UTC offsets at some instant every 6 h of 2025–2027).
- The result is stored in the TZQ1 v2 format described in `FORMAT.md`.

## Scope of the licence

Only this database is covered by the ODbL. No software is published here.
