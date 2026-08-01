---
name: Query historical and area-based Spotter data
description: Pull a time window of data from one Spotter, or search all your registered Spotters within a geospatial area.
api: openapi/sofar-ocean-spotter-openapi.yml
operations: [getWaveData, searchData]
---

# Query historical and area-based Spotter data

## Auth
Pass your account token in the `token` header or query parameter.

## Time-window query (single Spotter)
Call `getWaveData` (`GET https://api.sofarocean.com/api/wave-data?spotterId=<id>`).
- `startDate` / `endDate` are ISO 8601 (e.g. `2021-01-01T07:00:00Z`); `endDate`
  defaults to now.
- `limit` caps samples per type (default 20, max 500; max 100 when
  `includeFrequencyData=true`).
- Opt into extra families with `includeWindData`, `includeSurfaceTempData`,
  `includeFrequencyData` (needs `processingSources=hdr` or `all`),
  `includePartitionData`, `includeBarometerData`, `includeTrack`.
- Shared (not registered) Spotters only expose the past 30 days.

## Area search (all registered Spotters)
Call `searchData` (`GET https://api.sofarocean.com/api/search`). **Beta — params
may change.**
- `shape` = `circle` (needs `radius` in meters, one `shapeParams` lat,lon pair) or
  `envelope` (two pairs). `shapeParams` are SRID 4326 coordinates.
- `startDate`, `endDate` required. `pageSize` defaults to 500.
- Follow `metadata.page.nextPage` while `metadata.page.hasMoreData` is true.

## Rules
- Read-only GETs; safe to retry.
- `400 {"status":"error","message":"..."}` signals a bad radius, wrong
  shapeParams pair count, or a non-ISO-8601 date. Fix the parameter and retry.
