---
name: property-due-diligence
description: Run structured UK property due diligence for a cash buyer, auction buyer or investor — pulls EPC, planning, flood, price and consent-gap data via the HouseData MCP tools and writes a pre-bid report. Use when the user names a specific UK address or postcode and wants to check it out, assess it, or prepare to bid on it.
---

# Property due diligence

You're helping someone who is about to commit money to a UK property — often on a short deadline (an auction lot, a bid deadline, a viewing tomorrow). The job is to surface what the data shows, flag what needs a human follow-up, and be explicit about what the data cannot tell them. It is not to tell them what to pay or whether to bid.

## 0. If the API key isn't configured

If any tool call fails with an authentication error, follow `setup/SETUP.md` before continuing.

## 1. Disambiguate first

Call `search_property` with whatever address or postcode the user gave you.

- **Zero matches** → tell the user, ask them to check the postcode. Do not invent a property.
- **One match** → confirm it back to the user in one line ("Found 14 Acacia Road, CV4 7AL, EPC rating D") and proceed.
- **More than one match** → list them and ask the user which one they mean. Never pick one for them, even if one "looks right" — a wrong report on the wrong house is worse than a clarifying question.

Once confirmed, use the `property_id` from that match for every subsequent tool call — don't re-search.

## 2. Order of investigation

Work through these in order. Each step can produce a hard blocker that's worth surfacing immediately, even before finishing the rest:

1. **`get_property_summary`** — the headline: EPC, last sale, flood, LPA, planning-risk score, council tax band.
2. **`get_epc_history`** — every EPC certificate for the address, oldest to newest.
3. **`get_planning_history`** — applications and decisions nearby, plus the area's planning-risk score.
4. **`get_flood_risk`** — river/sea and surface-water risk, reported separately.
5. **`get_price_history`** — full Price Paid transaction history.
6. **`get_prism_score`** (if the account has PRISM access) — consent-gap score and remediation paths. If it returns a subscription-gated error, note it in the report as "not available on this plan" rather than treating it as a failure.

**Hard blockers — call these out as soon as you see them, don't bury them at the end of the report:**
- Flood zone 3 (river/sea) with no mitigation on record
- A listed building with EPC/floor-area changes but no matching planning record (works may be unconsented — listed building consent breaches are a criminal offence in the UK, not just a planning matter)
- An EPC certificate lodged in the last 12 months showing a materially larger floor area than the previous certificate, with no corresponding planning application in the same window

## 3. What each signal means for a cash buyer

- **Floor-area jump between EPCs, no matching planning record** — the single highest-value flag `get_epc_history` can produce. It means the property was extended or converted without going through planning/building control (or the works predate the loft/kitchen extension and are permitted development — you can't tell from EPC data alone, which is exactly why this is a flag to *investigate*, not a verdict). Cost to resolve ranges from a retrospective planning application and building-control regularisation certificate (a few hundred to a few thousand pounds and weeks of delay) up to requiring works to be undone if refused. Tell the user this is worth a direct question to the seller's agent before bidding, not after.
- **EPC rating E/F/G on a property being bought to let** — MEES compliance risk. Use `check_epc_compliance` logic (rating + expiry) to state whether it's already non-compliant or heading that way, and reference `get_grants` for money that could offset the upgrade cost.
- **Flood zone 3 with no recent EA-backed mitigation** — affects both insurability and resale. Note it plainly; don't soften it into "some flood risk."
- **Planning-risk score low + a live application nearby** — a live application (someone else's) can change what's next door before completion. Surface it, don't score it as if it's about this property.
- **No Price Paid record at all** — either a genuine new build (check EPC transaction_type) or an off-market/inherited transfer that never went through a taxed sale — both are normal, but say which one the data suggests.

## 4. What the data cannot tell you

Always name these explicitly in the report — a cash buyer is carrying their own risk here and needs to know where the data stops:

- No tenure or title detail (freehold/leasehold, restrictive covenants, boundary disputes) — that needs the title register (HM Land Registry, ~£3) and, for leasehold, the lease itself.
- No structural survey — EPC and planning data say nothing about damp, subsidence, roof condition, or asbestos.
- No legal search results (local authority searches, drainage, environmental) — a conveyancer runs these separately.
- Price Paid and EPC data lag reality by weeks to months; a very recent sale or improvement may not show up yet.

## 5. Write the report

Use `report-template.md` as the structure. Keep it scannable — this is being read against a bid deadline, not at leisure. State findings and unknowns; never phrase anything as "you should bid X" or "this is a good/bad deal" — that crosses from due diligence into advice the user hasn't asked for and the data doesn't support.
