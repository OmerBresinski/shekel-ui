# Design System — Colors, Fonts, Spacing, Dark Mode

## Color System

### Light Mode (`:root`)

All colors use oklch. The palette is warm — hues center around 55-80 for neutrals.

| Variable | Value | Description |
|----------|-------|-------------|
| `--background` | `oklch(0.972 0.008 80)` | `#FBF6F0` warm cream — main page background |
| `--foreground` | `oklch(0.22 0.01 55)` | Warm near-black |
| `--card` | `oklch(1 0 0)` | Pure white — cards, panels, detail areas |
| `--card-foreground` | `oklch(0.22 0.01 55)` | |
| `--popover` | `oklch(1 0 0)` | |
| `--popover-foreground` | `oklch(0.22 0.01 55)` | |
| `--primary` | `oklch(0.42 0.06 170)` | Muted teal |
| `--primary-foreground` | `oklch(0.98 0.005 170)` | |
| `--secondary` | `oklch(0.955 0.006 80)` | Light warm |
| `--secondary-foreground` | `oklch(0.30 0.01 55)` | |
| `--muted` | `oklch(0.955 0.006 80)` | |
| `--muted-foreground` | `oklch(0.55 0.01 60)` | |
| `--accent` | `oklch(0.945 0.008 80)` | |
| `--accent-foreground` | `oklch(0.22 0.01 55)` | |
| `--destructive` | `oklch(0.55 0.22 25)` | |
| `--border` | `oklch(0.928 0.003 90)` | `#E8E7E4` — all borders everywhere |
| `--input` | `oklch(0.928 0.003 90)` | `#E8E7E4` |
| `--ring` | `oklch(0.42 0.06 170)` | |

### Dark Mode (`.dark`)

Warm dark palette — hue 55, NOT zinc/cool gray.

| Variable | Value | Description |
|----------|-------|-------------|
| `--background` | `oklch(0.16 0.008 55)` | |
| `--foreground` | `oklch(0.93 0.006 80)` | |
| `--card` | `oklch(0.19 0.008 55)` | |
| `--card-foreground` | `oklch(0.93 0.006 80)` | |
| `--popover` | `oklch(0.19 0.008 55)` | |
| `--popover-foreground` | `oklch(0.93 0.006 80)` | |
| `--primary` | `oklch(0.55 0.08 170)` | |
| `--primary-foreground` | `oklch(0.16 0.008 55)` | |
| `--secondary` | `oklch(0.22 0.008 55)` | |
| `--secondary-foreground` | `oklch(0.93 0.006 80)` | |
| `--muted` | `oklch(0.22 0.008 55)` | |
| `--muted-foreground` | `oklch(0.62 0.01 60)` | |
| `--accent` | `oklch(0.22 0.008 55)` | |
| `--accent-foreground` | `oklch(0.93 0.006 80)` | |
| `--destructive` | `oklch(0.65 0.20 25)` | |
| `--border` | `oklch(0.26 0.006 55)` | |
| `--input` | `oklch(0.26 0.006 55)` | |
| `--ring` | `oklch(0.55 0.08 170)` | |

### Hardcoded Hex Overrides

These are NOT CSS variables — they're used directly in component classNames for specific surfaces that need to be slightly different from the semantic tokens.

| Hex | Usage | Context |
|-----|-------|---------|
| `#FBF6F0` | `--background` | Main page background (same as CSS var) |
| `#FAF8F7` | `bg-[#FAF8F7]` | Sidebar background, left column background (slightly warmer than `--card`) |
| `#F5F0EA` | `bg-[#F5F0EA]` | Hover state, selected state, active sidebar item (light mode) |
| `#E8E7E4` | `--border` | All borders (same as CSS var) |
| `#1a1918` | `dark:bg-[#1a1918]` | Sidebar/left column dark mode background |
| `#2a2826` | `dark:bg-[#2a2826]` | Selected state dark mode |
| `#252321` | `dark:hover:bg-[#252321]` | Hover state dark mode (darker than selected) |

**Rule**: In dark mode, hover is DARKER than selected. In light mode, hover and selected are the same color.

### Chart Colors

Five colors spread across 270 degrees of the hue wheel. Low chroma (0.04-0.10) keeps them muted.

**Light mode:**

| Token | Value | Description |
|-------|-------|-------------|
| `--chart-1` | `oklch(0.65 0.10 55)` | Warm caramel |
| `--chart-2` | `oklch(0.62 0.09 10)` | Dusty rose |
| `--chart-3` | `oklch(0.66 0.08 155)` | Sage green |
| `--chart-4` | `oklch(0.60 0.06 250)` | Slate blue |
| `--chart-5` | `oklch(0.62 0.07 280)` | Dusty mauve |

**Dark mode:**

| Token | Value | Description |
|-------|-------|-------------|
| `--chart-1` | `oklch(0.62 0.06 55)` | Warm brown |
| `--chart-2` | `oklch(0.60 0.06 10)` | Dusty rose |
| `--chart-3` | `oklch(0.63 0.05 145)` | Sage green |
| `--chart-4` | `oklch(0.56 0.04 250)` | Slate blue |
| `--chart-5` | `oklch(0.60 0.05 280)` | Dusty mauve |

### Status Colors

| Token | Light | Dark | Usage |
|-------|-------|------|-------|
| `--status-idle` | `oklch(0.70 0.01 60)` | `oklch(0.55 0.01 60)` | Idle entities |
| `--status-active` | `oklch(0.55 0.14 155)` | `oklch(0.60 0.14 155)` | Active entities |
| `--status-busy` | `oklch(0.65 0.16 60)` | `oklch(0.65 0.16 60)` | Busy entities |
| `--status-offline` | `oklch(0.60 0.18 25)` | `oklch(0.55 0.18 25)` | Offline entities |

### Sidebar Colors

| Variable | Light | Dark |
|----------|-------|------|
| `--sidebar` | `oklch(1 0 0)` | `oklch(0.17 0.008 55)` |
| `--sidebar-foreground` | `oklch(0.22 0.01 55)` | `oklch(0.93 0.006 80)` |
| `--sidebar-border` | `oklch(0.928 0.003 90)` | `oklch(0.26 0.006 55)` |
| `--sidebar-accent` | `oklch(0.955 0.006 80)` | `oklch(0.22 0.008 55)` |

---

## Typography

### Font Families

```css
--font-heading: 'Newsreader Variable', 'Georgia', serif;
--font-sans: 'Nunito Sans Variable', 'Helvetica Neue', sans-serif;
--font-mono: 'Cascadia Code', 'Menlo', monospace;
```

**Imports required:**
```css
@import "@fontsource-variable/newsreader";
@import "@fontsource-variable/nunito-sans";
@import "@fontsource/cascadia-code/400.css";
```

### Weight Rules

- **Headings (`font-heading`)**: ALWAYS `font-normal` (weight 400). Never `font-semibold` or `font-bold`.
- **Body text (`font-sans`)**: `font-normal` for regular text, `font-medium` for emphasis (e.g. table cells, status labels).
- **Mono (`font-mono`)**: Always weight 400 (only 400 is imported).

### Font Size Reference

| Context | Classes | Size |
|---------|---------|------|
| Detail metric values | `font-heading text-[42px] font-normal leading-tight` | 42px |
| Dashboard info card values | `font-heading text-[28px] font-normal leading-tight` | 28px |
| Page header title | `font-heading text-2xl font-normal tracking-tight` | 24px |
| Guide step headings | `font-heading text-xl font-normal` | 20px |
| Sidebar logo | `font-heading text-base font-normal tracking-tight` | 16px |
| List item primary text | `text-[15px] font-normal` | 15px (Nunito Sans) |
| Body text / table cells | `text-sm` | 14px |
| List item secondary text | `text-[12px] text-muted-foreground` | 12px |
| Section labels | `text-[0.6875rem] font-medium uppercase tracking-widest text-muted-foreground` | 11px |
| Metric card labels / small text | `text-[11px] text-muted-foreground` | 11px |
| List item metadata | `text-[11px] text-muted-foreground/70` | 11px |
| Status text (list items) | `text-[10px] font-medium` | 10px |
| Chart axis labels | `fontSize={11}` (Recharts prop) | 11px |
| Tooltip content | `text-[11px]` / `text-[12px]` | 11-12px |

### Section Label Pattern

Used for section titles within sealed cells (e.g. "CONNECT", "RECENT RECORDS", "COST PER ITEM"):

```
text-[0.6875rem] font-medium uppercase tracking-widest text-muted-foreground
```

This produces an 11px, medium weight, uppercase, widely tracked, muted label.

---

## Spacing

### Padding Patterns

| Context | Padding | Notes |
|---------|---------|-------|
| Detail metric cards | `pb-8 pl-16 pr-8 pt-8` | Larger left padding (64px) for visual weight |
| Dashboard info cards | `px-6 py-4` | Compact, no scroll needed |
| Chart cells | `px-6 py-4` | Compact flex cells |
| Page header | `px-6 py-5` | Consistent across all pages |
| Guide step cells | `px-8 py-8` | Generous for readability |
| Table container | `px-8 pb-8` (body) / `px-8 pt-4` (header) | |
| Table section label | `px-8 pl-16 pt-8` | Matches metric card left padding |
| List items | `px-5` | Horizontal padding only (height is fixed) |

### Gap Patterns

| Context | Gap |
|---------|-----|
| List item text lines | `gap-1.5` |
| Metric card label → value | `gap-2` |
| Dashboard info card label → value | `gap-1` |
| List item metadata dots | `gap-2` |
| List item content → status | `gap-4` |

---

## Borders

**Single color everywhere**: `border-border` which resolves to `#E8E7E4` in light mode.

### Border Patterns

- **Row separator**: `border-b border-border`
- **Column separator**: `border-l border-border`
- **Right edge** (left column): `border-r border-border`
- **No outer border-radius** on content areas — everything is edge-to-edge
- **No double borders** — cells share borders via the grid pattern

---

## Dark Mode

### Implementation

```typescript
// Toggle
document.documentElement.classList.toggle('dark', isDark);
// Persist
localStorage.setItem('theme', isDark ? 'dark' : 'light');
// Read on load
const stored = localStorage.getItem('theme');
```

### Transition Rules

```css
/* Body transitions for theme toggle */
body { @apply transition-colors duration-200; }

/* Interactive elements only — NOT on * */
a, button, [role="button"], input, select, textarea, .transition-colors {
    @apply transition-colors duration-150;
}
```

**CRITICAL**: Do NOT apply `transition-colors` to `*`. It causes ~300ms lag on page navigation because the browser processes transitions on hundreds of elements during mount/unmount.

### Dark Mode Color Mapping

| Surface | Light | Dark |
|---------|-------|------|
| Sidebar / left column bg | `#FAF8F7` | `#1a1918` |
| Hover state | `#F5F0EA` | `#252321` |
| Selected state | `#F5F0EA` | `#2a2826` |
| Active sidebar item | `#F5F0EA` | `#2a2826` |

**Rule**: Hover is darker than selected in dark mode. Hover = `#252321`, Selected = `#2a2826`.
