# Taskify — Technical Application Analysis

This document provides a comprehensive technical analysis of the **Taskify** Task Management & To-Do List application based directly on the source code in `f:\Antigravity\Portfolio\Task Management App`.

---

## 1. Application Features

Based on the implemented components and state hooks, the application provides the following features:

- **First-Visit Onboarding**: Displays a welcome screen on initial visit until dismissed via the **Let's Start** button, persisted in `localStorage`.
- **Task CRUD (Create, Read, Update, Delete)**:
  - **Create**: Add tasks with a required title, optional due date, task group, status (`To-do`, `In Progress`, `Completed`), and optional description.
  - **Read / List**: View tasks on the Dashboard (`In Progress` strip and `Task Groups` summary) and in the `Today's Tasks` list view.
  - **Update**: Edit any task's title, due date, group, status, or description via the modal, or toggle completion status directly via a task's checkbox.
  - **Delete**: Permanently remove a task from the list and `localStorage` via the trash icon button.
- **Progress Tracking**:
  - **Overall Daily Progress Card**: Computes completion percentage for today's tasks (falling back to all tasks if no tasks are due today) and renders an animated SVG circular progress ring (`CircularProgress`).
  - **Per-Group Progress**: Computes task counts and completion percentages for each of the four predefined groups (`Office Project`, `Personal Project`, `Daily Study`, `General`).
- **Filtering & Search (`TasksView`)**:
  - **Date Filter**: Horizontal date strip with `All Dates` plus a 6-day window (`Today - 1` through `Today + 4`).
  - **Status Filter**: Pill tabs for `All`, `To do`, `In Progress`, and `Completed`.
  - **Group Filter**: Clicking any group card on the Dashboard filters `Today's Tasks` to that group, with an inline clear (`X`) button.
  - **Text Search**: Case-insensitive substring search across task `title` and `description`.
- **Overdue Detection**: Tasks with a `dueDate` strictly earlier than `TODAY_ISO` (`YYYY-MM-DD`) and status other than `completed` display an `(Overdue)` label in red (`#e11d48`).
- **Seed Data Initialization**: On first run (when `taskify_tasks_v1` is `null` in `localStorage`), populates 5 sample tasks (`seed-1` through `seed-5`) with dates relative to the current day.
- **Responsive Layout & PWA Metadata**: Adapts between a mobile bottom-navigation layout (`< 768px`) and a desktop sidebar + multi-column grid layout (`>= 768px`), with a web app manifest (`public/manifest.json`) and SVG icon (`public/icon.svg`).

---

## 2. Screens & Views and Their Purpose

The application does not use a URL router; instead, `src/App.tsx` conditionally renders views using React state (`onboarded`, `activeTab`, and `modalOpen`).

| Screen / View | Component File | Purpose |
| :--- | :--- | :--- |
| **Onboarding Screen** | `src/components/OnboardingView.tsx` | Shown when `onboarded` is `false`. Introduces the app with a custom inline SVG illustration, headline, description, and a **Let's Start** button that sets `onboarded = true`. |
| **Dashboard (`Home`)** | `src/components/DashboardView.tsx` | Default view after onboarding (`activeTab === 'home'`). Displays a greeting header, the purple **Hero Progress Card** (overall completion % + **View Task** CTA), an **In Progress** horizontal/grid card strip, and **Task Groups** with completion rings. |
| **Today's Tasks (`Tasks`)** | `src/components/TasksView.tsx` | Active when `activeTab === 'tasks'`. Provides the date strip, status filter pills, search bar, active group filter indicator, and the interactive task card list (complete toggle, edit, delete) or empty state. |
| **Add / Edit Task Modal** | `src/components/TaskModal.tsx` | Rendered as a bottom sheet on mobile (`< 640px`) or centered dialog on larger screens (`>= 640px`) when `modalOpen` is `true`. Handles both creating new tasks (`editingTask === null`) and editing existing tasks. |

---

## 3. User Workflows

1. **First-Time Launch**:
   - `useTasks` initializes `onboarded` from `localStorage` (`taskify_onboarded_v1`) and seeds `tasks` if `taskify_tasks_v1` is `null`.
   - User sees `OnboardingView` and clicks **Let's Start** → `completeOnboarding()` sets `onboarded` to `true` and writes `'true'` to `localStorage`, transitioning to `DashboardView`.
2. **Creating a Task**:
   - User clicks the `+` button (Header quick-add, Mobile FAB, Desktop sidebar **Add New Task**, or Empty State **Add Task**).
   - `TaskModal` opens with empty title/description/dueDate, group defaulted to `office`, and status defaulted to `todo`.
   - User enters a title (required), optionally picks a due date (or clicks `Today`, `Tomorrow`, `No Date` chips), selects group/status/description, and submits → `addTask()` prepends the new task to `tasks` and persists to `localStorage`.
3. **Editing a Task**:
   - User clicks an **In Progress** mini-card on `DashboardView` or the pencil (`Edit`) button on a task card in `TasksView`.
   - `TaskModal` opens pre-populated with that task's fields (`key={editingTask?.id ?? 'new'}`).
   - Submitting calls `updateTask(id, input)` and updates `updatedAt`.
4. **Completing / Reopening a Task**:
   - User clicks the checkbox button on a task card in `TasksView` → `toggleComplete(id)` toggles `status` between `'completed'` and `'todo'`.
5. **Deleting a Task**:
   - User clicks the trash (`Delete`) button on a task card in `TasksView` → `deleteTask(id)` immediately filters the task out of state and updates `localStorage`.
6. **Filtering & Drilling Down**:
   - From `DashboardView`, clicking **View Task** switches to `TasksView` with `selectedGroupFilter = 'all'`.
   - Clicking a **Task Group** row switches to `TasksView` filtered by that `groupId`.
   - Inside `TasksView`, users combine date pill selection, status pill selection, and text search.

---

## 4. Navigation Structure

Navigation is managed in `src/App.tsx` via `activeTab: 'home' | 'tasks'` and `selectedGroupFilter: TaskGroupId | 'all'`, rendered by `src/components/Navigation.tsx`:

- **Mobile (`< 768px`)**: Fixed bottom navigation bar (`.mobile-bottom-nav`) containing:
  - **Home** (`Home` icon) → sets `activeTab = 'home'` and resets `selectedGroupFilter = 'all'`.
  - **Center Floating Action Button (FAB)** (`Plus` icon) → opens `TaskModal` for adding a task.
  - **Tasks** (`CalendarCheck2` icon) → sets `activeTab = 'tasks'`.
- **Desktop (`>= 768px`)**: Sticky left sidebar (`.desktop-sidebar`, `260px` wide) containing:
  - **Brand header**: Taskify logo and live `completedTasks/totalTasks done` counter.
  - **Dashboard** button → `activeTab = 'home'`.
  - **Today's Tasks** button → `activeTab = 'tasks'`.
  - **Add New Task** button → opens `TaskModal`.
- **In-Page Navigation**:
  - `DashboardView` → **View Task** button or **Task Group** cards navigate to `TasksView`.
  - `TasksView` → Top-left **Back** arrow button (`ArrowLeft`) returns to `DashboardView` and clears `selectedGroupFilter`.

---

## 5. Forms and Input Fields

### `TaskModal` Form (`src/components/TaskModal.tsx`)

| Field | HTML Element / ID | Type / Options | Required | Validation & Behavior |
| :--- | :--- | :--- | :--- | :--- |
| **Task Title** | `<input id="task-title">` | `type="text"` | Yes | Trimmed on submit. If empty, sets `error = 'Please enter a task title.'` and blocks submission. Error clears on `onChange`. |
| **Due Date** | `<input id="task-due-date">` | `type="date"` (`YYYY-MM-DD`) | No | Optional. Includes 3 quick-select chips: **Today** (`TODAY_ISO`), **Tomorrow** (`TOMORROW_ISO`), and **No Date** (`''`, stored as `undefined`). |
| **Task Group** | `<select id="task-group">` | `office`, `personal`, `study`, `other` | Yes (defaulted) | Defaults to `'office'` (`Office Project`) when creating a new task. |
| **Status** | `<select id="task-status">` | `todo`, `in_progress`, `completed` | Yes (defaulted) | Defaults to `'todo'` (`To-do`) when creating a new task. |
| **Description** | `<textarea id="task-desc">` | Multiline text (`rows={3}`) | No | Optional. Trimmed; empty strings are normalized to `undefined`. |

### `TasksView` Search Input (`src/components/TasksView.tsx`)

| Field | Element | Type | Behavior |
| :--- | :--- | :--- | :--- |
| **Search Tasks** | `<input type="search" className="search-input">` | `type="search"` | Controlled by `searchQuery` state; filters `tasks` in real time by case-insensitive substring match against `title` and `description`. |

---

## 6. Data Storage and Database Structure

There is **no relational or NoSQL database**. All persistence is handled client-side in browser `localStorage` via `src/hooks/useTasks.ts`.

### `localStorage` Keys

| Key | Type | Purpose |
| :--- | :--- | :--- |
| `taskify_tasks_v1` | JSON-serialized `Task[]` | Stores the array of tasks. If `null` on first load, `createSeedTasks()` seeds 5 initial tasks and writes them immediately. |
| `taskify_onboarded_v1` | String (`'true'`) | Tracks whether the user has completed the onboarding screen. |

### Data Models (`src/types.ts` & `src/data/groups.ts`)

```ts
export type TaskStatus = 'todo' | 'in_progress' | 'completed';
export type TaskGroupId = 'office' | 'personal' | 'study' | 'other';

export interface Task {
  id: string;            // crypto.randomUUID() or fallback `task-<timestamp>-<rand>`
  title: string;         // Required, trimmed
  description?: string;  // Optional, trimmed
  dueDate?: string;      // Optional ISO date string: YYYY-MM-DD
  status: TaskStatus;
  groupId: TaskGroupId;
  createdAt: string;     // ISO 8601 timestamp
  updatedAt: string;     // ISO 8601 timestamp
}
```

**Static Group Definitions (`TASK_GROUPS` in `src/data/groups.ts`)**:
- `office`: "Office Project" (`#F478B8`)
- `personal`: "Personal Project" (`#9260F4`)
- `study`: "Daily Study" (`#FF9142`)
- `other`: "General" (`#38B6FF`)

---

## 7. Backend Architecture

**None.** The application is a purely client-side Single-Page Application (SPA) built with React and Vite. There is no server-side runtime, REST/GraphQL API, or database server.

---

## 8. APIs and Integrations

- **Internal / External APIs**: None. No `fetch`, `axios`, or WebSocket calls exist anywhere in `src/`.
- **Browser Web APIs Used**:
  - `window.localStorage` for task and onboarding persistence.
  - `window.crypto.randomUUID()` (with `Date.now()` + `Math.random()` fallback) for generating task IDs.
  - `Date.prototype.toLocaleDateString` for formatting display dates and weekday labels.
- **External Static Assets**:
  - **Google Fonts** (`index.html`): Loads the `Lexend Deca` font family (`weights 300, 400, 500, 600, 700`) from `fonts.googleapis.com`.
- **PWA Manifest**:
  - `public/manifest.json` declares standalone display mode, theme color `#5F33E1`, background color `#FAF8FF`, and `/icon.svg`.

---

## 9. Authentication and Authorization

**None.** There is no user login, registration, session management, token handling, or role-based access control. The Dashboard header displays a static avatar initial (`T`) and generic `"Hello! / Welcome Back"` greeting.

---

## 10. Dependencies and Technologies Used

From `package.json`, `tsconfig.app.json`, and `vite.config.ts`:

### Production Dependencies
- `react` (`^19.3.1`) & `react-dom` (`^19.3.1`) — UI rendering and state management.
- `lucide-react` (`^1.81.0`) — SVG icon library (`Home`, `CalendarCheck2`, `Plus`, `CheckSquare`, `ArrowLeft`, `ArrowRight`, `Calendar`, `Check`, `Pencil`, `Trash2`, `Search`, `X`, `Briefcase`, `User`, `BookOpen`, `Layers`).

### Development & Build Tooling
- `vite` (`^8.4.0`) & `@vitejs/plugin-react` (`^6.3.0`) — Dev server and production bundler.
- `typescript` (`~7.0.2`) — Static type checking (`strict` bundler settings with `verbatimModuleSyntax: true`, `noUnusedLocals: true`, `noUnusedParameters: true`).
- `oxlint` (`^1.94.0`) — Fast linter (`npm run lint`).
- `@types/node` (`^26.6.6`), `@types/react` (`^19.3.0`), `@types/react-dom` (`^19.3.0`).

### Styling
- Vanilla CSS with CSS custom properties (`src/index.css`)—no CSS framework (such as Tailwind) or CSS-in-JS library is used.

---

## 11. Error Handling

Error handling is minimal and localized:

- **Form Validation (`src/components/TaskModal.tsx`)**: Validates that `title.trim()` is non-empty on submit; displays an inline `.form-error` message (`"Please enter a task title."`) and prevents submission.
- **Storage Reads & Writes (`src/hooks/useTasks.ts`)**:
  - `loadInitialTasks()` wraps `localStorage.getItem` and `JSON.parse` in `try/catch`, returning `[]` on failure and checking `Array.isArray(parsed)`.
  - `loadOnboarded()`, `completeOnboarding()`, and the `tasks` persistence `useEffect` wrap `localStorage` calls in `try/catch` and silently ignore errors (e.g., quota exceeded or disabled storage).
- **Group Lookup Fallbacks (`GroupIcon.tsx`, `DashboardView.tsx`, `TasksView.tsx`)**: Use `GROUP_MAP[task.groupId] ?? GROUP_MAP.other` to avoid runtime crashes if an unrecognized `groupId` is encountered.
- **Gaps**: There is no React Error Boundary, no user-facing notification if `localStorage` fails to persist, and no confirmation dialog or undo mechanism when deleting a task.

---

## 12. Application Limitations

1. **Device-Local Persistence Only**: Data lives solely in the current browser's `localStorage`; clearing browser data erases all tasks, and tasks do not sync across devices or browsers.
2. **No Service Worker / Offline Caching**: Although `public/manifest.json` is present, no Service Worker is registered, so the app is not cached for full offline PWA loading.
3. **Static Date Constants Across Midnight**: `TODAY_ISO`, `TOMORROW_ISO`, and `DATE_STRIP` are computed once at module load time (`DashboardView.tsx`, `TasksView.tsx`, `TaskModal.tsx`). If a tab remains open past midnight without a refresh, "Today" and overdue checks will reference the previous day until reload.
4. **Fixed Date Strip Window**: `TasksView` only shows `All Dates` plus a fixed 6-day window (`Today - 1` to `Today + 4`). Tasks scheduled further in the future or past can only be viewed under `All Dates`.
5. **Hardcoded Task Groups**: The four groups (`Office Project`, `Personal Project`, `Daily Study`, `General`) are statically defined in `src/data/groups.ts`; users cannot create, rename, or delete groups/projects.
6. **No Delete Confirmation or Undo**: Clicking the trash icon immediately deletes the task with no confirmation prompt or undo toast.
7. **No Automated Tests**: `package.json` includes `build` and `lint` scripts, but no unit, integration, or end-to-end test suite is configured.
8. **External Font Dependency**: `Lexend Deca` is loaded from Google Fonts (`index.html`); without internet access, the UI falls back to system sans-serif fonts.
