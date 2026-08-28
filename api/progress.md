# Progress API

Backend endpoint for the simplified Progress & Analytics dashboard (KPI metrics, GitHub-style consistency heatmap, and time-series charts).

## Base URL

```
http://localhost:3000/api/progress
```

All endpoints require authentication (`Bearer <token>`).

---

## Endpoints

### 1. Get Progress Overview

Retrieves all progress metrics, activity heatmap distribution, and time-series data for the selected range and filters in a single request.

**Endpoint:** `GET /overview`

**Authentication:** Required

#### Query Parameters

| Parameter | Type | Required | Default | Description |
| --------- | ---- | -------- | ------- | ----------- |
| `range` | `string` | No | `1M` | Time window: `1W`, `1M`, `3M`, `6M`, `1Y`, `ALL` |
| `metric` | `string` | No | `volume` | Chart metric type: `volume` or `weight` |
| `labels` | `string` | No | `""` | Comma-separated list of session labels to filter volume (e.g. `Chest,Arms`) |

#### Session Labels Filter Options

When filtering by `labels`, you can provide one or more of the following `SessionLabel` enum values:
`Chest`, `Back`, `Shoulders`, `Arms`, `Core`, `Legs`, `Glutes`, `FullBody`, `Cardio`, `Mobility`, `Stretching`.

#### Response

```json
{
  "metrics": {
    "consistency": 85,
    "activeDays": 24,
    "currentStreak": 4,
    "totalVolume": 48250
  },
  "heatmap": [
    {
      "date": "2025-08-28",
      "count": 1,
      "level": 1
    },
    {
      "date": "2025-08-29",
      "count": 0,
      "level": 0
    }
  ],
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
  "selectedLabels": ["Chest", "Arms"]
}
```

#### Response Fields

- **`metrics`**:
  - `consistency`: Percentage of active days against total days in the timeframe.
  - `activeDays`: Total number of days with completed workouts in the timeframe.
  - `currentStreak`: Consecutive days of active training up to today/yesterday.
  - `totalVolume`: Cumulative load lifted (in kg) for matching workouts in the timeframe.
- **`heatmap`**: 365-day array of daily workout frequency (`date`, `count`, `level` from `0` to `4`) designed for activity calendar visualization.
- **`chartData`**: Time-series points for charting:
  - When `metric="volume"`: `{ date, value (volume in kg), sessions }`
  - When `metric="weight"`: `{ date, value (body weight in kg) }`

---

## Example Usage

```javascript
const headers = { Authorization: `Bearer ${token}` };

// Fetch volume progress for Chest and Arms over the last 3 months
const response = await fetch(
  "http://localhost:3000/api/progress/overview?range=3M&metric=volume&labels=Chest,Arms",
  { headers }
).then((r) => r.json());

console.log(response.metrics.consistency); // 85%
console.log(response.chartData);
```
