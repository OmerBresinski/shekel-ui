# Components — Implementation Details

## Page Header

Every page starts with a `PageHeader` component.

```tsx
<PageHeader title="Page Title" description="Subtitle text" />
```

### Structure

```tsx
<div className="border-b border-border bg-card px-6 py-5">
  <h1 className="animate-[fadeSlideIn_0.3s_ease-out_both] font-heading text-2xl font-normal tracking-tight">
    {title}
  </h1>
  <p className="mt-0.5 animate-[fadeSlideIn_0.3s_ease-out_0.08s_both] text-sm text-muted-foreground">
    {description}
  </p>
</div>
```

- Title and description animate in with a 6px slide + fade, staggered by 80ms
- Background: `bg-card` (white in light, dark card in dark)
- Border: `border-b border-border`
- Used on all pages for visual consistency

---

## Sidebar

Icon-only collapsible sidebar using shadcn's `Sidebar` component.

### Container

```tsx
<Sidebar collapsible="icon" className="border-r border-border bg-[#FAF8F7] dark:bg-[#1a1918]">
```

**CRITICAL — Light/Dark Mode Colors:**

The sidebar uses hardcoded hex colors that MUST include both light AND dark variants:
- Light mode: `bg-[#FAF8F7]` (warm off-white)
- Dark mode: `dark:bg-[#1a1918]` (warm dark)

If you only specify one, the sidebar will appear in the wrong mode. **Always include both**:
```tsx
// CORRECT
className="bg-[#FAF8F7] dark:bg-[#1a1918]"

// WRONG — sidebar will be dark even in light mode
className="bg-[#1a1918]"

// WRONG — sidebar will be light even in dark mode  
className="bg-[#FAF8F7]"
```

### Icon Centering Fix (CRITICAL)

When using `collapsible="icon"`, the default shadcn sidebar has icons that appear left-aligned instead of centered. **Two fixes are required in `src/components/ui/sidebar.tsx`:**

#### 1. Update `sidebarMenuButtonVariants` (around line 473)

Change the collapsed state styles from:
```
group-data-[collapsible=icon]:p-2!
```

To:
```
group-data-[collapsible=icon]:p-0! group-data-[collapsible=icon]:justify-center [&>span:last-child]:group-data-[collapsible=icon]:hidden
```

What this does:
- `p-0!` — Removes padding so the icon can be truly centered in the 32px box
- `justify-center` — Centers the flex content (the icon) horizontally
- `[&>span:last-child]:group-data-[collapsible=icon]:hidden` — Hides the label text when collapsed (instead of just truncating it), so only the icon remains

#### 2. Update `SidebarMenuItem` function (around line 462)

Change:
```tsx
className={cn("group/menu-item relative", className)}
```

To:
```tsx
className={cn("group/menu-item relative flex justify-center", className)}
```

What this does:
- Makes the menu item a flex container that centers its child (the button) horizontally within the sidebar width

### Logo

```tsx
<SidebarHeader className="flex items-center justify-center py-4">
  <Link to="/dashboard" className="font-heading text-base font-normal tracking-tight">oc</Link>
</SidebarHeader>
```

### Navigation Items

Each item is wrapped in `Tooltip` > `TooltipTrigger` > `SidebarMenuButton`:

```tsx
<SidebarMenuButton
  asChild
  isActive={currentPage === item.page}
  className={cn(
    currentPage === item.page && 'bg-[#F5F0EA] text-foreground dark:bg-[#2a2826]',
  )}
>
  <Link to={item.path}>
    <HugeiconsIcon icon={item.icon} />
    <span>{item.label}</span>
  </Link>
</SidebarMenuButton>
```

**Icons used** (from `@hugeicons/core-free-icons`):

| Page | Icon |
|------|------|
| Dashboard | `Analytics02Icon` |
| Agents | `AiBrain01Icon` |
| History | `Clock01Icon` |
| Guide | `BookOpen01Icon` |
| Light mode toggle | `Sun03Icon` |
| Dark mode toggle | `Moon02Icon` |

### Theme Toggle (Footer)

```tsx
<SidebarFooter>
  <SidebarMenuButton onClick={onToggleTheme}>
    <HugeiconsIcon icon={isDark ? Sun03Icon : Moon02Icon} />
    <span>{isDark ? 'Light mode' : 'Dark mode'}</span>
  </SidebarMenuButton>
</SidebarFooter>
```

---

## List Items (Column Items)

Used in the left column of two-column pages (e.g. item list, project list). This is a core reusable pattern.

### Container

```tsx
<div className="flex h-full flex-col border-r border-border bg-[#FAF8F7] dark:bg-[#1a1918]">
  <ScrollArea className="flex-1">
    <div className="flex flex-col">
      {items.map(item => (
        <Link ... />
      ))}
    </div>
  </ScrollArea>
</div>
```

**CRITICAL**: The container MUST have `h-full` to fill the parent. The parent (in the page layout) must have `overflow-hidden` to prevent the list from pushing the page height:

```tsx
{/* In the page layout */}
<div className="flex min-h-0 flex-1 overflow-hidden">
  {/* List panel - with h-full to fill the flex container */}
  <div className="w-64 shrink-0 lg:w-72">
    <ItemList items={items} selectedId={selectedId} />
  </div>
  {/* Detail panel */}
  <div className="min-h-0 flex-1 overflow-hidden">
    <ItemDetail ... />
  </div>
</div>
```

**COMMON MISTAKE — List Overflows Page:**

If your list overflows the entire page (adding a scrollbar to the whole window instead of just the list), you're missing one of these:

1. `overflow-hidden` on the flex container wrapping list + detail
2. `min-h-0` on the flex container (allows flex children to shrink)
3. `h-full` on the list container itself
4. `flex-1` on the ScrollArea inside the list

The full chain must be unbroken:
```
Page (flex flex-col h-screen)
  └─ PageHeader (shrink-0)
  └─ Content area (flex min-h-0 flex-1 overflow-hidden)  ← MUST have overflow-hidden
       └─ List panel (w-64 shrink-0)
            └─ List container (flex h-full flex-col)  ← MUST have h-full
                 └─ ScrollArea (flex-1)  ← handles scrolling
       └─ Detail panel (min-h-0 flex-1 overflow-hidden)
```

### No Extra Headers in List Panel

The list panel should contain ONLY the scrollable list items — **no section headers, no filter labels, no "MONTHLY BILLS" type headers**. All page context goes in the PageHeader at the top.

```tsx
{/* WRONG — extra headers inside list panel */}
<div className="flex h-full flex-col">
  <div className="px-4 py-2 text-xs uppercase">Filter Stack</div>  {/* NO */}
  <div className="px-4 py-2 text-xs uppercase">Monthly Bills</div>  {/* NO */}
  <ScrollArea>...</ScrollArea>
</div>

{/* CORRECT — just the scrollable list */}
<div className="flex h-full flex-col border-r border-border bg-[#FAF8F7] dark:bg-[#1a1918]">
  <ScrollArea className="flex-1">
    <div className="flex flex-col">
      {items.map(item => <Link ... />)}
    </div>
  </ScrollArea>
</div>
```

- Background: `bg-[#FAF8F7] dark:bg-[#1a1918]` (slightly warmer than `bg-card`)
- Border: `border-r border-border` separating from the right column
- No separators between items
- No padding on the outer `flex flex-col` wrapper
- **No section headers or labels inside the list**

### Individual Item

```tsx
<Link
  to={`/path/${item.id}`}
  className={cn(
    'flex h-[101px] w-full cursor-pointer items-center gap-4 overflow-hidden px-5 text-left transition-colors',
    'hover:bg-[#F5F0EA] dark:hover:bg-[#252321]',
    selectedId === item.id && 'bg-[#F5F0EA] dark:bg-[#2a2826]',
  )}
>
```

| Property | Value | Notes |
|----------|-------|-------|
| Element | `<Link>` | Client-side navigation via react-router |
| Height | `h-[101px]` | Fixed height for all items |
| Width | `w-full` | Full width of container |
| Padding | `px-5` | Horizontal only (height is fixed) |
| Gap | `gap-4` | Between content and status text |
| Overflow | `overflow-hidden` | Required for truncation to work |
| Cursor | `cursor-pointer` | |
| Transition | `transition-colors` | Smooth hover/selected changes |

### Hover & Selected States

| State | Light | Dark |
|-------|-------|------|
| Default | transparent | transparent |
| Hover | `bg-[#F5F0EA]` | `bg-[#252321]` |
| Selected | `bg-[#F5F0EA]` | `bg-[#2a2826]` |

**Dark mode rule**: Hover (`#252321`) is DARKER than selected (`#2a2826`). This is intentional — the selected state should be the "brighter" highlight.

### Content Structure (Left Side)

The content wrapper uses the `w-0` trick to force truncation in flex:

```tsx
<div className="flex w-0 min-w-0 flex-1 flex-col gap-1.5">
```

**Line 1 — Primary text:**
```tsx
<span className="truncate text-[15px] font-normal">{titleCase(item.name)}</span>
```
- Font: Nunito Sans (body font), 15px, weight 400
- All names use `titleCase()`: `"item-1"` → `"Item-1"`
- `truncate` for ellipsis on overflow

**Line 2 — Secondary text:**
```tsx
<span className="truncate text-[12px] text-muted-foreground">
  {item.subtitle || 'Fallback text'}
</span>
```
- 12px, muted color
- Always provide a fallback (e.g. `"No active record"`, `"Not assigned"`)
- `truncate` for ellipsis

**Line 3 — Metadata (conditional):**
```tsx
{item.metadata && (
  <div className="flex items-center gap-2 text-[11px] text-muted-foreground/70">
    <span>{item.duration}</span>
    <span>&middot;</span>
    <span>{item.stat}</span>
  </div>
)}
```
- 11px, even more muted (70% opacity)
- Only shown when there's relevant data
- Uses middot (`·`) as separator

### Status Indicator (Right Side)

```tsx
<span className={cn('shrink-0 text-[10px] font-medium', statusColor[item.status])}>
  {statusLabel[item.status]}
</span>
```

- `shrink-0`: never truncates, always visible
- 10px, `font-medium`
- Plain text label — no badge, no icon, no background
- Color mapped via `Record<Status, string>`:

| Status | Class |
|--------|-------|
| idle | `text-muted-foreground` |
| active | `text-status-active` |
| busy | `text-status-busy` |
| offline | `text-status-offline` |

### Truncation — The `w-0` Trick

The key to making truncation work in flex layouts:

```tsx
<div className="flex w-0 min-w-0 flex-1 flex-col gap-1.5">
```

- `w-0`: sets initial width to 0
- `flex-1`: grows to fill available space
- `min-w-0`: allows shrinking below content width
- Result: the div takes exactly the available space, never more. Children with `truncate` clip properly.

Without `w-0`, the flex child would size to its content width, pushing the status indicator off-screen.

---

## Status Color Mapping

A reusable pattern for mapping any entity state to visual treatment.

### Define the mapping

```typescript
type EntityStatus = 'idle' | 'active' | 'busy' | 'offline';

const statusLabel: Record<EntityStatus, string> = {
  idle: 'Idle',
  active: 'Active',
  busy: 'Busy',
  offline: 'Offline',
};

const statusColor: Record<EntityStatus, string> = {
  idle: 'text-muted-foreground',
  active: 'text-status-active',
  busy: 'text-status-busy',
  offline: 'text-status-offline',
};
```

### Display variants

Choose the variant based on context:

**Text only** (default for list items):
```tsx
<span className={cn('shrink-0 text-[10px] font-medium', statusColor[status])}>
  {statusLabel[status]}
</span>
```

**Dot indicator** (for compact summaries, pool stats):
```tsx
<span className="flex items-center gap-1.5">
  <span className={cn('size-1.5 rounded-full', `bg-status-${status}`)} />
  <span className="text-[11px] text-muted-foreground">{count} {statusLabel[status].toLowerCase()}</span>
</span>
```

**In chart data** (for pie charts, mapping to chart colors):
```typescript
const statusFills: Record<string, string> = {
  idle: 'var(--color-chart-1)',
  active: 'var(--color-chart-3)',
  busy: 'var(--color-chart-2)',
  offline: 'var(--color-chart-5)',
};
```

### Adding new states

To add a new status:
1. Add to the `AgentStatus` type union
2. Add to `statusLabel` and `statusColor` records
3. Add a `--status-newstate` CSS variable in `index.css` (both light and dark)
4. Add to the `@theme inline` block as `--color-status-newstate`
5. Add to chart fill mappings if used in visualizations

---

## Chart Tooltips

Custom tooltip component for all Recharts charts. Mini-card style with separator.

### Structure

```tsx
<div className="rounded-lg border border-border bg-popover px-3 py-2.5 shadow-md">
  <p className="text-[12px] font-medium text-popover-foreground">{title}</p>
  <div className="my-1.5 h-px bg-border" />
  <div className="flex flex-col gap-1">
    {rows.map(row => (
      <div className="flex items-center justify-between gap-6">
        <div className="flex items-center gap-2">
          <span className="size-2 rounded-full" style={{ backgroundColor: row.color }} />
          <span className="text-[11px] text-muted-foreground">{row.label}</span>
        </div>
        <span className="font-mono text-[11px] font-medium text-popover-foreground">
          {row.value}
        </span>
      </div>
    ))}
  </div>
</div>
```

### Elements

| Element | Style |
|---------|-------|
| Container | `rounded-lg border border-border bg-popover px-3 py-2.5 shadow-md` |
| Title | `text-[12px] font-medium text-popover-foreground` |
| Separator | `my-1.5 h-px bg-border` |
| Color dot | `size-2 rounded-full` with inline `backgroundColor` |
| Label | `text-[11px] text-muted-foreground` |
| Value | `font-mono text-[11px] font-medium text-popover-foreground` |
| Row gap | `gap-6` between label and value for alignment |

### Label Capitalization

**CRITICAL**: All tooltip labels must be capitalized (Title Case). The label is the left-side text describing the value:

```tsx
// CORRECT - "Cost" capitalized
{ color: 'var(--chart-1)', label: 'Cost', value: formatCost(payload[0].value) }

// WRONG - "cost" lowercase
{ color: 'var(--chart-1)', label: 'cost', value: formatCost(payload[0].value) }
```

When deriving labels from data keys, capitalize the first letter:
```tsx
const rows: TooltipRow[] = payload.map((entry) => ({
  color: entry.color ?? 'var(--chart-1)',
  // Capitalize: "input" → "Input"
  label: String(entry.name).charAt(0).toUpperCase() + String(entry.name).slice(1),
  value: `${formatTokens(entry.value)} tokens`,
}));
```

### Usage per chart type

| Chart | Title | Rows |
|-------|-------|------|
| Cost Per Item | Item name | `● Cost  $1.52` |
| Breakdown | Item name | `● Input  495K tokens` / `● Output  66K tokens` / `● Reasoning  16K tokens` |
| Category Usage | Category name | `● Messages  28` |
| Status Distribution | Status name | `● Count  2` |

---

## Toasts (Sonner)

### Configuration

```tsx
<Toaster position="bottom-right" />
```

Custom `Sonner` wrapper:
```tsx
style={{
  "--normal-bg": "var(--popover)",
  "--normal-text": "var(--popover-foreground)",
  "--normal-border": "var(--border)",
  "--border-radius": "var(--radius)",
  "--width": "auto",
}}
toastOptions={{
  classNames: { toast: "cn-toast !w-auto !max-w-[90vw]" },
}}
```

- Auto-width (no fixed width)
- Max 90% viewport width
- No text wrapping

### Copy Toast Pattern

```tsx
toast(
  <span className="flex items-center gap-2 whitespace-nowrap">
    Copied{' '}
    <code className="rounded bg-muted px-1.5 py-0.5 font-mono text-xs font-normal">
      {copiedText}
    </code>
  </span>
);
```

- Text: "Copied" in body font
- Command: in Cascadia Code inside a muted pill (`rounded bg-muted`)
- `whitespace-nowrap` prevents wrapping

### Record Resume Toast

```tsx
toast(
  <span className="flex items-center gap-2 whitespace-nowrap">
    Copied resume command
  </span>
);
```

---

## Code Blocks & Commands

Three variants for displaying code/commands.

### Inline Code

```tsx
<code className="rounded bg-muted px-1.5 py-0.5 font-mono text-xs">text</code>
```

Used in: descriptions, toast content, paragraph text.

### Command Block (copyable)

```tsx
<div className="mt-4 flex items-center gap-3">
  <code className="min-w-0 flex-1 truncate rounded-md bg-muted px-4 py-3 font-mono text-sm">
    {command}
  </code>
  <Button variant="outline" className="shrink-0" onClick={() => onCopy(command)}>
    <HugeiconsIcon icon={Copy01Icon} data-icon="inline-start" />
    Copy
  </Button>
</div>
```

Used in: Guide page steps.

### Pre Block (multi-line, copyable)

```tsx
<div className="flex items-start gap-3">
  <pre className="min-w-0 flex-1 rounded-md bg-muted px-5 py-4 font-mono text-sm leading-relaxed">
    {multiLineContent}
  </pre>
  <Button variant="outline" className="shrink-0" onClick={() => onCopy(content)}>
    <HugeiconsIcon icon={Copy01Icon} data-icon="inline-start" />
    Copy
  </Button>
</div>
```

Used in: SSH config blocks.

### Copy Button

Always uses:
- `<Button variant="outline">` (default size for guide, `size="sm"` elsewhere)
- `<HugeiconsIcon icon={Copy01Icon} data-icon="inline-start" />`
- Text: "Copy"
- `className="shrink-0"` to prevent compression

---

## Guide Page (Step Grid)

The guide page uses a simple 2x2 grid of sealed cells for steps.

**CRITICAL RULES**:
1. **ONE PageHeader only** — at the very top, no other title bars or section headers anywhere
2. **bg-card background** — the entire page uses white (light) / dark card (dark) background
3. **NO Card components** — steps are plain text directly in the grid cells
4. **NO nested sections** — don't create "iPhone section" and "Android section" with their own headers
5. **NO rounded bordered boxes** — the grid borders ARE the visual structure

### Page Structure

The guide page is a FLAT structure — just PageHeader + grid rows:

```tsx
<ScrollArea className="h-full bg-card">
  <div className="flex flex-col">
    {/* ONE title bar only */}
    <PageHeader title="Quick Start Guide" description="Get started with..." />
    
    {/* Steps row 1 — directly in grid, no wrapper sections */}
    <div className="grid grid-cols-2 border-b border-border">
      <div className="min-h-[216px] px-8 py-8">{/* Step 1 content */}</div>
      <div className="min-h-[216px] border-l border-border px-8 py-8">{/* Step 2 content */}</div>
    </div>
    
    {/* Steps row 2 */}
    <div className="grid grid-cols-2 border-b border-border">
      <div className="min-h-[216px] px-8 py-8">{/* Step 3 content */}</div>
      <div className="min-h-[216px] border-l border-border px-8 py-8">{/* Step 4 content */}</div>
    </div>
    
    {/* Optional full-width section */}
    <div className="px-8 py-8">{/* Additional content */}</div>
  </div>
</ScrollArea>
```

**WRONG — Don't do this:**

```tsx
{/* WRONG — nested sections with their own headers */}
<div className="grid grid-cols-2">
  <div className="p-8">
    <h2>iPhone install track</h2>  {/* NO — this creates a nested section */}
    <p>Description...</p>
    <div className="border rounded-lg p-4">  {/* NO — nested card */}
      <span>STEP 1</span>
      ...
    </div>
  </div>
  <div className="p-8">
    <h2>Android install track</h2>  {/* NO */}
    ...
  </div>
</div>
```

The grid cells ARE the sections — don't nest sections inside them.

### Step Cell

Each step cell has a minimum height of 216px. Content is plain text — **NO Card components, NO rounded bordered containers**:

```tsx
{/* CORRECT — plain text content */}
<div className="min-h-[216px] px-8 py-8">
  <span className="text-[11px] font-medium uppercase tracking-widest text-muted-foreground">
    Step N
  </span>
  <h2 className="mt-3 font-heading text-xl font-normal">Step Title</h2>
  <p className="mt-2 text-sm text-muted-foreground">
    Step description.
  </p>
  <CommandBlock command="ssh item-1" onCopy={handleCopy} />
</div>

{/* WRONG — do NOT use Card components or bordered boxes */}
<div className="px-8 py-8">
  <Card>  {/* NEVER DO THIS */}
    <CardHeader>...</CardHeader>
    <CardContent>...</CardContent>
  </Card>
</div>

{/* WRONG — do NOT use rounded bordered containers */}
<div className="px-8 py-8">
  <div className="rounded-lg border p-4">  {/* NEVER DO THIS */}
    ...
  </div>
</div>
```

The guide page already has visual structure from the sealed grid borders — adding cards inside creates visual noise and breaks the hermetically sealed aesthetic.

| Property | Value | Notes |
|----------|-------|-------|
| Min height | `min-h-[216px]` | Ensures visual consistency across steps |
| Padding | `px-8 py-8` | Generous for readability |
| Step label | `text-[11px] font-medium uppercase tracking-widest text-muted-foreground` | |
| Heading | `mt-3 font-heading text-xl font-normal` | |
| Description | `mt-2 text-sm text-muted-foreground` | |

---

## Copy Action Button (Table Icon)

Small icon button placed in the first column of tables for row-level actions (e.g. copying a command).

```tsx
<button
  onClick={handleCopy}
  className="cursor-pointer text-muted-foreground/50 hover:text-foreground"
>
  <HugeiconsIcon icon={Copy01Icon} className="size-3.5" />
</button>
```

- Icon only, no text
- Very muted (50% opacity), brightens on hover
- Copies: the relevant command (e.g. `some-cli --resume ${recordId}`)
- Shows toast: "Copied resume command"

---

## Formatting Utilities

### `formatCost(cost: number | undefined): string`

```
undefined / 0  → "$0.00"
< 0.01         → "< $0.01"
>= 0.01        → "$X.XX" (2 decimal places)
```

### `formatTokens(count: number): string`

```
0              → "0"
< 1000         → "123"
1000-9999      → "1.2K" (1 decimal)
10000-999999   → "185K" (no decimal)
>= 1000000    → "1.2M" (1 decimal)
```

### `titleCase(str: string): string`

Capitalizes first letter of each word: `"item-1"` → `"Item-1"`.

Used on all display names — entity IDs, record references, status labels.
