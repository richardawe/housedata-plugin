# HouseData UK Property (Claude plugin)

UK property due diligence — EPC history, price history, flood risk, planning history and PRISM consent-gap scoring — from inside Claude Code and Cowork, without opening a browser.

> "I'm bidding on 14 Acacia Road, CV4 7AL on Thursday. Pull the data and tell me what to investigate before I commit."

## What's included

| Component | What it does |
|---|---|
| MCP server (`https://housedata.uk/mcp`) | 11 read-only tools that hit the HouseData API — search, EPC, price history, area market trend, flood risk, planning history, broadband, PRISM consent-gap score, EPC/MEES compliance, grants |
| `property-due-diligence` skill | The method: disambiguate → order of investigation → what each signal means for a cash buyer → what the data can't tell you → report structure |
| `landlord-epc-compliance` skill | Portfolio-level MEES 2030 sweep, grouped by status, with grant eligibility attached |
| `/housedata:report <address>` | Runs the due diligence skill end to end |
| `/housedata:portfolio <list>` | Runs the compliance skill across a list of properties |
| Setup skill | Guides first-time API key configuration |

## Install

```
/plugin install housedata@<marketplace>
```

Then configure your HouseData API key when prompted (or via `/plugin config`).

## Get an API key

Sign up at **[housedata.uk/prism](https://housedata.uk/prism)** — free trial keys work for every tool except PRISM consent-gap scoring, which requires an active subscription.

## Every tool is read-only

Nothing in this plugin can save a property, send a message, spend money, or write anything back to HouseData. It only reads.

## Data sources

All data is aggregated from official UK open datasets, refreshed on the schedule each source publishes:

- **EPC ratings & history** — [DLUHC Energy Performance of Buildings Register](https://epc.opendatacommunities.org) (Open Government Licence v3.0)
- **Price history** — [HM Land Registry Price Paid Data](https://www.gov.uk/government/collections/price-paid-data) (OGL v3.0, Crown copyright)
- **Area market trend** — [HM Land Registry UK House Price Index](https://landregistry.data.gov.uk/app/ukhpi) (OGL v3.0)
- **Flood risk** — [Environment Agency Flood Map for Planning](https://flood-map-for-planning.service.gov.uk) (OGL v3.0)
- **Planning history** — local planning authority registers, aggregated
- **LPA boundary** — [planning.data.gov.uk](https://www.planning.data.gov.uk) / postcodes.io (OGL v3.0)
- **Broadband** — [Ofcom Broadband and Mobile Coverage Checker](https://checker.ofcom.org.uk) (single-address lookups only)
- **Council tax band** — Valuation Office Agency (Crown copyright)
- **PRISM consent-gap score** — Housedata's own model, built on the sources above

Every tool response carries a `_source` (or `_sources`) field naming exactly which of these it used and its licence, so attribution travels with the data rather than living only in this README.

## Limitations

- No tenure, title, or leasehold detail — this is not a title deed search.
- No structural survey data.
- No legal search results (local authority, drainage, environmental) — a conveyancer runs these separately.
- Source data lags reality by weeks to months.

This plugin surfaces findings and flags for further investigation. It does not, and is not intended to, tell you what to pay or whether to bid.

## Privacy

HouseData does not store the addresses or postcodes you look up beyond what's needed for API rate limiting and abuse prevention. See [housedata.uk/privacy](https://housedata.uk/privacy) for the full policy.

## Support

Issues and feature requests: [github.com/richardawe/housedata-plugin/issues](https://github.com/richardawe/housedata-plugin/issues)
