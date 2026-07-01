# TaskFlow — Todo Application

A full-stack todo application built with **React** (multi-page frontend) and **Node.js + Express** (REST API backend).

---

## Table of Contents

1. [Features](#features)
2. [Project Structure](#project-structure)
3. [Getting Started](#getting-started)
4. [Backend API Reference](#backend-api-reference)
5. [Frontend Pages](#frontend-pages)
6. [Data Model](#data-model)
7. [Tech Stack](#tech-stack)

---

## Features

### Todo List Page (`/`)

| Feature | Description |
|---|---|
| **Create task** | Open a modal to add a new task with all fields |
| **Edit task** | Click the ✏ icon on any task to edit it inline via modal |
| **Delete task** | Click 🗑 to delete a single task (with confirmation) |
| **Toggle complete** | Click the checkbox on any task to mark it done/undone |
| **Search** | Live search filters tasks by title or description |
| **Filter by status** | Show All / Active / Completed tasks |
| **Filter by priority** | Show All / Low / Medium / High tasks |
| **Sort** | Sort by Newest first, Due date, Priority, or Title A–Z |
| **Stats bar** | Live count of total / active / completed tasks |
| **Clear completed** | Bulk-delete all completed tasks with one click |
| **View detail** | Click a task body to navigate to the single-task detail page |
| **Due date badges** | Tasks show "Today" (yellow) or "Overdue" (red) badges |
| **Priority stripe** | Color-coded left border: red = high, yellow = medium, green = low |
| **Tags** | Tasks display their tags as clickable-style chips |
| **Subtask progress** | Shows `☑ 2/3` subtask completion count on each task card |

### Task Form Modal (used for Create and Edit)

| Field | Details |
|---|---|
| **Title** | Required. Plain text task name |
| **Description** | Optional multi-line detail |
| **Priority** | Low / Medium / High (default: Medium) |
| **Due Date** | Optional date picker |
| **Tags** | Add multiple tags; press Enter or click Add; remove with ✕ |
| **Subtasks** | Add checklist items; toggle and remove within the form |

### Todo Detail Page (`/todo?id=<uuid>`)

| Feature | Description |
|---|---|
| **Full task view** | Title, description, priority pill, status badge |
| **Timestamps** | Created date, last updated (relative), completed at (relative) |
| **Due date** | Full date display with overdue/today highlight |
| **Tags** | All tags displayed as pills |
| **Subtask checklist** | Toggle individual subtasks with a progress bar showing completion % |
| **Complete/Undo button** | Large CTA to toggle completion status |
| **Edit** | Opens the same edit modal |
| **Delete** | Deletes and returns to the list |
| **Back navigation** | ← All tasks link |

---

## Project Structure

```
todo-app/
├── backend/
│   ├── server.js           # Express app entry point
│   ├── package.json
│   ├── data/
│   │   └── todos.json      # Persistent data file (auto-created)
│   └── routes/
│       ├── todos.js        # All CRUD route handlers
│       └── store.js        # File-based read/write helpers
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── App.js          # Route configuration
│   │   ├── index.js        # React entry point
│   │   ├── index.css       # Global CSS variables
│   │   ├── pages/
│   │   │   ├── TodoListPage.js    # Main list page
│   │   │   ├── TodoListPage.css
│   │   │   ├── TodoDetailPage.js  # Single todo page
│   │   │   ├── TodoDetailPage.css
│   │   │   └── NotFoundPage.js    # 404 page
│   │   ├── components/
│   │   │   ├── TodoItem.js        # List item card
│   │   │   ├── TodoItem.css
│   │   │   ├── AddTodoModal.js    # Create/edit modal
│   │   │   └── AddTodoModal.css
│   │   └── utils/
│   │       └── api.js             # All fetch calls to the backend
│   └── package.json
├── docs/
│   └── API.md              # Detailed API documentation
└── README.md               # This file
```

---

## Getting Started

### Prerequisites

- Node.js 16+
- npm 8+

### 1. Start the Backend

```bash
cd backend
npm install
npm start
# API runs at http://localhost:3001
```

### 2. Start the Frontend

```bash
cd frontend
npm install
npm start
# App runs at http://localhost:3000
```

### Environment Variables

The frontend reads `REACT_APP_API_URL` (defaults to `http://localhost:3001/api`):

```bash
# frontend/.env
REACT_APP_API_URL=http://localhost:3001/api
```

---

## Backend API Reference

Base URL: `http://localhost:3001/api`

### Endpoints

| Method | Path | Description |
|---|---|---|
| `GET` | `/todos` | List all todos (supports filters) |
| `GET` | `/todos/:id` | Get a single todo |
| `POST` | `/todos` | Create a new todo |
| `PUT` | `/todos/:id` | Replace a todo entirely |
| `PATCH` | `/todos/:id` | Partially update a todo |
| `DELETE` | `/todos/:id` | Delete a todo |
| `DELETE` | `/todos?clearCompleted=true` | Bulk-delete all completed todos |
| `GET` | `/health` | Health check |

### Query Parameters for `GET /todos`

| Param | Values | Description |
|---|---|---|
| `status` | `active`, `completed` | Filter by completion state |
| `priority` | `low`, `medium`, `high` | Filter by priority |
| `search` | any string | Search title and description |
| `sortBy` | `title`, `priority`, `dueDate` | Sort field |
| `sortOrder` | `asc`, `desc` | Sort direction (default `desc`) |

### `POST /todos` — Request Body

```json
{
  "title": "Buy groceries",
  "description": "Get milk, eggs, and bread",
  "priority": "medium",
  "dueDate": "2026-07-01",
  "tags": ["errands", "home"],
  "subtasks": [
    { "title": "Milk" },
    { "title": "Eggs" },
    { "title": "Bread" }
  ]
}
```

Only `title` is required. All other fields are optional.

See [docs/API.md](docs/API.md) for full request/response examples.

---

## Frontend Pages

### Page 1: `/` — Todo List

Multi-feature list view. Uses `react-router-dom` for navigation. Fetches from the API and re-fetches when filters change.

### Page 2: `/todo?id=<uuid>` — Todo Detail

Reads the `id` query parameter and fetches that single todo. Displays full metadata, a subtask checklist with a progress bar, and all CRUD actions. Redirects to `/404` if no `id` is supplied.

### Page 3: `/404` — Not Found

Shown for any unmatched routes.

---

## Data Model

Each todo stored in `backend/data/todos.json`:

```json
{
  "id": "uuid-v4",
  "title": "string (required)",
  "description": "string",
  "completed": false,
  "priority": "low | medium | high",
  "dueDate": "2026-07-01 | null",
  "tags": ["string"],
  "subtasks": [
    { "id": "uuid", "title": "string", "completed": false }
  ],
  "createdAt": "ISO 8601",
  "updatedAt": "ISO 8601",
  "completedAt": "ISO 8601 | null"
}
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend framework | React 18 |
| Routing | React Router DOM v6 (multi-page, not SPA) |
| Date formatting | date-fns |
| Backend framework | Express.js 4 |
| Unique IDs | uuid v4 |
| CORS | cors middleware |
| Data persistence | JSON file (`data/todos.json`) |
| Styling | Plain CSS with CSS custom properties |
