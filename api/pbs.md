# Personal Bests API

Endpoints for reading, syncing, and managing per-exercise personal best records.

Personal bests are stored as one row per `(user, exercise, metric)` and upserted in place whenever a new record is beaten. Each entry links back to the exact session and set where it was achieved.

## Base URL

```
http://localhost:3000/api/pbs
```

All endpoints require authentication.

---

## Metrics

| Metric        | Tracks                                                   | Unit    |
| ------------- | -------------------------------------------------------- | ------- |
| `MaxWeight`   | Heaviest single weight lifted                            | kg      |
| `MaxReps`     | Most reps in a single set                                | reps    |
| `MaxVolume`   | Highest weight × reps in a single set                    | kg·reps |
| `Max1RM`      | Highest estimated 1-rep max (Epley: `w × (1 + reps/30)`) | kg      |
| `MaxDuration` | Longest duration (holds, cardio)                         | seconds |
| `MaxDistance` | Longest distance (cardio)                                | km      |

---

## Get All Personal Bests

Returns all stored personal bests for the authenticated user. Optionally filter by metric and/or exercise.

**Endpoint:** `GET /`

**Query Parameters:**

| Param         | Type   | Required | Description                                       |
| ------------- | ------ | -------- | ------------------------------------------------- |
| `metric`      | string | No       | Filter by a single metric (e.g. `Max1RM`)         |
| `exerciseId`  | string | No       | Filter by a single exercise ID                    |
| `exerciseIds` | string | No       | Comma-separated list of exercise IDs (widget use) |

**Response:** `200 OK`

```json
[
  {
    "id": 1,
    "userId": "user-uuid",
    "exerciseId": "barbell-bench-press",
    "metric": "MaxWeight",
    "value": 130,
    "achievedAt": "2026-02-10T09:14:00.000Z",
    "sessionId": 42,
    "setId": 301,
    "exercise": {
      "id": "barbell-bench-press",
      "name": "Barbell Bench Press",
      "category": "chest"
    },
    "session": {
      "id": 42,
      "sessionName": "Push Day",
      "startTime": "2026-02-10T09:00:00.000Z"
    }
  }
]
```

---

## Get Personal Bests for One Exercise

Returns all metric records for a single exercise.

**Endpoint:** `GET /:exerciseId`

**Response:** `200 OK`

Array of personal best objects (same shape as above), one entry per metric that has been recorded.

---

## Sync All Personal Bests

Scans the user's full training history and upserts the best value for every metric across every exercise they have ever logged. Safe to call multiple times — existing records are only updated if the new value is higher.

Automatically called when a new exercise is added to the tracked list.

**Endpoint:** `POST /sync`

**Request body:** none

**Response:** `200 OK`

```json
{
  "message": "PBs synced",
  "upserted": 18
}
```

`upserted` is the number of metric rows that were created or updated.

---

## Check Session for New PBs

Checks a single completed session against stored personal bests and upserts any metrics that were beaten. Returns only the records that are genuinely new or improved.

Call this immediately after saving a session.

**Endpoint:** `POST /check-session/:sessionId`

**Request body:** none

**Response:** `200 OK`

```json
{
  "newPBs": [
    {
      "exerciseId": "barbell-bench-press",
      "metric": "Max1RM",
      "value": 151.3
    }
  ],
  "count": 1
}
```

`count` is `0` when nothing was beaten. `newPBs` is an empty array in that case.

**Errors:**

- `400` — invalid `sessionId`
- `404` — session not found or does not belong to the user

---

## Delete All PBs for an Exercise

Deletes every metric record for a given exercise. Called automatically when an exercise is removed from the tracked list in Settings.

**Endpoint:** `DELETE /:exerciseId`

**Response:** `200 OK`

```json
{
  "message": "Personal bests deleted",
  "deleted": 4
}
```

`deleted` is the number of metric rows removed.

---

## Delete a Single PB Metric

Removes one specific metric record for an exercise.

**Endpoint:** `DELETE /:exerciseId/:metric`

**Response:** `200 OK`

```json
{
  "message": "Personal best deleted"
}
```

**Errors:**

- `404` — record not found

---

## Example Usage

```javascript
// All PBs for the user
const all = await fetch("http://localhost:3000/api/pbs", {
  headers: { Authorization: `Bearer ${token}` },
}).then((r) => r.json());

// Widget: MaxWeight PBs for a set of tracked exercises
const widget = await fetch(
  "http://localhost:3000/api/pbs?metric=MaxWeight&exerciseIds=barbell-bench-press,back-squat",
  { headers: { Authorization: `Bearer ${token}` } },
).then((r) => r.json());

// All metrics for one exercise
const benchPBs = await fetch(
  "http://localhost:3000/api/pbs/barbell-bench-press",
  { headers: { Authorization: `Bearer ${token}` } },
).then((r) => r.json());

// Sync full history (e.g. after tracking a new exercise)
await fetch("http://localhost:3000/api/pbs/sync", {
  method: "POST",
  headers: { Authorization: `Bearer ${token}` },
});

// Check a session after saving it
const result = await fetch("http://localhost:3000/api/pbs/check-session/42", {
  method: "POST",
  headers: { Authorization: `Bearer ${token}` },
}).then((r) => r.json());

// Remove all PBs when untracking an exercise
await fetch("http://localhost:3000/api/pbs/barbell-bench-press", {
  method: "DELETE",
  headers: { Authorization: `Bearer ${token}` },
});
```
