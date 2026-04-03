# Workflow — Building with Shekel UI

This document guides an agent through building a new app or adding to an existing one using the Shekel UI design system.

## How to Ask Questions

When this workflow requires asking the user a question, use your **structured question tool** (e.g. the `question` function) with predefined options. This gives users clear choices and gives you structured responses that map to implementation patterns.

- Provide specific options that map to patterns in this skill
- Allow multiple selections where appropriate
- Allow custom/freeform answers for flexibility
- Adapt the question style to the user's level of detail — if they've already described what they want, don't re-ask

## Phase 0: Detect Project State

Before asking any questions, check what already exists:

| Check | How | Result |
|-------|-----|--------|
| Project exists? | `package.json` in root | If no → State 1 (new project) |
| Design system configured? | `src/index.css` contains `--font-heading` | If no → needs CSS setup |
| Routing set up? | `src/App.tsx` with `BrowserRouter` and `<Routes>` | If no → needs routing setup |
| Existing pages? | Check routes in `App.tsx`, files in `src/components/` | List what exists |
| Sidebar configured? | `src/components/app-sidebar.tsx` exists | If no → needs sidebar |
| shadcn initialized? | `components.json` in root | If no → needs shadcn init |

### Workflow States

| State | Condition | Action |
|-------|-----------|--------|
| **1: New Project** | No `package.json` | Full scaffolding → all questions → build everything |
| **2: New Page** | Project exists, user requests a new page/section | Skip scaffolding → ask about new page only → build it |
| **3: Modify Existing** | Project exists, user wants to change existing UI | Skip scaffolding → skip questions → read patterns → modify |
| **4: Add Feature** | Project exists, user wants to add a feature (dark mode, charts, etc.) | Skip scaffolding → targeted questions → implement |

Determine the state from the user's request + the detection results. If unclear, ask.

---

## Quick Checklist (Experienced Agents)

For agents familiar with shekel-ui, here's the short version. Skip items that are already done:

- [ ] Project scaffolded with Vite + shadcn preset
- [ ] Fonts installed (Newsreader, Nunito Sans, Cascadia Code)
- [ ] `index.css` has full oklch theme tokens (see design-system.md)
- [ ] Sidebar configured with icon navigation + dark mode toggle
- [ ] `PageHeader` component created with `fadeSlideIn` animation
- [ ] Routes defined in `App.tsx` with react-router
- [ ] Data hooks set up with React Query (polling, mock mode)
- [ ] Pages use sealed grid layout (border-b, border-l between cells)
- [ ] Tables use split pattern (fixed header, scrollable body)
- [ ] Toasts configured (sonner, bottom-right, auto-width)

---

## Phase 1: Project Scaffolding (State 1 Only)

Skip this phase if the project already exists.

### 1. Initialize Project

Use the bundled shadcn skill (see `./shadcn/SKILL.md`):

```bash
pnpm dlx shadcn@latest init --preset b2Cin5G7c --name my-app --template vite
cd my-app
```

### 2. Switch to Bun

```bash
rm -rf node_modules pnpm-lock.yaml
bun install
```

Update `package.json` scripts to use `bunx --bun vite` instead of `vite`:
```json
{
  "scripts": {
    "dev": "bunx --bun vite",
    "dev:mock": "VITE_MOCK=true bunx --bun vite",
    "build": "tsc -b && bunx --bun vite build",
    "preview": "bunx --bun vite preview"
  }
}
```

### 3. Install Fonts

```bash
bun add @fontsource-variable/newsreader @fontsource-variable/nunito-sans @fontsource/cascadia-code
```

### 4. Install Core Dependencies

```bash
bun add @tanstack/react-query react-router-dom sonner motion-number @hugeicons/react @hugeicons/core-free-icons
```

### 5. Add shadcn Components

```bash
pnpm dlx shadcn@latest add sidebar tooltip scroll-area skeleton badge progress table separator
```

For charts (if needed):
```bash
pnpm dlx shadcn@latest add chart
```

For toasts:
```bash
pnpm dlx shadcn@latest add sonner
```

### 6. Configure CSS

Replace `src/index.css` with the full shekel-ui theme. See `design-system.md` for all oklch token values. The critical parts:

```css
@import "@fontsource-variable/newsreader";
@import "@fontsource-variable/nunito-sans";
@import "@fontsource/cascadia-code/400.css";

@theme inline {
    --font-heading: 'Newsreader Variable', 'Georgia', serif;
    --font-sans: 'Nunito Sans Variable', 'Helvetica Neue', sans-serif;
    --font-mono: 'Cascadia Code', 'Menlo', monospace;
}
```

Plus all color tokens, the `fadeSlideIn` keyframe, and the base layer styles (see design-system.md for exact values).

### 7. Create Folder Structure

```
src/
├── components/
│   ├── ui/              # shadcn components (auto-generated)
│   ├── app-sidebar.tsx  # Icon sidebar with navigation
│   └── page-header.tsx  # Animated page header
├── hooks/               # React Query hooks
├── lib/
│   ├── utils.ts         # cn() helper (from shadcn)
│   ├── format.ts        # formatCost(), formatTokens()
│   └── title-case.ts    # titleCase()
├── types/               # TypeScript interfaces
├── App.tsx              # Router + layout + providers
├── main.tsx             # Entry point
└── index.css            # Theme tokens
```

### 8. Create Base Components

Create these before any pages:

1. **`page-header.tsx`** — See components.md → Page Header
2. **`app-sidebar.tsx`** — See components.md → Sidebar
3. **`lib/format.ts`** — See components.md → Formatting Utilities
4. **`lib/title-case.ts`** — See components.md → Formatting Utilities

### 9. Set Up App Shell

Create `App.tsx` with:
- `QueryClientProvider` wrapping everything
- `TooltipProvider` for sidebar tooltips
- `BrowserRouter` for routing
- `SidebarProvider` + `AppSidebar` + `SidebarInset` layout
- `Toaster` from sonner at `position="bottom-right"`
- Dark mode toggle via `useThemeToggle()` hook with localStorage persistence

---

## Phase 2: Define Structure

### Asking Questions

For each step below, check the **gate condition** first. If the relevant code already exists, skip the question. Only ask about things that need to be created.

### Step 1: Define Pages

**Gate**: Check `src/App.tsx` for existing `<Route>` elements.

If no routes exist, ask:

> **Question**: "What pages does your app need?"
> 
> Options (allow multiple):
> - Dashboard with stats and charts
> - List of items with a detail view
> - Table / history / log view
> - Guide / documentation page
> - Settings / configuration page

If routes already exist, ask only about new pages:

> **Question**: "You already have [existing pages]. What new pages do you want to add?"

**Mapping:**

| Answer | Pattern | Key files | Reference |
|--------|---------|-----------|-----------|
| Dashboard with stats/charts | Info cards + chart grid, keep-alive mount | `dashboard-page.tsx`, `dashboard-charts.tsx` | patterns.md → Chart Layout, Metric Cards |
| List with detail view | Two-column master-detail | `item-list.tsx`, `item-detail.tsx` | patterns.md → Master-Detail Recipe |
| Table/history/log | Full-width split table | `history-page.tsx` | patterns.md → Split Table Pattern |
| Guide/documentation | Step grid with command blocks | `guide-page.tsx` | components.md → Guide Page |
| Settings/configuration | Form layout | `settings-page.tsx` | shadcn/rules/forms.md |

For each page, also:
1. Add a route in `App.tsx`
2. Add a sidebar nav item with an appropriate `@hugeicons/core-free-icons` icon
3. Create a `PageHeader` with title and description

### Step 2: Define Entities

**Gate**: Check `src/types/` for existing type definitions.

Ask:

> **Question**: "What are the main things users will see and interact with? For example: projects, models, tasks, servers, documents."

For each entity, create:
1. **Type definition** in `src/types/` with status enum, properties, and related records
2. **List item component** following components.md → List Items pattern
3. **Detail panel** following patterns.md → Metric Cards
4. **Data hook** with React Query (`useEntities()`, `useEntityHistory()`)
5. **Route**: `/entities` and `/entities/:entityId`
6. **Mock data** for development (`VITE_MOCK=true` support)

### Step 3: Define Metrics

**Gate**: Check existing detail panel components for metric card grids.

Ask:

> **Question**: "What key numbers should be displayed for each [entity]? For example: cost, duration, usage percentage, count, uptime."

**Mapping:**

| Count | Pattern | Reference |
|-------|---------|-----------|
| 1-4 metrics | Single row `grid-cols-N` | patterns.md → Metric Cards (large variant) |
| 5-8 metrics | Two rows of cards | Same pattern, two `grid` rows |
| Dashboard summary | Compact info cards | patterns.md → Metric Cards (compact variant) |

For numeric values that update via polling, use `AnimateNumber` with the spring transition config.

### Step 4: Define States

**Gate**: Check `src/types/` for existing status enums.

Ask:

> **Question**: "Does each [entity] have a status or state? If so, what are the possible states?"

If yes, create:
1. A `type EntityStatus = 'state1' | 'state2' | ...` union
2. A `statusColor: Record<EntityStatus, string>` mapping (see components.md → Status Color Mapping)
3. A `statusLabel: Record<EntityStatus, string>` mapping for display text

Choose the display variant:
- **Text only** (default for list items): `text-[10px] font-medium` + color class
- **Badge**: for table cells or prominent placement
- **Dot**: `size-1.5 rounded-full bg-status-*` for compact indicators in summaries

### Step 5: Define Charts

**Gate**: Check `package.json` for `recharts` dependency.

Ask:

> **Question**: "Do you need any charts or visualizations? If so, what kind?"
>
> Options (allow multiple):
> - Vertical bar chart (e.g. cost per item)
> - Stacked bar chart (e.g. breakdown by category)
> - Horizontal bar chart (e.g. usage by model/type)
> - Pie / donut chart (e.g. status distribution)
> - None needed

If charts are needed:
1. Install: `pnpm dlx shadcn@latest add chart`
2. Create a `dashboard-charts.tsx` with `memo()` wrapped chart components
3. Lazy-load charts via `React.lazy()` + `Suspense`
4. Use the keep-alive pattern for the dashboard page
5. Follow chart styling rules in patterns.md → Chart Layout

### Step 6: Define Actions

**Gate**: Check existing components for interaction patterns.

Ask:

> **Question**: "What actions can users take? For example: copy a command, navigate to details, create new items, export data."

**Mapping:**

| Action | Pattern | Reference |
|--------|---------|-----------|
| Copy to clipboard | Copy button + sonner toast | components.md → Code Blocks & Commands |
| Navigate to detail | `<Link>` in list items | components.md → List Items |
| Resume/continue | Table row copy button | components.md → Copy Session Button |
| Create/edit | Form page | shadcn/rules/forms.md |

---

## Phase 3: Implementation Order

Build in this sequence to avoid rework:

1. **Types** — Define all interfaces and enums first
2. **Utilities** — `format.ts`, `title-case.ts`
3. **Hooks** — Data fetching with React Query + mock data
4. **Layout** — `App.tsx` with router, sidebar, providers
5. **Page headers** — `PageHeader` component
6. **List panels** — Left column list components
7. **Detail panels** — Right column with metric cards + tables
8. **Tables** — Split header/body pattern
9. **Dashboard** — Info cards + lazy-loaded charts (if needed)
10. **Guide page** — Step grid with command blocks (if needed)

### For each component, follow this process:

1. Read the relevant section in `components.md` for exact classes and structure
2. Check `design-system.md` for color tokens and font sizes
3. Check `patterns.md` for layout patterns
4. Build the component
5. Test in both light and dark mode

---

## Phase 4: Polish

After all pages are functional, add the finishing touches:

### Dark Mode
- Verify all hardcoded hex colors have `dark:` variants (see design-system.md → Hardcoded Hex Overrides)
- Test every page in dark mode
- Ensure transitions are only on interactive elements, NOT on `*`

### Loading States
- List panels: array of `<Skeleton className="h-14 w-full" />` (match the number of expected items)
- Charts: `<Skeleton className="h-full w-full rounded-sm" />`
- Tables: skeleton rows matching the table structure

### Empty States
- Center the message vertically: `flex h-full items-center justify-center`
- Text: `text-sm text-muted-foreground`
- Examples: "No items found", "No records yet", "Select an item from the list"

### Animations
- Page headers already animate via `fadeSlideIn` (built into `PageHeader`)
- Numeric values animate via `AnimateNumber` (add to metric cards)
- Color transitions on interactive elements (already in base CSS)

### Toasts
- Configure sonner: `position="bottom-right"`, auto-width
- Copy pattern: "Copied" + `<code>` pill with the copied text
- See components.md → Toasts for exact implementation
