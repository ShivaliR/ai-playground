# HookHub MVP Specification

## Overview
HookHub is a lightweight web portal that discovers, lists, and displays open‑source Claude hooks hosted on GitHub. The MVP focuses on **displaying** hooks in a responsive grid view. Each hook entry includes a name, category, short description, and a link to the source repository.

## Scope (MVP)
- **Data source**: Public GitHub repositories containing Claude hooks. Hooks are identified via a JSON manifest (`hooks.json`) stored in each repository or by scanning the repo for a known file pattern (`*.hook.ts`).
- **Front‑end**: A single page (`/`) that renders a grid of hook cards.
- **Card content**:
  - Hook name (title case).
  - Category badge (e.g., *productivity*, *devtools*).
  - One‑sentence description (max 150 chars).
  - "View on GitHub" button linking to the hook’s repository.
- **Search / filter**: Basic text search across name and description; optional category filter dropdown.
- **Responsive layout**: 1 column on mobile, 2–4 columns on larger screens.
- **No authentication** – all data is publicly readable.

## Data Model
```ts
interface Hook {
  name: string;        // Human‑readable hook name
  category: string;    // Simple tag (e.g., "productivity")
  description: string; // Short description (≤150 chars)
  repoUrl: string;     // Full GitHub URL to the repo
}
```
The front‑end will consume a JSON endpoint returning `Hook[]`.

## API Endpoints (Server‑side)
| Method | Path          | Description                                 | Response                |
|--------|---------------|---------------------------------------------|-------------------------|
| GET    | `/api/hooks`  | Returns the list of discovered hooks.       | `Hook[]` (JSON array)   |
| GET    | `/api/search` | Optional: query `q` and `category` params for client‑side filtering. | Filtered `Hook[]` |

The implementation may use Next.js API routes (`pages/api/*.ts`). The endpoint will fetch data from a static `data/hooks.json` bundle generated at build time or, for the MVP, a hard‑coded in‑memory array.

## UI Design
- **Header**: Title “HookHub” and a brief subtitle.
- **Search bar** – left‑aligned input with placeholder “Search hooks…”.
- **Category filter** – dropdown next to the search bar.
- **Grid** – CSS Grid/Flexbox; each card has:
  - Bold hook name.
  - Category badge (styled pill).
  - Description text.
  - Primary action button “View on GitHub”.
- **Footer** – small note with a link to the project repository.

## Hook Discovery (Build Process)
1. Define a script (`scripts/fetch-hooks.js`) that reads a list of GitHub repo URLs from a config file (`hook-repos.json`).
2. For each repo, fetch the raw `hooks.json` (or fallback to scanning the repo). Merge results into `public/data/hooks.json`.
3. This script runs locally before `npm run dev` or can be part of a CI step.

## Non‑Functional Requirements
- **Performance**: Initial page load < 1 s on typical broadband.
- **Accessibility**: Semantic HTML, alt text on images (if any), focusable buttons.
- **Responsiveness**: Mobile‑first design.
- **Testing**: Simple unit tests for the API route returning mock data.

## Future Extensions (Post‑MVP)
- Authenticated submission of new hooks.
- Hook rating / stars.
- Auto‑refresh of hook list via GitHub webhook.
- Detailed hook page with README rendering.

---
*This specification provides the minimal functional and architectural description needed to implement the HookHub MVP.*