---
name: housedata-setup
description: First-time configuration check for the HouseData plugin — verifies the API key is set, explains free vs PRISM tiers, and runs a test lookup.
---

# HouseData setup

Run this before the first `search_property` or `/housedata:report` call in a session, or whenever a HouseData tool call fails with an authentication error.

## 1. Check whether the API key is configured

Try calling `search_property` with a well-known postcode, e.g. `"SW1A 1AA"`.

- **It returns matches** → the key is configured and working. Skip to step 4.
- **It returns a 401 / "Valid API key required" error** → the key is missing or invalid. Go to step 2.

## 2. Get a key

Tell the user:

> HouseData needs an API key to look up UK property data. Sign up for a free trial key at **https://housedata.uk/prism** — it takes under a minute and only needs an email and password. Then add the key to this plugin's configuration (`api_key` setting) and reconnect.

Do not attempt to sign up on the user's behalf — this requires an email and password only they should set.

## 3. Re-test

Once the user confirms the key is set, retry the same `search_property` call. If it still fails, check:

- The key was pasted in full (`hd_prism_...`, no trailing whitespace)
- The plugin's MCP connection was reconnected/reloaded after the config change

## 4. Explain tiers

Free-trial keys work for every tool except `get_prism_score`. If `get_prism_score` returns an error mentioning subscription or upgrade, tell the user plainly what's gated and point them at `https://housedata.uk/prism` — never present it as a generic failure.

## 5. Done

Once a lookup succeeds, proceed with whatever the user originally asked for (a report, a compliance sweep, etc.) — don't re-run setup checks on every subsequent call in the same session.
