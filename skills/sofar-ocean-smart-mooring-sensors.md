---
name: Read Smart Mooring subsurface sensor data
description: Pull subsurface Smart Mooring sensor readings from a Spotter and resolve their sensor configuration.
api: openapi/sofar-ocean-spotter-openapi.yml
operations: [getSensorData, getSensorConfigurationHistory]
---

# Read Smart Mooring subsurface sensor data

## Auth
Pass your account token in the `token` header or query parameter.

## Steps
1. **Get sensor data** — call `getSensorData`
   (`GET https://api.sofarocean.com/api/sensor-data?spotterId=<id>`). `startDate`
   defaults to 24 hours ago, `endDate` to now (ISO 8601). Set
   `includeSoundPressureReadings=true` for raw SPL from hydrophone Spotters on
   cellular.
2. Read the flat `data[]` stream. Each reading has `sensorPosition` (1 = closest
   to the Spotter), `units`, `value`, `unit_type`, `data_type_name`, and a
   `configuration_checksum`. Timestamps are ordered but not unique — multiple
   sensors can report at the same instant.
3. **Resolve configuration** — call `getSensorConfigurationHistory`
   (`GET https://api.sofarocean.com/api/sensor-configuration-history?spotterId=<id>`)
   over the same window and match each reading's `configuration_checksum` to
   determine the sensor configuration at collection time.

## Rules
- Read-only GETs; safe to retry.
- The demo account has no Smart Mooring devices, so the demo token returns empty
  `data[]` arrays here.
- `400 {"status":"error","message":"Device not found"}` = wrong/inaccessible spotterId.
