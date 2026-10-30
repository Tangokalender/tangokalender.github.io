# CLAUDE.md

This is the **Oslo data repo** of Tangokalender (https://tango.oslo.no/): `events/` (one JSON file per event,
`events/YYYY/MM-month/YYYY-MM-DD-<id>.json`), `venues.json` (geocoding cache), `site.toml` (site settings),
`assets/og-image.png` (link preview), `assets/icons/` (favicon and home-screen icons, `bin/make_icons.jl`), the rendered issue forms (`.github/ISSUE_TEMPLATE`) and thin workflows.
All code, the schemas, the form templates and the reusable workflows are in the package repo
https://github.com/Tangokalender/TangoKalender.jl (see its CLAUDE.md); `Project.toml` pins the version.
Local checkouts in `/workspace`: this repo is `OsloEvents/`, the code repo `TangoKalender.jl/` (each folder named after
its content; the GitHub names stay `tangokalender.github.io` and `TangoKalender.jl`).

- Validate before committing: `julia --project=. -m TangoKalender validate events` (after `Pkg.instantiate()`).
- Bulk edits: `julia --project=. -m TangoKalender edit TERM… KEY=VALUE --preview`, then without `--preview`.
- New addresses: `julia --project=. -m TangoKalender geocode events` (network; writes venues.json).
- Never edit `.github/ISSUE_TEMPLATE` by hand: change the templates in the package and run
  `julia --project=. -m TangoKalender templates`.
- Event rules (schema `https://tango.oslo.no/schema/tango-event.schema.json`): every event is dated; series
  share a `series` id with one file per date; cancelled dates get `"status": "cancelled"` instead of being deleted;
  times are Oslo local time with offset (`+02:00` summer, `+01:00` winter). No personal contact details.
  Titles are the event's own name without the organiser («Nybegynnerkurs i tango», not «… (Tango Puente)»);
  the list shows the organiser next to it. Keep names that are the event's real name («Otra Milonga»).
