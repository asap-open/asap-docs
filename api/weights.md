# Weight Tracking API

Track and manage body weight logs over time.

## Base URL

```
http://localhost:3000/api/weights
```

All endpoints require authentication.

---

## Log Weight

Record a new body weight measurement.

**Endpoint:** `POST /`

**Headers:**

```
Authorization: Bearer {token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "weightKg": 85.5,
  "recordedAt": "2026-02-23T08:00:00.000Z"
}
```

**Fields:**

- `weightKg` (required): Weight value in kilograms
- `recordedAt` (optional): ISO timestamp of when the measurement was taken — defaults to now

**Response:** `201 Created`

```json
{
  "id": "weight-log-uuid",
  "userId": "user-uuid",
  "weightKg": 85.5,
  "recordedAt": "2026-02-23T08:00:00.000Z"
}
```

**Note:** This also updates `latestWeightKg` in your profile automatically.

**Errors:**

- `400` - `weightKg` is required
- `401` - Unauthorized

---

## Get Weight History

Retrieve weight logs for the current user, ordered by date ascending (suitable for charting).

**Endpoint:** `GET /history`

**Headers:**

```
Authorization: Bearer {token}
```

**Query Parameters:**

| Param   | Type   | Description                   |
| ------- | ------ | ----------------------------- |
| `range` | string | Time range filter (see below) |

**Range values:**

| Value | Description                   |
| ----- | ----------------------------- |
| `1W`  | Last 7 days                   |
| `1M`  | Last 30 days                  |
| `3M`  | Last 3 months                 |
| `6M`  | Last 6 months                 |
| `1Y`  | Last 12 months                |
| `ALL` | All time (up to 5000 entries) |

If `range` is omitted, the last 100 entries are returned.

**Response:** `200 OK`

```json
[
  {
    "id": "weight-log-uuid",
    "userId": "user-uuid",
    "weightKg": 84.2,
    "recordedAt": "2026-02-01T08:00:00.000Z"
  },
  {
    "id": "weight-log-uuid-2",
    "userId": "user-uuid",
    "weightKg": 85.5,
    "recordedAt": "2026-02-23T08:00:00.000Z"
  }
]
```

**Errors:**

- `401` - Unauthorized

---

## Example Usage

### Log a New Weight

```javascript
const weightLog = await fetch("http://localhost:3000/api/weights", {
  method: "POST",
  headers: {
    Authorization: `Bearer ${token}`,
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    weightKg: 85.5,
    recordedAt: new Date().toISOString(),
  }),
}).then((r) => r.json());
```

### Get Weight History for Charting

```javascript
// Last 3 months — good default for a weight trend chart
const history = await fetch(
  "http://localhost:3000/api/weights/history?range=3M",
  { headers: { Authorization: `Bearer ${token}` } },
).then((r) => r.json());

// history is ordered oldest → newest, ready to pass to a chart library
const chartData = history.map((log) => ({
  x: log.recordedAt,
  y: log.weightKg,
}));
```

## Integration with Profile

When you log a weight:

1. A new entry is created in weight_logs table
2. Your profile's `latestWeightKg` is automatically updated
3. The profile page displays this latest weight
4. BMI is recalculated based on the new weight

This means:

- No need to manually update your profile weight
- Always shows your most recent measurement
- Historical data is preserved in weight logs
