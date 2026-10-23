# Beer Passport Data

An open database of beers with a barcode, their country of origin and, where known, their region. It powers the
Beer Passport app and anyone can reuse it under the terms below.

- About 13 000 beers, updated weekly. Every update is listed in [CHANGELOG.md](CHANGELOG.md).
- No personal data: nothing that users of the app record ever reaches this repository.
- Licence: **ODbL v1.0** for the database and **DbCL v1.0** for its contents. Licence and attribution are in [LICENSE](LICENSE).

## Files

Everything lives under `v1/`. Its format never changes: a breaking change would go to `v2/`, and `v1/` would stay.

| Path | What it is |
|---|---|
| `CHANGELOG.md` | One entry per published version: beers new, updated and removed, new beers by country, reference files changed. |
| `v1/manifest.json` | Current version: `built` (Unix time), `tag`, `count`, licence, attribution, sha256 and size of the main files, list of deltas. Small: read it first. |
| `v1/catalog.json.gz` | The whole database, gzip-compressed JSON. |
| `v1/deltas/<built>.json.gz` | Changes since the previous version: `items` (new or changed records) and `removed` (barcodes). |
| `v1/by-code/<prefix>.json` | Records whose barcode starts with those 3 digits, for single lookups. |
| `v1/by-code/index.json` | sha256, size and record count of each `by-code` file. |
| `v1/reference/map_seed.json` | Up to 2 well-known beers per country and per region, without a barcode. |
| `v1/reference/beers.json` | Beers known by name only, without a barcode yet: `{"v": 1, "license", "items"}`. |

Every data file starts with `{"v": 1, "built": …, "license": "ODbL-1.0", …}`.

### Beer record

| Field | Meaning |
|---|---|
| `c` | Barcode, as printed (EAN-13, EAN-8; UPC-A is stored as EAN-13 with a leading `0`). Unique. |
| `n` | Name. |
| `b` | Brand or brewery, if known. |
| `o` | Country of origin, ISO 3166-1 alpha-3 as used by Natural Earth (for example `XKX` for Kosovo). |
| `oq` | How the country was obtained. Missing: from the record itself. `n`: matched by name or brand. `p`: only from the barcode prefix, so it is a guess (the prefix tells where the barcode was registered, not where the beer is brewed). |
| `r` | Region, ISO 3166-2 code as used by Natural Earth, if known. Always inside `o`. |
| `t` | Last modification of the source record (Unix time). |

### Reference beer (`map_seed.json`)

`countries` and `regions` map a country or region code to a list of `{id, n, b, o, r, k}`: `id` is `web:<name>` (checked
by hand) or `wd:<Q-id>` (Wikidata), `b` is the brewery and `k` is `beer` or `brewery`.

### Beer without a barcode (`beers.json`)

`items` is a list of `{id, n, b, o, r, s, f, k}`: `id` is `web:<slug>` or `wd:<Q-id>`, `n` the name, `b` the brewery, `o`
and `r` as in a beer record, `s` the style (an id of the app's style list, only when the name states it), `f` the
fermentation (`top`, `bottom`, `spontaneous` or `mixed`) and `k` is `beer`. `r` and `s` may be missing.

## Using it

Read the manifest from the branch, then fetch the files of the tag it names (tags never change, so they cache well):

```
https://cdn.jsdelivr.net/gh/Homeboy-Studio/beerpassport-data@main/v1/manifest.json
https://cdn.jsdelivr.net/gh/Homeboy-Studio/beerpassport-data@<tag>/v1/catalog.json.gz
https://cdn.jsdelivr.net/gh/Homeboy-Studio/beerpassport-data@<tag>/v1/by-code/541.json
```

`raw.githubusercontent.com/Homeboy-Studio/beerpassport-data/<tag>/v1/…` serves the same files. Check each file against
the sha256 in the manifest (or in `by-code/index.json`).

To stay up to date, apply the deltas newer than your copy in order; if your copy is older than the oldest delta
listed, download `catalog.json.gz` again.

## Terms of reuse

The data is free to use, including commercially, under the ODbL v1.0:

- **Attribute:** keep the notice in [LICENSE](LICENSE) and credit its sources wherever you use or show the data.
- **Share alike:** if you publish an adapted version of the database, release it under the ODbL v1.0 too.
- **Keep it open:** you may add technical restrictions only if you also offer a version without them.

The data comes from collaborative and automatic sources and may contain errors. It is provided as is, without
any warranty.
