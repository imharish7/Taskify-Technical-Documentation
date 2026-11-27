# Taskify — Product Overview

## 1. Product Introduction

**Taskify** is a client-side Task Management and To-Do List web application designed to help individuals organize daily work and track progress across personal and professional categories. Built as a responsive Single-Page Application (SPA) with Progressive Web App (PWA) metadata, Taskify delivers a clean, mobile-first experience that adapts seamlessly to desktop browsers.

---

## 2. Purpose of the Application

The purpose of Taskify is to provide a lightweight, zero-setup productivity tool for managing tasks without requiring user accounts, server infrastructure, or complex configuration. Users can create, organize, update, filter, and complete tasks directly in their browser while keeping all data stored locally on their device.

---

## 3. Problems It Solves

- **Friction in Getting Started**: Eliminates account registration and login barriers so users can begin organizing tasks immediately after a brief onboarding screen.
- **Lack of Visual Progress Clarity**: Replaces plain text checklists with real-time completion percentages—both for daily tasks and across task groups—using circular progress indicators.
- **Disorganized Task Lists**: Groups tasks into clear categories (`Office Project`, `Personal Project`, `Daily Study`, and `General`) and separates work by status (`To-do`, `In Progress`, and `Completed`).
- **Cross-Device Layout Inconsistency**: Provides a tailored navigation model for both mobile screens (bottom navigation bar with a floating action button) and desktop screens (sticky sidebar and multi-column task grid).

---

## 4. Target Users

Taskify is built for individual users who want a straightforward, private, browser-based task manager, including:

- **Professionals and Freelancers** tracking daily office deliverables and ongoing work items.
- **Students and Self-Learners** organizing daily study topics and reading goals.
- **Everyday Users** managing personal projects and general to-do items without needing team collaboration or cloud sync.

---

## 5. Key Features

- **First-Visit Onboarding**: Welcomes new users with an introductory screen before transitioning to the main dashboard.
- **Dashboard Overview**:
  - **Hero Progress Card**: Displays the completion percentage for today's tasks (or all tasks if none are due today) with a quick **View Task** action.
  - **In Progress Strip**: Highlights active `In Progress` tasks for one-click editing.
  - **Task Groups Summary**: Displays task counts and live circular completion rings for each predefined group (`Office Project`, `Personal Project`, `Daily Study`, and `General`).
- **Full Task Management (CRUD)**:
  - Create tasks with a required **Task Title**, optional **Due Date** (with `Today`, `Tomorrow`, and `No Date` quick-select chips), **Task Group**, **Status**, and optional **Description**.
  - Toggle task completion in one click from the task list.
  - Edit any task field or delete tasks permanently.
- **Filtering and Search (`Today's Tasks`)**:
  - Filter by **Due Date** using a horizontal strip (`All Dates` and a 6-day window from `Today - 1` to `Today + 4`).
  - Filter by **Status** (`All`, `To do`, `In Progress`, `Completed`).
  - Filter by **Task Group** when navigating from a Dashboard group card.
  - Search in real time across task titles and descriptions.
- **Overdue Indicators**: Automatically flags incomplete tasks whose due date is earlier than today as `(Overdue)`.
- **Sample Seed Data**: Populates five starter tasks on first launch so users can explore the interface immediately.

---

## 6. Supported Platforms

Taskify runs in modern web browsers and supports:

- **Desktop Browsers**: Responsive multi-column layout with a sticky left navigation sidebar (`>= 768px` viewport).
- **Mobile Browsers**: Touch-friendly layout with a fixed bottom navigation bar, floating `+` action button, and slide-up modal sheet (`< 768px` viewport).
- **Installable Web App (PWA Metadata)**: Includes a web app manifest (`public/manifest.json`) and SVG icon (`public/icon.svg`) configured for standalone display on supporting browsers such as Chrome on Android.

---

## 7. Technology Stack

| Layer | Technology |
| :--- | :--- |
| **UI Framework** | React (`^19.3.1`) and React DOM (`^19.3.1`) |
| **Language** | TypeScript (`~7.0.2`) |
| **Build Tool & Dev Server** | Vite (`^8.4.0`) with `@vitejs/plugin-react` (`^6.3.0`) |
| **Icons** | `lucide-react` (`^1.81.0`) |
| **Styling** | Vanilla CSS with CSS custom properties (`src/index.css`) and Google Fonts (`Lexend Deca`) |
| **Linting** | `oxlint` (`^1.94.0`) |

---

## 8. Data Storage

Taskify is a **100% client-side application** with no backend server, external APIs, or cloud database. All application state is persisted in the browser's `localStorage`:

- **`taskify_tasks_v1`**: Stores the JSON array of task objects (`id`, `title`, `description`, `dueDate`, `status`, `groupId`, `createdAt`, `updatedAt`).
- **`taskify_onboarded_v1`**: Stores `'true'` once the user completes the onboarding screen.

---

## 9. Current Limitations

- **Local-Only Persistence**: Tasks are stored only in the current browser's `localStorage`. Clearing browser storage deletes all data, and tasks do not synchronize across devices or browsers.
- **No User Accounts or Authentication**: There is no login, user profile management, or multi-user support.
- **No Offline Service Worker**: While PWA manifest metadata is present, no Service Worker is registered to cache application assets for full offline loading.
- **Fixed Task Groups**: The four groups (`Office Project`, `Personal Project`, `Daily Study`, `General`) are predefined in code; users cannot create, rename, or delete groups.
- **Fixed Date Strip Range**: The horizontal date filter displays `All Dates` and a fixed 6-day window (`Today - 1` to `Today + 4`); tasks outside that range are accessed via `All Dates`.
- **Static Day Calculation**: Today's date reference (`TODAY_ISO`) is evaluated when the app loads; keeping a browser tab open past midnight requires a page refresh to update date-based calculations.
- **Immediate Deletion**: Deleting a task takes effect immediately without a confirmation dialog or undo option.
- **No Automated Test Suite**: The repository provides build and lint scripts, but no automated unit, integration, or end-to-end tests.
