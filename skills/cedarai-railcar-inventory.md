---
name: Track and move railcar inventory
description: List a carrier's railcar inventory, review a car's history, and move equipment between tracks/spots in Cedar ARMS.
api: openapi/cedarai-external-openapi.yml
operations: [listInventory, listStationTracks, listEquipmentHistory, moveEquipment, switchRequestEquipment]
---

# Track and move railcar inventory

Use the Cedar ARMS External API to see where a carrier's railcars are and to move them.

## Auth
Send on every request (HTTP POST, `Content-Type: application/json`):
- `x-arms-api-key`: your region-scoped Cedar key (US host `api-lg.arms.cedarai.com`, EU host `api-lg.arms.cedarai.se`).
- `x-arms-assume-user`: email of a user in a user group bound to the key.
Include `carrierId` as a query parameter on each call. See `authentication/cedarai-authentication.yml`.

## Steps
1. Resolve station/track ids with `listStationTracks` — station, track and tag ids used as filters come from here.
2. Call `listInventory` with filters (`loadStatus`, `aarCarType`, `station`, `track`, `tag`). Page with `pageSize` + `pageNextToken`/`pagePrevToken` (see `conventions/cedarai-conventions.yml`).
3. For a specific car, call `listEquipmentHistory` filtered by equipment and time range.
4. To relocate equipment, call `moveEquipment` (supports sequencing and batch moves), or `switchRequestEquipment` to submit switch requests to spots/tracks.

## Rules
- Pagination is cursor-based; never construct page tokens yourself — reuse the returned `nextToken`.
- Errors: 401 = key/assumed-user not in a bound group; 403 = user lacks permission; see `errors/cedarai-problem-types.yml`.
