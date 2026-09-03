# Progress API

Backend endpoints for the Progress & Analytics dashboard (KPI metrics, GitHub-style consistency heatmap with year filtering, volume tracking, and time-series charts).

## Base URL

```
http://localhost:3000/api/progress
```

All endpoints require authentication (`Bearer <token>`).

---

## Endpoints

### 1. Get Consistency Heatmap

Fetches workout frequency and activity levels day-by-day for a specific calendar year or the trailing 365 days.

**Endpoint:** `GET /heatmap`

**Authentication:** Required

#### Query Parameters

| Parameter | Type | Required | Default | Description |
| --------- | ---- | -------- | ------- | ----------- |
| `year` | `number` | No | Trailing 365 days | Calendar year to fetch (e.g. `2026`, `2025`) |

#### Response

```json
{
  "year": 2026,
  "startDate": "2026-01-01",
  "endDate": "2026-12-31",
  "totalWorkouts": 112,
  "currentStreak": 4,
  "heatmap": [
    {
      "date": "2026-01-01",
      "count": 1,
      "level": 1
    },
    {
      "date": "2026-01-02",
      "count": 0,
      "level": 0
    }
  ]
}
```

---

### 2. Get Progress Metrics (Cards)

Calculates the 4 primary KPI metrics: consistency rate, active workout days, current active streak, and cumulative volume.

**Endpoint:** `GET /metrics`

**Authentication:** Required

#### Query Parameters

| Parameter | Type | Required | Default | Description |
| --------- | ---- | -------- | ------- | ----------- |
| `range` | `string` | No | `1M` | Time window: `1W`, `1M`, `3M`, `6M`, `1Y`, `ALL` |
| `labels` | `string` | No | `""` | Comma-separated list of session labels |

#### Response

```json
{
  "metrics": {
    "consistency": 85,
    "activeDays": 24,
    "currentStreak": 4,
    "totalVolume": 48250
  },
  "range": "1M",
  "selectedLabels": []
}
```

---

### 3. Get Volume / Weight Tracking

Returns time-series data points for volume progression or body weight tracking across the requested time window and label filters.

**Endpoint:** `GET /volume`

**Authentication:** Required

#### Query Parameters

| Parameter | Type | Required | Default | Description |
| --------- | ---- | -------- | ------- | ----------- |
| `range` | `string` | No | `1M` | Time window: `1W`, `1M`, `3M`, `6M`, `1Y`, `ALL` |
| `metric` | `string` | No | `volume` | Metric type: `volume` or `weight` |
| `labels` | `string` | No | `""` | Comma-separated session labels |

#### Response (Volume)

```json
{
  "chartData": [
    {
      "date": "2026-08-20",
      "value": 4200,
      "sessions": 1
    },
    {
      "date": "2026-08-22",
      "value": 5100,
      "sessions": 1
    }
  ],
  "metric": "volume",
  "range": "1M",
  "selectedLabels": ["Chest"],
  "totalVolume": 9300
}
```

---

## Session Labels Filter Options

When filtering by `labels`, you can provide one or more of the following `SessionLabel` enum values:
`Chest`, `Back`, `Shoulders`, `Arms`, `Core`, `Legs`, `Glutes`, `FullBody`, `Cardio`, `Mobility`, `Stretching`.
