# API Documentation

Base URL: `http://localhost:3001/api`

All request and response bodies use JSON. The server returns `Content-Type: application/json` on all routes.

---

## Health Check

### `GET /health`

Returns server status.

**Response `200`**
```json
{ "status": "ok", "timestamp": "2026-06-27T10:00:00.000Z" }
```

---

## Todos

### `GET /todos`

Retrieve all todos. Supports filtering, searching, and sorting via query parameters.

**Query parameters**

| Parameter | Type | Description |
|---|---|---|
| `status` | `active` \| `completed` | Filter by completion state |
| `priority` | `low` \| `medium` \| `high` | Filter by priority level |
| `search` | string | Case-insensitive search in `title` and `description` |
| `sortBy` | `title` \| `priority` \| `dueDate` | Field to sort by |
| `sortOrder` | `asc` \| `desc` | Sort direction. Default is `desc` |

When no `sortBy` is provided, results are sorted by `createdAt` descending (newest first).

**Example request**
```
GET /api/todos?status=active&priority=high&sortBy=dueDate&sortOrder=asc
```

**Response `200`** — Array of todo objects
```json
[
  {
    "id": "a1b2c3d4-...",
    "title": "Submit report",
    "description": "End of quarter report for finance",
    "completed": false,
    "priority": "high",
    "dueDate": "2026-06-30",
    "tags": ["work", "finance"],
    "subtasks": [
      { "id": "x1y2z3...", "title": "Gather data", "completed": true },
      { "id": "x4y5z6...", "title": "Write summary", "completed": false }
    ],
    "createdAt": "2026-06-01T08:00:00.000Z",
    "updatedAt": "2026-06-27T09:15:00.000Z",
    "completedAt": null
  }
]
```

---

### `GET /todos/:id`

Retrieve a single todo by its UUID.

**Response `200`** — Single todo object (same shape as above)

**Response `404`**
```json
{ "error": "Todo not found" }
```

---

### `POST /todos`

Create a new todo.

**Request body**

| Field | Type | Required | Default |
|---|---|---|---|
| `title` | string | ✅ | — |
| `description` | string | | `""` |
| `priority` | `low` \| `medium` \| `high` | | `"medium"` |
| `dueDate` | `YYYY-MM-DD` string | | `null` |
| `tags` | string[] | | `[]` |
| `subtasks` | `{ title: string }[]` | | `[]` |

**Example**
```json
{
  "title": "Plan team offsite",
  "description": "Q3 planning session for the team",
  "priority": "high",
  "dueDate": "2026-07-15",
  "tags": ["work", "planning"],
  "subtasks": [
    { "title": "Book venue" },
    { "title": "Send invites" },
    { "title": "Prepare agenda" }
  ]
}
```

**Response `201`** — Created todo object with generated `id`, `createdAt`, `updatedAt`, `completedAt`

**Response `400`**
```json
{ "error": "Title is required" }
```

---

### `PUT /todos/:id`

Full replacement of a todo's fields. All fields are optional but provided ones fully overwrite the existing values.

**Request body** — Same fields as POST

**Response `200`** — Updated todo object

**Response `404`**
```json
{ "error": "Todo not found" }
```

---

### `PATCH /todos/:id`

Partial update. Only the provided fields are changed. Use this to toggle completion, update a single field, or update subtask completion states.

**Common use cases**

Toggle completion:
```json
{ "completed": true }
```

Update subtask list:
```json
{
  "subtasks": [
    { "id": "x1y2...", "title": "Book venue", "completed": true },
    { "id": "x3y4...", "title": "Send invites", "completed": false }
  ]
}
```

**Behaviour for `completed`:** When set to `true`, `completedAt` is automatically set to the current timestamp. When set to `false`, `completedAt` is cleared to `null`.

**Response `200`** — Updated todo object

**Response `404`**
```json
{ "error": "Todo not found" }
```

---

### `DELETE /todos/:id`

Delete a single todo permanently.

**Response `200`**
```json
{ "message": "Todo deleted successfully" }
```

**Response `404`**
```json
{ "error": "Todo not found" }
```

---

### `DELETE /todos?clearCompleted=true`

Bulk-delete all todos where `completed` is `true`.

**Response `200`**
```json
{ "message": "Deleted 5 completed todos" }
```

**Response `400`** (if query param missing)
```json
{ "error": "Use ?clearCompleted=true to bulk delete" }
```

---

## Error Responses

All errors follow the same shape:

```json
{ "error": "Human-readable error message" }
```

| Status | Meaning |
|---|---|
| `400` | Bad request — invalid input |
| `404` | Resource not found |
| `500` | Internal server error |
