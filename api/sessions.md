# Sessions API

Create, manage, and track workout sessions.

## Base URL

```
http://localhost:3000/api/sessions
```

All endpoints require authentication.

---

## Create Session

Start and log a new workout session.

**Endpoint:** `POST /sessions`

**Headers:**

```
Authorization: Bearer {token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "sessionName": "Push Day A",
  "routineId": "routine-uuid",
  "startTime": "2026-02-23T09:00:00.000Z",
  "endTime": "2026-02-23T10:15:00.000Z",
  "labels": ["Chest", "Shoulders"],
  "exercises": [
    {
      "exerciseId": "exercise-uuid",
      "sets": [
        { "weight": 100, "reps": 10, "isHardSet": true },
        { "weight": 100, "reps": 9, "isHardSet": true },
        { "weight": 100, "reps": 8, "isHardSet": true }
      ]
    }
  ]
}
```

**Fields:**

- `sessionName` (required): Name for the session
- `routineId` (optional): Link session to a routine template
- `startTime` (optional): ISO timestamp — defaults to now
- `endTime` (optional): ISO timestamp
- `labels` (optional): Array of session labels (e.g. `"Chest"`, `"Back"`, `"Legs"`)
- `exercises` (optional): Array of exercise entries
  - `exerciseId`: ID of the global exercise
  - `sets[].weight`: Weight used (number)
  - `sets[].reps`: Reps completed (number)
  - `sets[].isHardSet` (optional): Whether it is a working set — defaults to `true`

**Response:** `201 Created` — full session object with nested exercises and sets.

---

## List Sessions

Retrieve the current user's workout history.

**Endpoint:** `GET /sessions`

**Headers:**

```
Authorization: Bearer {token}
```

**Query Parameters:**

| Param    | Type   | Default | Description                              |
| -------- | ------ | ------- | ---------------------------------------- |
| `page`   | number | `1`     | Page number                              |
| `limit`  | number | `20`    | Results per page                         |
| `filter` | string | —       | Time filter: `today`, `week`, or `month` |

**Response:** `200 OK`

```json
{
  "data": [
    {
      "id": 1,
      "sessionName": "Push Day A",
      "startTime": "2026-02-23T09:00:00.000Z",
      "endTime": "2026-02-23T10:15:00.000Z",
      "labels": ["Chest"],
      "exercises": [...],
      "stats": {
        "totalVolume": 9250,
        "duration": 75,
        "exerciseCount": 4,
        "totalSets": 12
      }
    }
  ],
  "pagination": {
    "total": 42,
    "page": 1,
    "limit": 20,
    "totalPages": 3
  }
}
```

---

## Get Session Details

Get a single session with all exercises and sets.

**Endpoint:** `GET /sessions/:id`

**Headers:**

```
Authorization: Bearer {token}
```

**Response:** `200 OK` — full session object including nested `exercises[].sets` and `exercises[].exercise` (global exercise metadata).

**Errors:**

- `400` - Invalid session ID
- `404` - Session not found

---

## Update Session

Replace the full state of an existing session (used to sync a workout in progress or edit after the fact).

**Endpoint:** `PUT /sessions/:id`

**Headers:**

```
Authorization: Bearer {token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "sessionName": "Push Day A",
  "endTime": "2026-02-23T10:15:00.000Z",
  "labels": ["Chest", "Triceps"],
  "exercises": [
    {
      "exerciseId": "exercise-uuid",
      "sets": [{ "weight": 105, "reps": 8, "isHardSet": true }]
    }
  ]
}
```

All fields are optional. When `exercises` is provided the existing exercise entries are fully replaced.

**Response:** `200 OK` — updated session object.

**Errors:**

- `404` - Session not found or not owned by user

---

## Delete Session

Permanently remove a session and all its exercise entries and sets.

**Endpoint:** `DELETE /sessions/:id`

**Headers:**

```
Authorization: Bearer {token}
```

**Response:** `200 OK`

```json
{ "message": "Session deleted successfully" }
```

**Errors:**

- `404` - Session not found or not owned by user

---

## Calendar Stats

Get per-day workout counts, total volume, and total duration for use in calendar/heatmap views.

**Endpoint:** `GET /sessions/stats/calendar`

**Headers:**

```
Authorization: Bearer {token}
```

**Query Parameters:**

| Param  | Type   | Default | Description                   |
| ------ | ------ | ------- | ----------------------------- |
| `days` | number | `30`    | How many days back to include |

**Response:** `200 OK`

```json
[
  {
    "date": "2026-02-23",
    "workouts": 1,
    "totalVolume": 9250,
    "duration": 75
  }
]
```

---

## Example Usage

```javascript
// Create a session
const session = await fetch("http://localhost:3000/api/sessions", {
  method: "POST",
  headers: {
    Authorization: `Bearer ${token}`,
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    sessionName: "Leg Day",
    startTime: new Date().toISOString(),
    labels: ["Legs"],
    exercises: [
      {
        exerciseId: "squat-id",
        sets: [
          { weight: 140, reps: 5, isHardSet: true },
          { weight: 140, reps: 5, isHardSet: true },
          { weight: 140, reps: 5, isHardSet: true },
        ],
      },
    ],
  }),
}).then((r) => r.json());

// List recent sessions (last week, page 1)
const { data, pagination } = await fetch(
  "http://localhost:3000/api/sessions?filter=week&page=1&limit=20",
  { headers: { Authorization: `Bearer ${token}` } },
).then((r) => r.json());

// Get session details
const details = await fetch(
  `http://localhost:3000/api/sessions/${session.id}`,
  {
    headers: { Authorization: `Bearer ${token}` },
  },
).then((r) => r.json());

// Update session (sync full state)
await fetch(`http://localhost:3000/api/sessions/${session.id}`, {
  method: "PUT",
  headers: {
    Authorization: `Bearer ${token}`,
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    endTime: new Date().toISOString(),
    exercises: updatedExercises,
  }),
});

// Delete a session
await fetch(`http://localhost:3000/api/sessions/${session.id}`, {
  method: "DELETE",
  headers: { Authorization: `Bearer ${token}` },
});
```
