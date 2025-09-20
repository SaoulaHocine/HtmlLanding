# File Organizer UI — Iteration 14

A three-step user interface to configure how files should be organized into folders. This UI is currently a dry run (no actual copy/move yet) and is designed to run in the browser (Chromium) or inside Electron later for full file-system access.

Important: This is the 14th iteration of the project (your iteration tag). Do not change or bump the iteration number when setting up in Windsurf.

---

## Project identity

- Name: File Organizer UI
- Iteration: 14 (do not bump)
- Purpose: Three-step UI to configure how files should be organized into folders; currently a dry run (no actual copy/move yet)

---

## Core tech stack

- Framework: Next.js App Router (13+; recommended 15)
- Language: TypeScript (5.x)
- UI: React 18, Tailwind CSS (3.x), shadcn/ui (Radix UI under the hood), lucide-react icons
- State: React hooks (useState/useMemo/useEffect); no global state library
- Styling: Tailwind utility classes
- Icons: lucide-react
- Accessibility: semantic markup, labeled inputs, keyboard-friendly components via shadcn/Radix

---

## Runtime and tooling

- Node.js: 18.18+ (or 20 LTS recommended)
- Package manager: pnpm 9+ recommended (npm/yarn also fine)
- Environment variables: none
- Browsers (web mode): Chromium-based (Chrome/Edge) for File System Access API (`showDirectoryPicker`)

---

## Electron (optional; for real file access and packaging)

- Electron: latest stable (e.g., 31.x)
- Builder: electron-builder (e.g., 24.x)
- Dev helpers: concurrently, wait-on
- Security: `contextIsolation: true`, `nodeIntegration: false`, preload bridge exposing a minimal `window.electronAPI`
- Dev approach: Electron loads http://localhost:3000 (Next dev server)
- Prod approach: `next export` → `out/` → Electron loads `out/index.html`
- Windows packaging: target `nsis` (installer); can add portable if desired

---

## Key features in iteration 14

- Step 1 — Folder Selection
  - Operation selector at top: Copy or Move (exclusive)
  - Select multiple source folders
  - Include Subfolders checkbox
  - Folder name prefix (optional)
  - Pick a destination folder
- Step 2 — Grouping Options
  - Modes: category, date, extension, size, alphabetical, keywords
  - Size: Normal vs Custom ranges with validation (no negatives, From < To, no overlaps)
  - Date granularity: day, month, or year
  - Keywords: comma-separated, case-insensitive, unique
- Step 3 — Destination & Conflicts
  - Destination subfolder path (under selected destination)
  - Conflict handling: rename, replace, skip
  - Run Organizer: dry run only; shows a toast (no file I/O yet)
- Summary panel shows: operation, selected folders, include subfolders, prefix, grouping details, destination, conflict mode

---

## Current limitations (explicit)

- No real file copy/move yet (dry run only)
- No persistence (settings reset on reload)
- No preview of resulting folder names
- No “Move” confirmation dialog

---

## High-priority next tasks

- Persist settings to localStorage (operation, prefix, grouping, conflict, etc.)
- Add preview of resulting folder names
- Auto-append underscore during name generation (do not mutate the input)
- If using Electron: wire `organizer:run` IPC to implement actual copy/move with conflict handling and progress; confirm before Move

---

## Typical versions (reference)

- next: ^15.x (App Router compatible; 13+ works)
- react / react-dom: ^18.3.x
- typescript: ^5.x
- tailwindcss: ^3.4.x
- postcss: ^8.4.x
- autoprefixer: ^10.4.x
- lucide-react: ^0.4x.x (current stable)
- shadcn/ui components: local in `components/ui` (Radix UI peer deps included by template)
- Optionally for Electron:
  - electron: ^31.x
  - electron-builder: ^24.x
  - concurrently: ^8.x
  - wait-on: ^7.x

---

## Project structure (key files)

- `app/page.tsx` — mounts the UI
- `components/file-organizer.tsx` — all UI and client-side logic
- `components/ui/*` — shadcn/ui components
- `hooks/use-toast.ts` — notifications
- `lib/utils.ts` — utilities (`cn`)
- `app/globals.css`, `tailwind.config.ts` — styling

---

## How to run (web)

1) Install dependencies
- `pnpm install` (recommended) or `npm install`

2) Start the dev server
- `pnpm dev` or `npm run dev`

3) Open the app
- Visit http://localhost:3000 in a Chromium-based browser (Chrome/Edge)

Notes:
- Uses the File System Access API (`showDirectoryPicker`) for path-only folder selection.
- If the API isn’t supported (e.g., Safari/Firefox), the UI shows a helpful message.
- For production or full file-system access, run inside Electron (optional).

---

## How to run (Electron dev; optional)

- Add scripts using `concurrently` to run Next + Electron, and `wait-on` to wait for port 3000.
- Launch Electron to load http://localhost:3000.
- Security: keep `contextIsolation` on and use a preload script for IPC.

---

## What Windsurf should NOT change

- The iteration tag: keep it as “Iteration 14”.
- The overall 3-step flow and the presence/location of the Copy/Move selector (top of Step 1).
\`\`\`

```md project="file-organizer-ui" file="CONTEXT.md" type="markdown"
# Windsurf Context — File Organizer UI (Iteration 14)

Purpose
- Provide a clear, validated UI for organizing files into folders.
- Iteration 14 emphasizes simplicity in Step 1, an explicit Copy/Move operation selector, and dry-run validation.

What Windsurf is dealing with
- A Next.js (App Router) + TypeScript + Tailwind + shadcn/ui project.
- Single-page UI logic primarily in `components/file-organizer.tsx`.
- Browser flow relies on the File System Access API for folder picking (Chromium).
- No server or environment variables required.

Core user flow
1) Step 1 — Folder Selection
   - Operation: Copy or Move (exclusive radio at the top).
   - Select multiple source folders.
   - Include Subfolders toggle.
   - Folder name prefix (optional).
   - Select a destination folder.

2) Step 2 — Grouping Options
   - Mode: `category | date | extension | size | alphabetical | keywords`
   - Size: Normal vs Custom ranges with validation (no negatives, `From < To`, no overlap).
   - Date granularity: `day | month | year`.
   - Keywords: comma-separated, case-insensitive, unique, capped.

3) Step 3 — Destination & Conflicts
   - Destination subfolder under the selected destination.
   - Conflict mode: `rename | replace | skip`.
   - Run Organizer → dry run (toast confirmation).

State snapshot (kept in `components/file-organizer.tsx`)
- `operation: "copy" | "move"`
- `sources: { id, name, handle, subfolders[] }[]`
- `includeSubfolders: boolean`
- `namePrefix: string`
- `destHandle: any | null` (browser handle or path when in Electron)
- `destFolderName: string`
- `destSubfolder: string`
- `conflictMode: "rename" | "replace" | "skip"`
- `groupingMode: "category" | "date" | "extension" | "size" | "alphabetical" | "keywords"`
- Size grouping:
  - `sizeMode: "normal" | "custom"`
  - `rows: CustomRangeRow[]` (validated with `normalizeAndValidate`)
- `dateGranularity: "day" | "month" | "year"`
- `keywordsText: string` (parsed to unique, lowercase list)

Important helpers
- `normalizeAndValidate(rows)` → `{ normalized, errors }` for size ranges.
- `formatRange(range)` → human-readable size labels.
- `parseKeywords(input)` → normalized keyword list.
- `dateGranularityLabel(g)` → label for summary.
- `rid()` → id generator for UI elements.

Constraints / Assumptions
- No backend yet; `Run Organizer` is a dry run.
- No persistence; settings reset on refresh.
- For browser folder picking, use Chromium. In unsupported browsers, the UI explains the limitation.

Known improvements to implement next
1) Persistence to `localStorage`
   - Persist: operation, namePrefix, includeSubfolders, groupingMode, sizeMode, rows, dateGranularity, keywordsText, destSubfolder, conflictMode.
   - Load on mount; keep in sync on change.

2) Preview of resulting folder names
   - Compute sample target folder names by current `groupingMode` and prefix.
   - Auto-append underscore during generation (not in the input):  
     `effectivePrefix = namePrefix && !namePrefix.endsWith("_") ? namePrefix + "_" : namePrefix`

3) Future: real copy/move (Electron)
   - Add a secure IPC bridge in `preload.js` to expose minimal APIs.
   - Implement handlers in `main.js` for select directory, list subfolders, and `organizer:run`.
   - Respect `conflictMode` (rename/replace/skip), stream progress events.

4) Safety UX
   - When operation is Move, show a confirmation dialog before execution.

Testing checklist (for Windsurf to verify)
- Unsupported browser path shows the correct helper message.
- Size custom ranges reject negatives, catch `From >= To`, and detect overlaps.
- Keywords parsing trims, lowercases, deduplicates, and caps length.
- Date granularity label matches the selected option.
- Summary reflects the Operation and current configuration.

How to run (web)
- `pnpm install`
- `pnpm dev`
- Open http://localhost:3000 in a Chromium browser.

Optional Electron (not required for iteration 14)
- Dev: load http://localhost:3000 in Electron.
- Prod: export to `out/` and load static files in Electron.

What Windsurf should NOT change
- Keep the iteration tag as “Iteration 14”.
- Maintain the 3-step flow and the Copy/Move selector placement at the top of Step 1.
