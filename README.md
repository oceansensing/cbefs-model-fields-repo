# cbefs-model-fields-repo

The CBEFS **fields** — a data repository of the oceansensing ocean map system: its own
Pages site, its own schedule, its own gigabyte, holding no code of its own.

**Live since 2026-09-27** — published to Pages and R2, not drawn on the
website's map. `PLAN.md` is the founding plan;
`CLAUDE.md` carries what must not be got wrong and the shared doc doctrine.

## What it publishes

The Chesapeake Bay Environmental Forecasting System's **physical scalar**
fields: temperature and salinity at the surface and the bottom, and water
level.

| root | quantity |
| --- | --- |
| `sst-cbefs.json` | surface temperature |
| `sss-cbefs.json` | surface salinity |
| `ssh-cbefs.json` | water level (`zeta`) |
| `bottomt-cbefs.json` | bottom temperature |
| `bottoms-cbefs.json` | bottom salinity |

The daily mean stamped noon UTC on today's date. About 0.8 MB each, 3.7 MB a
tree (measured 2026-09-27).

Every root is one regional grid at 0.007 degree (336 x 438, `regional:
true`), `source: Chesapeake Bay Environmental Forecast System (CBEFS),
Virginia Institute of Marine Science` — the citation the data's license asks
for. **A bottom root is s-level 0** (ROMS counts from the seabed up), and its
header carries no depth, as Mercator's `bottomt` does not. The fetcher is the
site's `scripts/fetch-cbefs.py`, shared by the three CBEFS repositories and
scoped here with `--only=`; the workflow runs on `7 1,7,13,19 * * *` since
2026-09-27, after its first dispatched run published.

These products are published **operationally but not drawn on the website's
map** — the owner's call, 2026-09-27. The map's status line still reports
them when they fall behind, which is how their health stays visible.

## Published to R2 alone (since 2026-10-10)

Declared `r2_only` in `pipeline/products.toml`: the same run builds these,
they are left out of this repository's Pages site and its status, and the R2
job publishes them beside the rest (the site pipeline's D13, its note of
2026-10-09). Their roots stay on the `published` branch, as every product's do.

| root | quantity | grid |
| --- | --- | --- |
| `bottomdepth-cbefs.json` | level 0's depth below the free surface, m (beside `bottomt-cbefs` and `bottoms-cbefs`) | 0.007 degree, `regional: true` |
| `temp-cbefs-<depth>m.json` | the temperature at each of the first 17 of Mercator's depths, 0.494 to 40.344 m — every one the bay's water reaches — interpolated from the model's 20 terrain-following levels below the datum; one root a depth named for it to the meter (`-0m` … `-40m`), daily means | 0.007 degree, `regional: true` |
| `sal-cbefs-<depth>m.json` | the salinity at the same depths, daily means | 0.007 degree, `regional: true` |
| `qnet-cbefs.json` | the surface's net heat flux, W/m², positive into the water: the averages file's own daily mean `shflux` (since 2026-10-10) | 0.007 degree, `regional: true` |
| `emp-cbefs.json` | evaporation less rain, mm a day: the averages file's own daily means (since 2026-10-10) | 0.007 degree, `regional: true` |


**Each grid states the span its values cover** (since 2026-10-10): `validFrom`
and `validTo` in its header, the interval its daily mean covers — the day round its noon stamp, as ROMS averages.
**Temperature, salinity and water level publish today and the day ahead**: each
such root is today's and lists the days it publishes in its header
(`frames`: each day's time, span, run where there is one, and file); the day
ahead is the root's name with `.v<its time>` before `.json`
(`temp-cbefs-11m.v20261012T1200Z.json`), with its own file. A file names one time for as long as it
exists, and a day no longer published leaves on the next run.

## Where the data comes from

**Source, read 2026-09-26/27**: VIMS publishes CBEFS output openly, with no
login, through THREDDS/OPeNDAP at
`https://tds.vims.edu/thredds/catalog/abever/ECB_FORECAST_HR/catalog.xml`,
in daily folders (`AVERAGES/`, `HISTORY/`, `SURFACE_VELOCITY/`, `STATION/`).
The model is ROMS ChesROMS-ECB on a **curvilinear** 336 × 564 grid at about
600 m with 20 terrain-following levels; it runs nightly for one nowcast day
and five forecast days, and each day's files were posted around 12:20 UTC.
The daily-average file carries `temp`, `salt`, `oxygen`, `pH`, `alkalinity`
and `Aragonite` on every level, so the surface and the bottom are the top and
bottom s-levels, fetched by OPeNDAP index subsetting; `SURFACE_VELOCITY`
carries hourly surface currents already on rho points and eastward/northward.
The files' own license: *"These data are freely available for public use.
Please cite the Chesapeake Bay Environmental Forecast System (CBEFS), Virginia
Institute of Marine Science, when using this data."* The citation is carried
in every published header's `source` and in the README.

## How it runs

The orchestrator (the site's private `pipeline/`), the fetchers and the
published-file contract all come from `oceansensing.github.io`, checked out at
run time. This repository carries `pipeline/products.toml` and its publish
workflow, and nothing else executable. Each run publishes to GitHub Pages and
to Cloudflare R2 from one build. Sibling repositories of the same model:
`cbefs-model-currents-repo` and `cbefs-model-bgc-repo`.

**Which document gets what, and what "update docs" means across all
twenty repositories, is the doctrine block at the top of `CLAUDE.md`** —
the same text in all twenty, held equal by the site's `check:docs`.

## Structure

```
README.md       what this is
CLAUDE.md       what must not be got wrong, and the shared doc doctrine
PLAN.md         the founding plan and running record
DECISIONS.md    dated one-way decisions, D1 onward
pipeline/       products.toml, the declaration the orchestrator reads
.github/        the publish workflow
```
