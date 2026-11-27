# Taskify — Installation and Setup Guide

This guide explains how to download, install, run, and build the **Taskify** Task Management & To-Do List application on your local machine.

---

## 1. System Requirements

Taskify is a client-side React and TypeScript web application bundled with Vite. It runs on any modern operating system that supports Node.js and a modern web browser:

- **Operating System**: Windows 10/11, macOS, or Linux
- **Web Browser**: A modern browser with ES2023 and `localStorage` support (such as Google Chrome, Microsoft Edge, Mozilla Firefox, or Safari)
- **Internet Connection**: Required during initial dependency installation (`npm install`) and to load the `Lexend Deca` font from Google Fonts (`index.html`)

---

## 2. Required Software and Tools

Before setting up the project, ensure the following tools are installed:

1. **Node.js and npm**:
   - **Node.js**: Version `20.19+` or `22.12+` (verified in this workspace with Node.js `v26.3.0`)
   - **npm** (included with Node.js): Verified with npm `11.16.0`
2. **Git** *(optional, if cloning from a repository)*: Required only if you download the source code using `git clone`.
3. **Code Editor** *(optional)*: Visual Studio Code or any text editor if you plan to inspect the project files.

### Verify Installed Versions

Open a terminal (PowerShell, Command Prompt, or bash) and run:

```bash
node -v
npm -v
```

**Expected Output (example):**

```text
v26.3.0
11.16.0
```

> **Explanation:** `node -v` prints your installed Node.js version, and `npm -v` prints your installed npm package manager version.

---

## 3. Project Download or Clone Instructions

You can obtain the project source code either by cloning its GitHub repository or by opening a downloaded copy of the project folder.

### Option A: Clone from GitHub

1. Open your terminal and navigate to the folder where you want to store the project.
2. Replace `<your-github-repository-url>` with your actual **GitHub repository URL** (for example, `https://github.com/your-username/task-management-app.git`) and run:

```bash
git clone <your-github-repository-url>
cd <repository-folder-name>
```

> **Explanation:** `git clone` downloads the project files from the GitHub repository URL you provide, and `cd` moves your terminal into the cloned project folder (where `package.json` is located).

### Option B: Use a Downloaded or Existing Project Folder

If you downloaded the project as a ZIP archive (and extracted it) or already have the project folder on your computer, open your terminal and navigate into the project root directory:

```bash
cd path/to/task-management-app
```

*(Replace `path/to/task-management-app` with the folder location on your computer where `package.json` is located.)*

---

## 4. Dependency Installation

Taskify uses npm to manage its runtime dependencies (`react`, `react-dom`, `lucide-react`) and development tools (`vite`, `typescript`, `oxlint`, and `@vitejs/plugin-react`).

From the project root directory, run:

```bash
npm install
```

> **Explanation:** `npm install` reads `package.json` and `package-lock.json` and downloads all required packages into the `node_modules` folder.

**Expected Output:**

```text
added 31 packages, and audited 31 packages in 10s
found 0 vulnerabilities
```

*(Exact package counts and timing may vary slightly depending on your network and npm cache.)*

---

## 5. Development Server Setup

Taskify does not require `.env` files, API keys, or a backend database server. All application state is stored locally in your browser's `localStorage`.

### Optional: Run the Linter

Before starting the server, you can verify code quality using the configured `oxlint` script in `package.json`:

```bash
npm run lint
```

> **Explanation:** `npm run lint` executes `oxlint` across the source files to check for code warnings or errors.

**Expected Output:**

```text
> task-management-app@0.0.0 lint
> oxlint

Found 0 warnings and 0 errors.
```

---

## 6. Running the Application Locally (Development Environment)

The **development environment** runs Vite's local dev server with fast Hot Module Replacement (HMR) so changes appear immediately without a full rebuild.

Start the development server:

```bash
npm run dev
```

> **Explanation:** `npm run dev` runs the `"dev": "vite"` script defined in `package.json`, starting the local Vite development server on port `5173` (or the next available port if `5173` is already in use).

**Expected Terminal Output:**

```text
> task-management-app@0.0.0 dev
> vite

  VITE v8.4.0  ready in 1539 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
```

### Open the Application in Your Browser

1. Open Google Chrome (or another modern browser).
2. Navigate to **`http://localhost:5173/`** (or the exact `Local:` URL printed in your terminal).
3. You should see the **Task Management & To-Do List** onboarding screen.

> **[Screenshot Placeholder: Development Server Running in Terminal and Browser]**  
> *Description: Terminal window showing the Vite dev server ready at `http://localhost:5173/` alongside a browser window displaying the Taskify onboarding screen.*

To stop the development server, press **`Ctrl + C`** in the terminal.

---

## 7. Production Build Instructions (Production Environment)

Unlike the development server, the **production environment** compiles TypeScript and bundles optimized, minified HTML, CSS, and JavaScript assets into the `dist/` folder.

Run the production build command:

```bash
npm run build
```

> **Explanation:** `npm run build` executes `"build": "tsc -b && vite build"` from `package.json`. First, `tsc -b` checks all TypeScript types using `tsconfig.json` and `tsconfig.app.json`. If type checking passes, `vite build` creates the optimized static bundle in `dist/`.

**Expected Terminal Output:**

```text
> task-management-app@0.0.0 build
> tsc -b && vite build

vite v8.4.0 building client environment for production...
transforming...
✓ 2162 modules transformed.
rendering chunks...
computing gzip size...
dist/index.html                   1.23 kB │ gzip:  0.58 kB
dist/assets/index-CkXjVe16.css   14.53 kB │ gzip:  3.47 kB
dist/assets/index-BEk26Exz.js   247.33 kB │ gzip: 76.99 kB

✓ built in 1.23s
```

*(Exact asset hashes and file sizes may vary.)*

---

## 8. Previewing the Production Build

After running `npm run build`, you can test the compiled `dist/` bundle locally before hosting it anywhere.

Run:

```bash
npm run preview
```

> **Explanation:** `npm run preview` executes `"preview": "vite preview"` from `package.json`, starting a local static server that serves the production files from `dist/`.

**Expected Terminal Output:**

```text
> task-management-app@0.0.0 preview
> vite preview

  ➜  Local:   http://localhost:4173/
  ➜  Network: use --host to expose
```

Open the `Local:` URL shown in your terminal (typically `http://localhost:4173/`) to verify the production build in your browser. Press **`Ctrl + C`** to stop the preview server.

> **[Screenshot Placeholder: Production Preview in Browser]**  
> *Description: Browser displaying the Taskify Dashboard served from `http://localhost:4173/` via `vite preview`.*

---

## 9. Common Setup Problems and Solutions

| Problem | Likely Cause | Solution |
| :--- | :--- | :--- |
| **`npm` or `node` is not recognized** | Node.js is not installed or not added to your system `PATH`. | Install Node.js (`20.19+` / `22.12+` or newer), restart your terminal, and verify with `node -v`. |
| **`Port 5173 is in use, trying another one...`** | Another process is already using port `5173`. | This is normal—Vite automatically selects the next free port (such as `5174`). Open the exact `Local:` URL printed in your terminal. |
| **`npm run build` fails with TypeScript errors** | `tsc -b` found type errors or unused variables (`noUnusedLocals` / `verbatimModuleSyntax`). | Check the file and line number printed in the terminal error output and fix any TypeScript issues, or run `npm run lint` to inspect warnings. |
| **`npm run preview` fails or shows a blank page** | `dist/` has not been built yet or is outdated. | Run `npm run build` first, then run `npm run preview`. |
| **Tasks disappear when switching browsers or clearing history** | Tasks are stored in the current browser's `localStorage` (`taskify_tasks_v1`). | Use the same browser profile to retain tasks; clearing site data resets the app to its initial seed tasks. |
| **Command chaining fails with `&&` in Windows PowerShell 5.1** | Older Windows PowerShell versions do not support `&&` as a command separator. | Run commands on separate lines or separate them with `;` instead of `&&` (for example, `npm run build; npm run lint`). |
