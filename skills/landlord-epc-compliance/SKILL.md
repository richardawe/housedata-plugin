---
name: landlord-epc-compliance
description: Portfolio-level EPC / Minimum Energy Efficiency Standard (MEES) compliance sweep across a list of rental properties — groups by compliance status and surfaces grant eligibility for the ones that fail. Use when a landlord gives a list of addresses or postcodes and wants their compliance or MEES 2030 status checked.
---

# Landlord EPC compliance

You're helping a landlord (or their agent) understand which properties in a portfolio are compliant with the Minimum Energy Efficiency Standard, which are at risk, and what it would cost to fix the ones that aren't — across a list, not one property at a time.

## 0. If the API key isn't configured

If the tool call fails with an authentication error, follow `setup/SETUP.md` before continuing.

## 1. Get the list

If the user hasn't given you a list of addresses/postcodes yet, ask for it. A handful typed inline is fine; for a long list, ask them to paste it one per line. Don't try to guess a portfolio from a single address.

## 2. Run the sweep

Call `check_epc_compliance` with the full list in one call (up to 50 items — if there are more, batch it and tell the user you're doing so). Don't call it once per property; that's what the batch input is for.

## 3. Group and prioritise

Group the results by the `status` field the tool returns (`red` / `amber` / `green` / `unknown` / `not_found`), most urgent first:

1. **Red** — expired certificate, or rating E/F/G. These fail MEES now or are about to. Sort within this group by expiry date (soonest first) — an expired cert is a compliance gap today, not a future one.
2. **Amber** — rating D. Compliant today, but will fail the tightened 2030 threshold (EPC C) without work.
3. **Green** — already C or above. No action needed; mention briefly so the portfolio picture is complete.
4. **Unknown / not found** — no certificate on record, or the address/postcode didn't match. Flag these separately — an unknown is not the same as compliant, and it needs a fresh EPC assessment before anything else can be said about it.

## 4. Surface grants for the failures

For each red/amber property, call `get_grants` (using its `property_id` or postcode) to check what could offset the upgrade cost. Attach the matched schemes to that property's line in the output rather than listing grants separately — a landlord acting on this wants "property X, upgrade to C, £Y available via scheme Z" as one line, not two lists to cross-reference.

## 5. Prioritised remediation order

Order the red/amber properties by cost-to-compliance where the data supports it (cheapest and most urgent — soonest expiry — first), so the landlord has a sequence to work through rather than a flat list. If cost data isn't available for a property, put it after the ones that do have it and say why.

## 6. Report

A compact table: address/postcode, status, rating, expiry date, matched grants, and one-line next step. Close with a one-sentence portfolio summary (e.g. "6 of 14 properties are already MEES-2030 compliant; 5 need work before 2030; 3 have no EPC on record"). State the MEES 2030 threshold as understood at the time of the check and tell the user to confirm the current legal deadline and rating requirement before acting — these have shifted before and this report is not legal advice.
