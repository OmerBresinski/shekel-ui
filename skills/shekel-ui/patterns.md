# Patterns — Layouts, Tables, Cards, Charts, Animations

## Page Layout

### Root Structure

The app fills the viewport using a full-height flexbox:

```
<SidebarProvider defaultOpen={false}>
  <AppSidebar />
  <SidebarInset className="flex h-screen min-h-0 flex-col">
    {/* Page content */}
  </SidebarInset>
</SidebarProvider>
```

- `h-screen` + `min-h-0` on the main container prevents overflow
- Each page fills the remaining space with `flex min-h-0 flex-1 flex-col`

### Two-Column Page (Master-Detail)

```
┌─────────────────────────────────────────────────┐
│ PageHeader (border-b)                            │
├──────────────┬──────────────────────────────────┤
│ List panel   │ Detail panel                      │
│ w-64 lg:w-72 │ min-h-0 flex-1                   │
│ shrink-0     │                                   │
└──────────────┴──────────────────────────────────┘
```

- Left panel: `w-64 shrink-0 lg:w-72` — fixed width, doesn't shrink
- Right panel: `min-h-0 flex-1` — takes remaining space
- The two columns sit in a `flex min-h-0 flex-1` container

### Full-Width Page (e.g. Dashboard, History, Guide)

```
┌─────────────────────────────────────────────────┐
│ PageHeader (border-b)                            │
├─────────────────────────────────────────────────┤
│ Content area (flex-1, may scroll)                │
└─────────────────────────────────────────────────┘
```

---

## Sealed Grid Cells

The core visual pattern. Content areas are divided into bordered cells that tile edge-to-edge with no gaps, no outer padding, no border-radius.

### Rules

1. **No outer padding** on the content area — cells go flush to container edges
2. **No border-radius** on cells — sharp corners everywhere
3. **Row separator**: `border-b border-border` on each row
4. **Column separator**: `border-l border-border` on cells that aren't first in row
5. **Inner padding** varies by context (see Spacing section in design-system.md)

### Example: 4-cell metric row

```tsx
<div className="grid grid-cols-4 border-b border-border">
  {metrics.map((metric, i) => (
    <div className={`flex h-[200px] flex-col justify-center gap-2 pb-8 pl-16 pr-8 pt-8 ${i > 0 ? 'border-l border-border' : ''}`}>
      {/* content */}
    </div>
  ))}
</div>
```

### Example: 2-column split row

```tsx
<div className="grid grid-cols-2 border-b border-border">
  <div className="px-6 py-4">{/* left */}</div>
  <div className="border-l border-border px-6 py-4">{/* right */}</div>
</div>
```

### Example: 2/3 + 1/3 split

```tsx
<div className="grid grid-cols-3 border-b border-border">
  <div className="col-span-2 pb-8 pl-16 pr-8 pt-8">{/* 2/3 */}</div>
  <div className="border-l border-border pb-8 pl-8 pr-8 pt-8">{/* 1/3 */}</div>
</div>
```

---

## Split Table Pattern

Tables use a split approach: fixed header outside the scroll area, scrollable body inside. This ensures the header stays pinned while rows scroll.

**DO NOT** use the shadcn `<Table>` component for scrollable tables. Use raw HTML `<table>` elements instead, because `<Table>` wraps in a container div that breaks the split.

### Structure

```tsx
<div className="flex min-h-0 flex-1 flex-col">
  {/* Section label (fixed) */}
  <div className="shrink-0 px-8 pl-16 pt-8">
    <span className="text-[0.6875rem] font-medium uppercase tracking-widest text-muted-foreground">
      Section Title
    </span>
  </div>

  {/* Table header (fixed) */}
  <div className="shrink-0 border-b border-border px-8 pl-16 pt-4">
    <table className="w-full">
      <thead>
        <tr>
          <th className="w-[48px] pb-3" />
          <th className="pb-3 text-left text-sm font-medium text-muted-foreground">Column</th>
          <th className="w-[120px] pb-3 text-left text-sm font-medium text-muted-foreground">Column</th>
          <th className="w-[120px] pb-3 text-right text-sm font-medium text-muted-foreground">Column</th>
        </tr>
      </thead>
    </table>
  </div>

  {/* Table body (scrollable) */}
  <ScrollArea className="min-h-0 flex-1">
    <div className="px-8 pb-8 pl-16">
      <table className="w-full">
        <tbody>
          <tr className="h-14 border-b border-border last:border-0">
            <td className="w-[48px]">{/* icon/action */}</td>
            <td className="text-sm font-medium">{/* main content */}</td>
            <td className="w-[120px] text-sm">{/* data */}</td>
            <td className="w-[120px] text-right text-sm text-muted-foreground">{/* data */}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </ScrollArea>
</div>
```

### Column Width Reference

| Column Type | Width |
|-------------|-------|
| Icon/action (copy button) | `w-[48px]` |
| Entity name | `w-[140px]` |
| Data column (duration, cost, timestamp) | `w-[120px]` |
| Primary content (title, description) | No fixed width (flex) |

### Row Styling

- Height: `h-14`
- Border: `border-b border-border last:border-0`
- Header text: `text-sm font-medium text-muted-foreground`, `pb-3`
- Body text: `text-sm` (primary cell also `font-medium`)
- Right-aligned columns: `text-right`

---

## Metric Cards

Two variants depending on context.

### Detail Panel Metrics (large)

```tsx
<div className="grid shrink-0 grid-cols-4 border-b border-border">
  <div className="flex h-[200px] flex-col justify-center gap-2 pb-8 pl-16 pr-8 pt-8">
    <span className="text-[11px] text-muted-foreground">Label</span>
    <div className="font-heading text-[42px] font-normal leading-tight">Value</div>
  </div>
</div>
```

| Property | Value |
|----------|-------|
| Height | `h-[200px]` |
| Padding | `pb-8 pl-16 pr-8 pt-8` |
| Label | `text-[11px] text-muted-foreground` |
| Value | `font-heading text-[42px] font-normal leading-tight` |

### Dashboard Info Cards (compact)

```tsx
<div className="grid grid-cols-4 border-b border-border">
  <div className="flex flex-col justify-center gap-1 px-6 py-4">
    <span className="text-[11px] text-muted-foreground">Label</span>
    <div className="font-heading text-[28px] font-normal leading-tight">Value</div>
  </div>
</div>
```

| Property | Value |
|----------|-------|
| Height | Auto (no fixed height) |
| Padding | `px-6 py-4` |
| Label | `text-[11px] text-muted-foreground` |
| Value | `font-heading text-[28px] font-normal leading-tight` |

### AnimateNumber

Numeric values animate using `motion-number`'s `AnimateNumber` component:

```tsx
const springTransition = {
  duration: 0.8,
  type: 'spring' as const,
  bounce: 0.15,
};

<AnimateNumber
  transition={springTransition}
  prefix="$"           // optional
  suffix="%"           // optional
  format={{ minimumFractionDigits: 2, maximumFractionDigits: 2 }}  // optional
>
  {numericValue}
</AnimateNumber>
```

- Currency: `prefix="$"` with 2 decimal places
- Percentage: `suffix="%"`
- Tokens: divide by 1000/1000000 and use `suffix="K"` or `"M"`
- Uptime (string like "2d 4h"): plain text, no animation

---

## Scrollable Areas

Use `ScrollArea` from shadcn for all scrollable content. The pattern:

```tsx
<div className="flex min-h-0 flex-1 flex-col">
  <div className="shrink-0">{/* Fixed content above */}</div>
  <ScrollArea className="min-h-0 flex-1">
    {/* Scrollable content */}
  </ScrollArea>
</div>
```

**Key**: `min-h-0` on both the parent flex container and the `ScrollArea`. Without it, flex items won't shrink below their content height.

---

## Chart Layout

Charts fill remaining viewport space without scrolling. Two rows of two charts each.

```tsx
<div className="flex min-h-0 flex-1 flex-col">
  <div className="grid min-h-0 flex-1 grid-cols-2 border-b border-border">
    <div className="flex min-h-0 flex-col px-6 py-4">{/* chart 1 */}</div>
    <div className="flex min-h-0 flex-col border-l border-border px-6 py-4">{/* chart 2 */}</div>
  </div>
  <div className="grid min-h-0 flex-1 grid-cols-2">
    <div className="flex min-h-0 flex-col px-6 py-4">{/* chart 3 */}</div>
    <div className="flex min-h-0 flex-col border-l border-border px-6 py-4">{/* chart 4 */}</div>
  </div>
</div>
```

Each chart cell:
- Label: `shrink-0 text-[0.6875rem] font-medium uppercase tracking-widest text-muted-foreground`
- Chart container: `mt-2 min-h-0 flex-1` — fills available space
- Charts use `className="h-full w-full"` on `ChartContainer`

### Chart Lazy Loading

Charts are lazy-loaded to keep Recharts out of the main bundle:

```tsx
const LazyChart = lazy(() =>
  import('@/components/dashboard-charts').then(m => ({ default: m.ChartName }))
);

<Suspense fallback={<Skeleton className="h-full w-full rounded-sm" />}>
  <LazyChart data={data} />
</Suspense>
```

### Chart Styling Rules

| Property | Value |
|----------|-------|
| Max bar width | `maxBarSize={40}` |
| Zero-value bars | `minPointSize={2}` |
| Grid lines | `vertical={false}` for vertical bars, `horizontal={false}` for horizontal |
| Axis | `tickLine={false} axisLine={false} fontSize={11}` |
| Bar corners | `radius={[4, 4, 0, 0]}` for top bars, `radius={[0, 4, 4, 0]}` for horizontal |
| Model Y-axis width | `width={140}` with truncation at 16 chars |
| Pie inner/outer radius | `innerRadius="50%" outerRadius="75%"` |
| Pie stroke | `strokeWidth={2} stroke="var(--background)"` |

### Keep-Alive Pattern

The Dashboard page is always mounted to avoid expensive Recharts re-renders:

```tsx
<div className={isDashboard ? 'flex min-h-0 flex-1 flex-col' : 'hidden'}>
  <DashboardPage />
</div>
```

- When navigating away: `display: none` (no unmount)
- When navigating back: `display: flex` (instant, no re-render)
- Other pages use normal React Router mount/unmount

---

## Animations

### Page Header — fadeSlideIn

```css
@keyframes fadeSlideIn {
    from { opacity: 0; transform: translateY(6px); }
    to   { opacity: 1; transform: translateY(0); }
}
```

Applied via Tailwind arbitrary animation syntax:
- Title: `animate-[fadeSlideIn_0.3s_ease-out_both]`
- Description: `animate-[fadeSlideIn_0.3s_ease-out_0.08s_both]` (80ms delay for stagger)

### Number Rolling — AnimateNumber

Spring transition: `{ duration: 0.8, type: 'spring', bounce: 0.15 }`

Used on metric card values. Numbers roll digit-by-digit when the value changes (e.g. when switching between items or when polling data updates).

### Color Transitions

Only on interactive elements:
```css
a, button, [role="button"], input, select, textarea, .transition-colors {
    @apply transition-colors duration-150;
}
body { @apply transition-colors duration-200; }
```

**Never** apply `transition-colors` to `*`.

---

## Master-Detail Page Recipe

A complete recipe for building a new two-column page with a list panel and detail panel.

### 1. Create the route

In `App.tsx`, add two routes:
```tsx
<Route path="/items" element={<ItemsPage />} />
<Route path="/items/:itemId" element={<ItemsPage />} />
```

### 2. Add the sidebar nav item

In `app-sidebar.tsx`, add to `navItems`:
```tsx
{ page: 'items', path: '/items', icon: SomeIcon, label: 'Items' },
```

### 3. Build the page component

```tsx
function ItemsPage() {
  const { itemId } = useParams();
  const navigate = useNavigate();
  const { data: items, isLoading } = useItems();
  const { data: records } = useItemRecords();
  const summary = useSummary(items);

  // Auto-navigate to first item if none selected
  useEffect(() => {
    if (items && items.length > 0 && !itemId) {
      navigate(`/items/${items[0].id}`, { replace: true });
    }
  }, [items, itemId, navigate]);

  const selectedItem = items?.find((i) => i.id === itemId) ?? null;

  return (
    <div className="flex min-h-0 flex-1 flex-col">
      <PageHeader
        key="items"
        title="Items"
        description={`${summary.total} items · ${summary.active} active · ${summary.idle} idle`}
      />
      <div className="flex min-h-0 flex-1">
        {/* List panel */}
        <div className="w-64 shrink-0 lg:w-72">
          {isLoading ? (
            <div className="flex flex-col gap-3 p-4">
              {[...Array(5)].map((_, i) => <Skeleton key={i} className="h-14 w-full" />)}
            </div>
          ) : items ? (
            <ItemList items={items} selectedId={itemId ?? null} />
          ) : null}
        </div>
        {/* Detail panel */}
        <div className="min-h-0 flex-1">
          {selectedItem ? (
            <ItemDetail item={selectedItem} records={records ?? []} />
          ) : (
            <div className="flex h-full items-center justify-center">
              <p className="text-sm text-muted-foreground">Select an item from the list</p>
            </div>
          )}
        </div>
      </div>
    </div>
  );
}
```

### 4. Build the list panel

Follow components.md → List Items for the exact pattern. Key points:
- Container: `h-full flex-col border-r border-border bg-[#FAF8F7] dark:bg-[#1a1918]`
- Each item: `<Link>` with `h-[101px]`, `overflow-hidden`, `px-5`
- Content uses `w-0 min-w-0 flex-1` trick for truncation
- Status text right-aligned with `shrink-0`

### 5. Build the detail panel

- Container: `flex h-full flex-col bg-card`
- Top: Metric cards in `grid shrink-0 grid-cols-4 border-b border-border`
- Bottom: Split table (fixed header + scrollable body)

---

## Proportional Sizing Guide

The relationship between container size, text size, padding, and gap follows consistent ratios across the design system.

### Core Rule

**Labels are ALWAYS `text-[11px] text-muted-foreground`** regardless of container size. The primary value/heading scales with the container. The ratio between value size and container height determines visual weight.

### Sizing by Container Type

| Container | Height | Value Text | Text/Height | Padding | Label→Value Gap |
|-----------|--------|-----------|-------------|---------|----------------|
| Detail metric card | `200px` | `42px` | 21% | `32/64/32/32` (asymmetric) | `gap-2` |
| Dashboard info card | `~70px` (auto) | `28px` | ~40% | `16/24` (symmetric) | `gap-1` |
| List item | `101px` | `15px` (name) | 15% | `0/20` (horizontal only) | `gap-1.5` |
| Guide step cell | `~180px` (auto) | `20px` (heading) | ~11% | `32/32` (symmetric) | `mt-3` |
| Chart cell | `flex` (fills space) | `11px` (label only) | N/A | `16/24` (symmetric) | `mt-2` |

### Padding Rules

1. **Large containers** (200px metric cards): Use asymmetric padding — `pl-16` (64px left) creates visual weight, while `pr-8 pt-8 pb-8` (32px) on other sides provides breathing room.
2. **Medium containers** (dashboard cards, chart cells): Use symmetric padding — `px-6 py-4` (24px/16px).
3. **Generous containers** (guide steps): Use large symmetric padding — `px-8 py-8` (32px) for readability.
4. **List items**: Horizontal-only padding — `px-5` (20px). Vertical space comes from the fixed `h-[101px]` height with `items-center`.

### Gap Rules

1. `gap-1` — Tight: label sits close to value (compact cards)
2. `gap-1.5` — Medium: text lines within list items
3. `gap-2` — Comfortable: label to value in large cards
4. `mt-2` to `mt-4` — Spacious: section label to chart/table/command content

### Text Hierarchy Within a Container

For any container, the text sizes follow this hierarchy (largest to smallest):

1. **Primary value**: `font-heading`, largest text, scales with container
2. **Primary text**: Body font, the main readable content (name, title)
3. **Secondary text**: `text-muted-foreground`, supporting info (subtitle, description)
4. **Metadata**: `text-muted-foreground/70`, smallest, conditional (duration, stats)
5. **Label**: `text-[11px] text-muted-foreground`, always the same regardless of context
6. **Status**: `text-[10px] font-medium`, colored, right-aligned

### When Adding a New Container Type

1. Decide the container height (fixed or flex)
2. Choose the primary value font size based on the height ratio (~20-40% of container height)
3. Label is always `text-[11px]`
4. Choose padding: asymmetric for hero containers, symmetric for everything else
5. Choose gap based on density: `gap-1` (compact) → `gap-2` (spacious)
