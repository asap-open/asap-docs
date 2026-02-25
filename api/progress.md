# Progress API

Analytics endpoints for training consistency, volume, and muscle distribution.

## Base URL

```
http://localhost:3000/api/progress
```

All endpoints require authentication.

---

## Range Parameter

All progress endpoints accept an optional `range` query parameter to filter by time period:

| Value | Description    |
| ----- | -------------- |
| `1W`  | Last 7 days    |
| `1M`  | Last 30 days   |
| `3M`  | Last 3 months  |
| `6M`  | Last 6 months  |
| `1Y`  | Last 12 months |
| `ALL` | All time       |

If omitted, all-time data is returned.

---

## Consistency Heatmap

Returns per-day session counts, suitable for rendering a calendar heatmap.

**Endpoint:** `GET /progress/consistency`

**Query Parameters:** `range`

**Response:** `200 OK`

```json
[
  { "day": "2026-02-01", "value": 1 },
  { "day": "2026-02-03", "value": 2 },
  { "day": "2026-02-05", "value": 1 }
]
```

Each entry represents a date on which at least one session was logged. `value` is the number of sessions on that day.

---

## Volume Stats

Returns per-day total training volume (weight × reps summed across all sets), suitable for a bar or line chart.

**Endpoint:** `GET /progress/volume`

**Query Parameters:** `range`

**Response:** `200 OK`

```json
[
  { "day": "2026-02-01", "volume": 12500 },
  { "day": "2026-02-03", "volume": 9800 }
]
```

---

## Muscle Distribution

Returns a breakdown of training volume by primary muscle group, weighted by number of sets performed. Suitable for a radar/spider chart.

**Endpoint:** `GET /progress/muscles`

**Query Parameters:** `range`

**Response:** `200 OK`

```json
[
  { "muscle": "Chest", "value": 36 },
  { "muscle": "Back", "value": 48 },
  { "muscle": "Legs", "value": 30 }
]
```

---

## Example Usage

```javascript
// Consistency heatmap for the last 3 months
const consistency = await fetch(
  "http://localhost:3000/api/progress/consistency?range=3M",
  { headers: { Authorization: `Bearer ${token}` } },
).then((r) => r.json());

// Volume chart for the last month
const volume = await fetch(
  "http://localhost:3000/api/progress/volume?range=1M",
  { headers: { Authorization: `Bearer ${token}` } },
).then((r) => r.json());

// Muscle distribution (all time)
const muscles = await fetch("http://localhost:3000/api/progress/muscles", {
  headers: { Authorization: `Bearer ${token}` },
}).then((r) => r.json());
```
