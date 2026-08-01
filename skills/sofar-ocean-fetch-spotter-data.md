---
name: Fetch latest Spotter data
description: Discover the Spotters on a Sofar account and pull the most recent waves, wind, temperature, and barometric data from one of them.
api: openapi/sofar-ocean-spotter-openapi.yml
operations: [getDevices, getLatestData]
---

# Fetch latest Spotter data

Use this to get a live snapshot from a Sofar Ocean Spotter buoy.

## Auth
Every request needs your account token in the `token` HTTP header (or `token`
query parameter). Generate/regenerate it at `https://spotter.sofarocean.com/api`.
Regenerating invalidates previous tokens.

## Steps
1. **List devices** — call `getDevices` (`GET https://api.sofarocean.com/api/devices`).
   Read `data.devices[]`; each item has `spotterId`, `name`, and `hullType`
   (`spotter`, `scout`, or `velella`). Pick the `spotterId` you want.
2. **Get latest data** — call `getLatestData`
   (`GET https://api.sofarocean.com/api/latest-data?spotterId=<id>`). Add
   `includeWindData=true`, `includeSurfaceTempData=true`, and
   `includeBarometerData=true` as needed — optional data families are opt-in.
3. Read `data.waves[]` for `significantWaveHeight`, `peakPeriod`, `meanDirection`,
   etc. Battery health is in `data.batteryVoltage` (full 3.8-4.1V, empty 3.0V).

## Rules
- Read-only GETs; safe to retry. No idempotency key needed.
- A `400 {"status":"error","message":"Device not found"}` means the spotterId is
  wrong or not shared with your account — re-check via `getDevices`.
- A `401 {"message":"Authentication Failed"}` or `{"message":"No token provided"}`
  means the token is bad or missing.
- To try without your own devices, use the demo token from the Test the API docs page.
