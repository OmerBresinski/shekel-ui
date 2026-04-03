# shekel-ui

A design system for building warm, editorial-style web applications with React, Tailwind CSS, and shadcn/ui.

## Install

```bash
npx skills add OmerBresinski/shekel-ui
```

## What's Included

- **Warm editorial aesthetic** — cream (`#FBF6F0`) + white palette, Newsreader serif headings, warm dark mode
- **Hermetically sealed layouts** — bordered cells, no floating cards, edge-to-edge grids
- **Three-font system** — Newsreader Variable (headings), Nunito Sans Variable (body), Cascadia Code (mono)
- **Complete component patterns** — sidebars, master-detail views, metric cards, split tables, charts
- **shadcn/ui bundled** — includes the full shadcn skill for component APIs and composition rules

## Documentation

| Document | Description |
|----------|-------------|
| [SKILL.md](./skills/shekel-ui/SKILL.md) | Entry point and philosophy |
| [workflow.md](./skills/shekel-ui/workflow.md) | Project scaffolding, decision tree, implementation order |
| [design-system.md](./skills/shekel-ui/design-system.md) | Colors, fonts, spacing, borders, dark mode tokens |
| [patterns.md](./skills/shekel-ui/patterns.md) | Page layouts, sealed grids, split tables, charts, animations |
| [components.md](./skills/shekel-ui/components.md) | Sidebar, list items, metric cards, tables, tooltips, toasts |

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | React + Vite |
| Runtime | Bun |
| Styling | Tailwind CSS v4 + shadcn/ui |
| Fonts | Newsreader Variable, Nunito Sans Variable, Cascadia Code |
| Icons | Hugeicons |
| Charts | Recharts (lazy loaded) |
| Data fetching | TanStack React Query |
| Toasts | Sonner |

## License

MIT
