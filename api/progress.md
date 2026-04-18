# Progress API

Backend endpoints for the V2 Progress dashboard (summary, calendar, workload, strength trend, day drilldown, and balance analytics).

## Base URL

```
http://localhost:3000/api/progress
```

All endpoints require authentication.

## Common Query Parameters

### `range`

| Value | Description    |
| ----- | -------------- |
| `1W`  | Last 7 days    |
| `1M`  | Last 30 days   |
| `3M`  | Last 3 months  |
| `6M`  | Last 6 months  |
| `1Y`  | Last 12 months |
| `ALL` | All time       |

### `mode`

Used by summary and muscle-balance endpoints.

| Value      | Description                     |
| ---------- | ------------------------------- |
| `Strength` | Strength-priority targeting     |
| `Balanced` | Even distribution               |
| `Body`     | Physique/body-composition focus |

## V2 Endpoints

### Summary

**Endpoint:** `GET /summary`

**Query:** `range`, `mode`

Returns adherence, streaks, totals, and trend percentages.

---

### Calendar

**Endpoint:** `GET /calendar`

**Query:** `range`, `metric`

`metric` values: `sessions`, `volume`, `intensity`

Returns day-level calendar entries and streak summary.

---

### Time Series

**Endpoint:** `GET /timeseries`

**Query:** `range`, `granularity`, `compare`

`granularity` values: `day`, `week`, `month`  
`compare` values: `true`, `false`

Returns bucketized workload series for current period and optional previous period.

---

### Muscle Balance

**Endpoint:** `GET /muscle-balance`

**Query:** `range`, `mode`

Returns set distribution by muscle and by grouped movement patterns (`push/pull/legs/core/other`) with target alignment.

---

### PB Timeline

**Endpoint:** `GET /pbs/timeline`

**Query:** `range`, optional `exerciseId`, optional `metric`

Returns PR events timeline and cadence summary.

---

### Strength Trend (e1RM)

**Endpoint:** `GET /strength/1rm`

**Query:** `range`, `exerciseId`

Returns estimated 1RM series and summary (latest/best/change).

---

### Workload

**Endpoint:** `GET /workload`

**Query:** `range`

Returns workload status, ACWR, ramp rate, confidence, recommendation, and supporting series.

---

### Day Detail

**Endpoint:** `GET /day-detail`

**Query:** `date` (format: `YYYY-MM-DD`)

Returns session-level and exercise-level breakdown for the selected day.

## Legacy Endpoints (Compatibility)

These endpoints remain available while older clients migrate:

- `GET /consistency`
- `GET /volume`
- `GET /muscles`

## Example Usage

```javascript
const headers = { Authorization: `Bearer ${token}` };

// V2 summary card data
const summary = await fetch(
  "http://localhost:3000/api/progress/summary?range=1M&mode=Balanced",
  { headers },
).then((r) => r.json());

// Calendar drilldown data
const calendar = await fetch(
  "http://localhost:3000/api/progress/calendar?range=1M&metric=sessions",
  { headers },
).then((r) => r.json());

// Day detail for selected date
const dayDetail = await fetch(
  "http://localhost:3000/api/progress/day-detail?date=2026-04-18",
  { headers },
).then((r) => r.json());
```
