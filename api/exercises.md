# Exercises API

Browse, search, and manage the exercise library.

## Base URL

```
http://localhost:3000/api/exercises
```

All endpoints require authentication.

---

## Search Exercises

Backend filter endpoint for paginated exercise browsing.

> Note: The web client now performs fuzzy text search locally with Fuse.js using cached exercise data. This endpoint handles structured filters (muscle/category/equipment) and pagination.

**Endpoint:** `GET /search`

**Query Parameters:**

| Param       | Type   | Description              |
| ----------- | ------ | ------------------------ |
| `muscle`    | string | Filter by muscle group   |
| `category`  | string | Filter by category       |
| `equipment` | string | Filter by equipment type |
| `limit`     | number | Max results to return    |
| `offset`    | number | Offset for pagination    |

**Response:** `200 OK`

```json
{
  "data": [
    {
      "id": "exercise-uuid",
      "name": "Barbell Bench Press",
      "category": "Chest",
      "equipment": "Barbell",
      "primaryMuscles": ["Chest"],
      "secondaryMuscles": ["Triceps", "Shoulders"],
      "instructions": "...",
      "isCustom": false
    }
  ],
  "meta": {
    "total": 124,
    "limit": 20,
    "offset": 0
  }
}
```

---

## Exercise Cache Version

Returns the latest update timestamp for exercises visible to the current user (global + their custom exercises).

**Endpoint:** `GET /last-updated`

**Response:** `200 OK`

```json
{
  "data": {
    "lastUpdated": "2026-03-25T15:48:37.114Z"
  }
}
```

---

## Bulk Exercise Payload

Returns the full exercise payload used by the client for local fuzzy search and offline-like instant filtering.

**Endpoint:** `GET /all`

**Response:** `200 OK`

```json
{
  "data": [
    {
      "id": "barbell-bench-press",
      "name": "Barbell Bench Press",
      "category": "strength",
      "equipment": "barbell",
      "isBodyweightExercise": false,
      "primaryMuscles": ["chest"],
      "secondaryMuscles": ["triceps", "shoulders"],
      "instructions": "...",
      "isCustom": false,
      "createdBy": null
    }
  ],
  "meta": {
    "total": 1500
  }
}
```

---

## List Exercises

Legacy page-based listing endpoint. Kept for backward compatibility.

**Endpoint:** `GET /`

**Query Parameters:**

| Param    | Type   | Default | Description            |
| -------- | ------ | ------- | ---------------------- |
| `muscle` | string | —       | Filter by muscle group |
| `page`   | number | `1`     | Page number            |
| `limit`  | number | `20`    | Results per page       |

**Response:** `200 OK`

```json
{
  "data": [...],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 1042,
    "totalPages": 53
  }
}
```

---

## Get Exercises by Muscle

Return all exercises targeting a specific muscle group.

**Endpoint:** `GET /by-muscle/:muscle`

**Response:** `200 OK`

```json
{
  "data": [...],
  "muscle": "Chest",
  "total": 87
}
```

---

## Get Exercises by Category

Return all exercises in a specific category.

**Endpoint:** `GET /by-category/:category`

**Response:** `200 OK`

```json
{
  "data": [...],
  "category": "Chest",
  "total": 42
}
```

---

## Metadata Endpoints

Three endpoints return the available filter values for building UI filter lists.

### Get All Muscle Groups

**Endpoint:** `GET /muscles`

**Response:** `200 OK`

```json
{ "data": ["Chest", "Back", "Shoulders", "Biceps", "Triceps", "Legs", "Core", ...] }
```

### Get All Categories

**Endpoint:** `GET /categories`

**Response:** `200 OK`

```json
{
  "data": [
    "Chest",
    "Back",
    "Shoulders",
    "Arms",
    "Legs",
    "Core",
    "Full Body",
    "Cardio"
  ]
}
```

### Get All Equipment

**Endpoint:** `GET /equipment`

**Response:** `200 OK`

```json
{ "data": ["Barbell", "Dumbbell", "Cable", "Machine", "Bodyweight", ...] }
```

---

## Create Custom Exercise

Add a user-specific exercise to the library.

**Endpoint:** `POST /`

**Headers:**

```
Authorization: Bearer {token}
Content-Type: application/json
```

**Request Body:**

```json
{
  "name": "Paused Bench Press",
  "category": "Chest",
  "equipment": "Barbell",
  "primaryMuscles": ["Chest"],
  "secondaryMuscles": ["Triceps", "Shoulders"],
  "instructions": "Perform a bench press with a 2-second pause at the bottom."
}
```

**Fields:**

- `name` (required): Exercise name
- `category` (required): Category name
- `equipment` (optional): Equipment type
- `primaryMuscles` (optional): Array of primary muscle group names
- `secondaryMuscles` (optional): Array of secondary muscle group names
- `instructions` (optional): How-to description

**Response:** `201 Created` — the created exercise object.

---

## Update Custom Exercise

Update a user-created exercise. You can only edit exercises you created.

**Endpoint:** `PUT /:id`

**Headers:**

```
Authorization: Bearer {token}
Content-Type: application/json
```

**Request Body:** Same fields as create (all optional).

**Response:** `200 OK` — updated exercise object.

**Errors:**

- `403` - Not the owner of this exercise
- `404` - Exercise not found

---

## Delete Custom Exercise

Delete a user-created exercise. You can only delete exercises you created.

**Endpoint:** `DELETE /:id`

**Headers:**

```
Authorization: Bearer {token}
```

**Response:** `200 OK`

```json
{ "message": "Exercise deleted successfully" }
```

**Errors:**

- `403` - Not the owner of this exercise
- `404` - Exercise not found

---

## Example Usage

```javascript
// Filter chest exercises from backend
const results = await fetch(
  "http://localhost:3000/api/exercises/search?muscle=chest&limit=20&offset=0",
  { headers: { Authorization: `Bearer ${token}` } },
).then((r) => r.json());

// Check cache version
const version = await fetch(
  "http://localhost:3000/api/exercises/last-updated",
  { headers: { Authorization: `Bearer ${token}` } },
).then((r) => r.json());

// Fetch full exercise dataset for local Fuse.js search
const all = await fetch("http://localhost:3000/api/exercises/all", {
  headers: { Authorization: `Bearer ${token}` },
}).then((r) => r.json());

// Get all available muscle groups
const { data: muscles } = await fetch(
  "http://localhost:3000/api/exercises/muscles",
  { headers: { Authorization: `Bearer ${token}` } },
).then((r) => r.json());

// Create a custom exercise
const custom = await fetch("http://localhost:3000/api/exercises", {
  method: "POST",
  headers: {
    Authorization: `Bearer ${token}`,
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    name: "My Custom Movement",
    category: "Back",
    primaryMuscles: ["Lats"],
  }),
}).then((r) => r.json());

// Delete it
await fetch(`http://localhost:3000/api/exercises/${custom.id}`, {
  method: "DELETE",
  headers: { Authorization: `Bearer ${token}` },
});
```
