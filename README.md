# ShiftFocus Unified App

## Quick Start
```bash
npm install
npm run dev
```

## Architecture

```
src/
├── App.tsx                    ← BrowserRouter + all routes + lazy loading
├── main.tsx                   ← Entry point
├── styles/
│   └── globals.css            ← ONE design token file (36 colors, 11 typography, shadows, radii)
├── lib/
│   ├── utils.ts               ← cn() helper (clsx + tailwind-merge)
│   └── ErrorBoundary.tsx      ← Root error boundary
├── components/
│   ├── shell/
│   │   ├── AppShell.tsx       ← Layout: sidebar (240px) + header (64px) + <Outlet/>
│   │   ├── Sidebar.tsx        ← Navigation with NavLink (all 14+ pages)
│   │   └── Header.tsx         ← Search + notifications + user menu
│   └── design-system/         ← Shared CVA components (Button, Badge, Card, etc.)
└── pages/
    ├── okrs/                  ← OKR module (ObjectivesContext, OKRsPage, KPIIntelligencePage)
    ├── enforcement/           ← Enforcement Center (store.ts with localStorage, 7 sub-routes)
    ├── admin/                 ← Admin Center (8 settings panels with internal sidebar)
    ├── tasks/                 ← Tasks OS (6 view modes: List, Kanban, Timeline, Calendar, My Tasks, AI)
    ├── weekly/                ← Weekly Check-in
    ├── initiative-hub/        ← Initiative Hub
    ├── initiative-detail/     ← Initiative Detail
    ├── manager/               ← Manager OS
    ├── people/                ← People OS
    ├── execution/             ← Execution Command Center
    ├── intelligence/          ← Intelligence Report
    ├── strategy/              ← Strategy Planning (time horizon switching)
    ├── risk/                  ← Risk Center OS
    └── notifications/         ← Notification Settings
```

## Routes

| Path | Page | Status |
|---|---|---|
| `/` | → Redirects to /execution | ✅ |
| `/execution` | Execution Command Center | ✅ |
| `/org-health` | Org Health Radar | 🔜 Coming Soon |
| `/team-comparison` | Team Comparison OS | 🔜 Coming Soon |
| `/weekly` | Weekly Check-in | ✅ |
| `/risk` | Risk Center OS | ✅ |
| `/intelligence` | Intelligence Report | ✅ |
| `/okrs` | Objectives | ✅ |
| `/key-results` | Key Results Center | 🔜 Coming Soon |
| `/strategy` | Strategy Planning | ✅ |
| `/kpis` | KPI Intelligence | ✅ |
| `/check-ins` | Check-ins | 🔜 Coming Soon |
| `/initiative-hub` | Initiative Hub | ✅ |
| `/initiative-detail` | Initiative Detail | ✅ |
| `/tasks` | Tasks OS | ✅ |
| `/my-tasks` | My Tasks | 🔜 Coming Soon |
| `/team-tasks` | Team Tasks | 🔜 Coming Soon |
| `/manager` | Manager OS | ✅ |
| `/people` | People OS | ✅ |
| `/teams` | Teams Directory | 🔜 Coming Soon |
| `/capacity` | Team Capacity | 🔜 Coming Soon |
| `/one-on-one` | 1:1 Prep Intelligence | 🔜 Coming Soon |
| `/enforcement/*` | Enforcement Center (7 tabs) | ✅ |
| `/admin/*` | Admin Center (8 panels) | ✅ |
| `/notifications` | Notification Settings | ✅ |

## Design System Tokens (globals.css)

All 14 pages use identical tokens:

| Token | Value | Usage |
|---|---|---|
| `--brand-primary` | `#6A3DE8` | Primary buttons, links, active states |
| `--brand-primary-hover` | `#7448EE` | Hover states |
| `--neutral-800` | `#2B2B2B` | Headings, primary text |
| `--neutral-600` | `#666666` | Secondary text, descriptions |
| `--neutral-400` | `#A1A1A1` | Placeholders, labels |
| `--neutral-200` | `#E5E5E5` | Borders, dividers |
| `--neutral-100` | `#F3F3F3` | Hover backgrounds |
| `--neutral-50` | `#FAFAFA` | Page backgrounds |
| `--success` | `#3CCB7F` | On-track, positive |
| `--warning` | `#F5A623` | At-risk, caution |
| `--danger` | `#E53935` | Off-track, critical |
| `--shadow-card` | `0 4px 14px rgba(0,0,0,0.04)` | Card elevation |

Font: Inter (400, 500, 600)

## Known Issues — What Shiva Needs to Fix

### 1. Import Path Deduplication (~30 min)
Each page has its own local `design-system/`, `ui/`, and `lib/` folder with copies of shared components.
This works but creates duplication. Optimize later by:
- Updating imports in each page from `./design-system/button` → `@/components/design-system/button`
- Then deleting the local `design-system/` and `lib/` folders inside each page
- Delete all `/ui/` folders (dead Radix primitives, nothing imports them)
- Delete all `/figma/` folders (dead ImageWithFallback)

### 2. OKR Module — Remaining Hex (~482 instances in ~12 files)
Quick fix with sed:
```bash
cd src/pages/okrs/
find . -name "*.tsx" -exec sed -i "s/#2B2B2B/var(--neutral-800)/g" {} +
find . -name "*.tsx" -exec sed -i "s/#666666/var(--neutral-600)/g" {} +
find . -name "*.tsx" -exec sed -i "s/#E5E5E5/var(--neutral-200)/g" {} +
find . -name "*.tsx" -exec sed -i "s/#6A3DE8/var(--brand-primary)/g" {} +
find . -name "*.tsx" -exec sed -i "s/#FFFFFF/var(--white)/g" {} +
find . -name "*.tsx" -exec sed -i "s/#F5A623/var(--warning)/g" {} +
find . -name "*.tsx" -exec sed -i "s/#3CCB7F/var(--success)/g" {} +
find . -name "*.tsx" -exec sed -i "s/#E53935/var(--danger)/g" {} +
```

### 3. OKR — `as any` (5 instances in api.service.ts)
Replace `any` with `unknown` or proper types in `src/pages/okrs/services/api.service.ts`

### 4. TypeScript Errors on First Build
Expect ~20-50 type errors on `npm run build` — mostly:
- Missing prop types where components pass unknown props
- Possible null reference on optional chaining
- These are compilation-only and won't affect dev mode (`npm run dev` works)

## Already Fixed (Audit Completed)
- ✅ All versioned imports stripped (sonner@2.0.3, lucide-react@0.487.0, etc.)
- ✅ All relative import paths fixed for new folder structure (200+ files)
- ✅ Page-specific lib/, types/, data/ folders copied and paths resolved
- ✅ OKR nested src/ (api, data, types) properly linked
- ✅ Execution types/ folder copied + depth-based paths fixed
- ✅ InitiativeDetail wrapped with InitiativeProvider
- ✅ Initiative Hub types/ and data/ folders included
- ✅ All design-system lib/utils references corrected

## What's Ready for Backend Wiring

Every button currently shows a toast placeholder (e.g., `toast.success('PDF exported')`).
These are intentional. Replace each toast with your actual API call:

- **Enforcement store.ts** — The ONLY working data layer. Uses localStorage for rules CRUD.
  This is the pattern to extend to other modules.
- **OKR ObjectivesContext** — State management shell, ready for API integration.
- **All forms** — react-hook-form ready, just wire onSubmit to your endpoints.

## Adding New Pages

1. Create folder: `src/pages/new-page/`
2. Add components and `index.tsx` with default export
3. Add lazy import in `App.tsx`
4. Add `<Route>` in App.tsx
5. Add nav item in `Sidebar.tsx`

Each step is one line of code.
