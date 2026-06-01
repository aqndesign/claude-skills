# Skill: Design System Setup

Apply a foundational design system using Radix UI Themes, Tailwind v4, and a custom visual layer on top. Run this skill whenever starting a new app or when adding a design system to an existing one.

---

## Step 1 — Install dependencies

```bash
npm install @radix-ui/themes
npm install --save-dev tailwindcss @tailwindcss/postcss
```

---

## Step 2 — Load fonts via next/font/google

In the root layout (e.g. `app/layout.tsx`), load fonts with Next.js's built-in font optimization — **do not use `<link>` tags for Google Fonts**. Three fonts are in use:

- **IBM Plex Sans** — headings (`--font-ibm-plex-sans`)
- **Sora** — data viz values (`--font-sora`)
- **Source Sans 3** — body text (`--font-source-sans-3`)

```tsx
import { IBM_Plex_Sans, Sora, Source_Sans_3 } from "next/font/google";

const ibmPlexSans = IBM_Plex_Sans({
  subsets: ["latin"],
  weight: ["100", "200", "300", "400", "500", "600", "700"],
  style: ["normal", "italic"],
  variable: "--font-ibm-plex-sans",
});
const sora = Sora({ subsets: ["latin"], variable: "--font-sora" });
const sourceSans3 = Source_Sans_3({
  subsets: ["latin"],
  style: ["normal", "italic"],
  variable: "--font-source-sans-3",
});
```

Apply all three CSS variables to the `<html>` element:

```tsx
<html lang="en" className={`${ibmPlexSans.variable} ${sora.variable} ${sourceSans3.variable}`}>
```

Then import stylesheets in this order in the root layout, **before** your own CSS:

```ts
import "@radix-ui/themes/styles.css";
import "@carbon/charts-react/styles.css"; // only if using Carbon Charts — see Step 6
import "./globals.css";
```

---

## Step 3 — Wrap the app in the Theme provider

```tsx
import { Theme } from "@radix-ui/themes";

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en" className={`${ibmPlexSans.variable} ${sora.variable} ${sourceSans3.variable}`}>
      <body>
        <Theme
          accentColor="blue"
          grayColor="slate"
          radius="full"
          scaling="100%"
          appearance="light"
        >
          {children}
        </Theme>
      </body>
    </html>
  );
}
```

---

## Step 4 — Use only Radix components for UI

All UI elements must come from `@radix-ui/themes`. Do not reach for raw HTML elements or other component libraries for anything Radix covers. Common imports:

```tsx
import {
  Button,
  Dialog,
  AlertDialog,
  Popover,
  DropdownMenu,
  Flex,
  Box,
  Text,
  Heading,
  Card,
  TextField,
  Select,
  Separator,
  Badge,
  Avatar,
  Tooltip,
  ScrollArea,
} from "@radix-ui/themes";
```

Reference: https://www.radix-ui.com/themes/docs/components/button

---

## Step 5 — Apply the custom visual layer

Create `app/globals.css` (or `src/styles/globals.css`) with the rules below. Import it in the root layout **after** the Radix and Carbon stylesheets.

```css
@import "tailwindcss";

/* ─── Design tokens ──────────────────────────────────────────────────────── */

:root {
  --font-heading: var(--font-ibm-plex-sans);
  --font-body: var(--font-source-sans-3);

  --color-primary: #1C2024;
  --color-secondary: #60646C;

  /* Brand gradient — adjust per project */
  --brand-gradient: linear-gradient(to right, #3300FC, #95008A, #3C5CDD);
  --brand-gradient-hover: linear-gradient(to right, #2800e0, #7e0075, #3050c8);
}

/* ─── Radix font token overrides ─────────────────────────────────────────── */

/* IMPORTANT: Radix sets --default-font-family and --heading-font-family on
   .radix-themes, not :root. Overriding at :root level is silently ignored
   because .radix-themes has higher specificity. Always override on
   .radix-themes to ensure the custom fonts are actually applied. */
.radix-themes {
  --default-font-family: var(--font-source-sans-3), system-ui, sans-serif;
  --heading-font-family: var(--font-ibm-plex-sans), system-ui, sans-serif;
}

/* ─── Typography ─────────────────────────────────────────────────────────── */

body {
  font-family: var(--font-body), system-ui, sans-serif;
  color: var(--color-primary);
}

h1, h2, h3, h4, h5, h6,
.rt-Heading {
  font-family: var(--font-heading), system-ui, sans-serif;
}

/* H1/H2/H3 — semi-bold (600). Confirmed correct for this design system. */
h1, h2, h3,
h1.rt-Heading, h2.rt-Heading, h3.rt-Heading {
  font-weight: 600;
}

h4,
h4.rt-Heading {
  font-weight: 700;
}

/* ─── Brand accent override ──────────────────────────────────────────────── */

/* Radix accentColor only accepts named scales; these CSS overrides pin the
   exact brand blue. accent-9 = solid bg, accent-10 = hover, accent-11 = text,
   accent-contrast = text on solid accent surfaces. */
.radix-themes {
  --accent-9:        #0064E0;
  --accent-10:       #0055C4;
  --accent-11:       #0047A6;
  --accent-contrast: #ffffff;
}

/* ─── Radix gray token overrides ─────────────────────────────────────────── */

/* Pull default text/icon colors into the brand palette */
.radix-themes {
  --gray-12: var(--color-primary);
  --gray-11: var(--color-secondary);
  --gray-a12: var(--color-primary);
  --gray-a11: var(--color-secondary);
}

/* ─── Data viz text tokens ───────────────────────────────────────────────── */

/* Large: KPI hero numbers, chart callout values, prominent tooltips */
.data-viz-lg {
  font-family: var(--font-sora), system-ui, sans-serif;
  font-size: 24px;
  font-weight: 500;
  line-height: 1;
  letter-spacing: -0.02em;
}

/* Small: compact stats in cards, chart annotations */
.data-viz-sm {
  font-family: var(--font-sora), system-ui, sans-serif;
  font-size: 15px;
  font-weight: 500;
  line-height: 1.2;
  letter-spacing: -0.01em;
}

/* ─── Carbon Charts overrides ────────────────────────────────────────────── */

:root {
  --cds-charts-font-family: var(--font-source-sans-3), system-ui, sans-serif;
  --cds-charts-font-family-condensed: var(--font-source-sans-3), system-ui, sans-serif;
}

/* 2px white gap between donut/pie segments */
.cds--cc--pie path.slice,
.cds--cc--donut path.slice {
  stroke: white;
  stroke-width: 2px;
}

/* ─── Buttons ────────────────────────────────────────────────────────────── */

/* Pill shape — the !important is intentional: Radix inlines radius via a CSS
   variable at higher specificity, so this is the only reliable override. */
.rt-Button,
.rt-reset.rt-Button {
  border-radius: 9999px !important;
}

/* ─── Card styling ───────────────────────────────────────────────────────── */

/* Default Radix card: thin 0.5px border, no shadow */
.rt-Card {
  transition: box-shadow 250ms ease, background-color 250ms ease !important;
}

.rt-BaseCard {
  --base-card-surface-box-shadow:       0 0 0 0.5px #D9D9E0 !important;
  --base-card-surface-hover-box-shadow: 0 0 0 0.5px #D9D9E0 !important;
  box-shadow: none !important;
}

/* Glass card — frosted glass panel.
   Requires a visually rich background layer behind it (e.g. a blurred gradient
   or BlobCanvas) otherwise the blur has nothing to act on.
   Apply .card-glass to any Box, Card, or div that should look frosted. */
.card-glass {
  background:        rgba(255, 255, 255, 0.80);
  backdrop-filter:         blur(28px) saturate(1.8) brightness(1.04);
  -webkit-backdrop-filter: blur(28px) saturate(1.8) brightness(1.04);
  border:       0.5px solid rgba(255, 255, 255, 0.75);
  border-radius: 20px;
  overflow:      hidden;
  box-shadow:
    0 2px 23px rgba(0, 0, 0, 0.054),
    inset 0 1px 0 rgba(255, 255, 255, 0.9);
}

/* Interactive card ring — use on clickable cards that need a brand-blue
   focus ring on hover. Works via a ::after pseudo-element so it can overlay
   the card's own border without affecting layout.
   Pair with cursor: pointer (the global rule above already covers .rt-Card). */
.card-interactive {
  position: relative;
  transition:
    box-shadow   250ms ease,
    transform    250ms ease,
    background-color 250ms ease !important;
}

.card-interactive::after {
  content: "";
  pointer-events: none;
  position: absolute;
  inset: 0;
  border-radius: inherit;
  transition: box-shadow 150ms ease;
  box-shadow:
    inset 0 0 0 0px color-mix(in srgb, var(--accent-9) 0%, transparent),
    0 0 0 0.5px #D9D9E0;
}

.card-interactive:hover::after {
  box-shadow:
    inset 0 0 0 2px color-mix(in srgb, var(--accent-9) 10%, transparent),
    0 0 0 1px var(--accent-9);
}

/* ─── Interactive hover transitions ──────────────────────────────────────── */

.rt-Button,
.rt-BaseButton {
  transition:
    background-color 200ms ease-in-out,
    color            200ms ease-in-out,
    box-shadow       200ms ease-in-out,
    opacity          200ms ease-in-out,
    transform        200ms ease-in-out !important;
}

/* Solid buttons: lift on hover */
.rt-Button.rt-variant-solid:not([data-accent-color]):hover,
.rt-Button.rt-variant-solid:not([data-accent-color]):focus-visible {
  transform: translateY(-2px);
  box-shadow: 0 4px 16px rgba(28, 32, 36, 0.1) !important;
}

.rt-Badge {
  transition: background-color 250ms ease, color 250ms ease !important;
}

.rt-TextField-root,
.rt-TextArea {
  transition: box-shadow 250ms ease, border-color 250ms ease !important;
}

.rt-CheckboxButton,
.rt-RadioGroupItem {
  transition: background-color 250ms ease, border-color 250ms ease !important;
}

a {
  transition: opacity 250ms ease, color 250ms ease;
}

/* ─── Cursor for clickable components ────────────────────────────────────── */

.rt-Button,
.rt-BaseButton,
.rt-CheckboxButton,
.rt-RadioGroupItem,
.rt-SegmentedControlItem,
.rt-SelectTrigger,
.rt-SliderThumb,
.rt-SwitchButton,
.rt-TabsTrigger,
.rt-DropdownMenuTrigger,
.rt-ContextMenuTrigger,
.rt-MenubarTrigger,
.rt-TooltipTrigger,
.rt-PopoverTrigger,
.rt-DialogTrigger,
.rt-AlertDialogTrigger,
.rt-HoverCardTrigger,
.rt-AccordionTrigger,
a,
button,
[role="button"],
[role="tab"],
[role="checkbox"],
[role="radio"],
[role="menuitem"],
[role="option"],
label {
  cursor: pointer;
}

/* ─── Dialog / AlertDialog animations ───────────────────────────────────── */

.rt-DialogContent,
.rt-AlertDialogContent {
  transition: opacity 350ms ease-in-out, transform 350ms ease-in-out;
}

.rt-DialogContent[data-state="open"],
.rt-AlertDialogContent[data-state="open"] {
  opacity: 1;
  transform: translate(-50%, -50%) scale(1);
}

.rt-DialogContent[data-state="closed"],
.rt-AlertDialogContent[data-state="closed"] {
  opacity: 0;
  transform: translate(-50%, -50%) scale(0.96);
}

/* ─── Popover animations ─────────────────────────────────────────────────── */

.rt-PopoverContent {
  transition: opacity 350ms ease-in-out, transform 350ms ease-in-out;
}

.rt-PopoverContent[data-state="open"] {
  opacity: 1;
  transform: scale(1);
}

.rt-PopoverContent[data-state="closed"] {
  opacity: 0;
  transform: scale(0.96);
}

/* ─── Overlay backdrop ───────────────────────────────────────────────────── */

.rt-DialogOverlay,
.rt-AlertDialogOverlay {
  transition: opacity 350ms ease-in-out;
}

.rt-DialogOverlay[data-state="open"],
.rt-AlertDialogOverlay[data-state="open"] {
  opacity: 1;
}

.rt-DialogOverlay[data-state="closed"],
.rt-AlertDialogOverlay[data-state="closed"] {
  opacity: 0;
}
```

---

## Step 6 — Install Carbon Charts for data visualization

All charts and data visualization components must come from `@carbon/charts-react`. Do not use Recharts, Chart.js, D3, Victory, or any other charting library.

```bash
npm install @carbon/charts-react
```

Import the Carbon Charts stylesheet in the root layout **after** the Radix stylesheet and **before** `globals.css` (see Step 2).

Available chart components (all imported from `@carbon/charts-react`):

**Standard charts**
- `AlluvialChart` — flow/sankey diagrams
- `AreaChart` / `StackedAreaChart` — area over time
- `BoxplotChart` — statistical distributions
- `BubbleChart` — three-variable scatter
- `BulletChart` — progress vs. target
- `CirclePackChart` — hierarchical circles
- `ComboChart` — mixed bar + line
- `DonutChart` / `PieChart` — part-to-whole
- `GaugeChart` — single-value meter
- `GroupedBarChart` / `StackedBarChart` — categorical comparisons
- `HeatmapChart` — two-axis density
- `HistogramChart` — frequency distribution
- `LineChart` — trends over time
- `LollipopChart` — ranked values
- `MeterChart` — utilization/progress
- `RadarChart` — multivariate comparison
- `ScatterChart` — correlation
- `TreeChart` / `TreemapChart` — hierarchical data
- `WordCloudChart` — frequency by text

**Diagram components** (from `@carbon/charts-react`)
- `CardNode`, `CardNodeColumn`, `CardNodeLabel`, `CardNodeSubtitle`, `CardNodeTitle`
- `Edge`, `Marker`, `ShapeNode`

Example usage:

```tsx
import { DonutChart } from "@carbon/charts-react";

export function MyChart() {
  return (
    <DonutChart
      data={[
        { group: "Assigned", value: 51 },
        { group: "Available", value: 18 },
        { group: "Coworking", value: 203 },
      ]}
      options={{
        title: "Workspace breakdown",
        resizable: true,
        donut: { center: { label: "Workspaces" } },
        height: "300px",
      }}
    />
  );
}
```

---

## Step 7 — Verify the setup

1. Run the dev server and open the app in a browser.
2. Confirm buttons are pill-shaped (fully rounded) and use the brand blue `#0064E0`.
3. Confirm solid buttons lift (`translateY(-2px)`) on hover.
4. Open and close a Dialog or Popover and confirm the 350ms fade+scale animation plays on both entry and exit.
5. Apply `.data-viz-lg` and `.data-viz-sm` to test elements and confirm Sora renders at the right sizes in Medium weight.
6. Render a Carbon chart and confirm Source Sans 3 is used for chart text and white gaps appear between donut/pie segments.
7. Place a `.card-glass` element over a colourful background and confirm the frosted-glass blur is visible.
8. Add `.card-interactive` to a clickable card and confirm the thin resting border transitions to a brand-blue ring on hover.
9. Check the browser console for any missing CSS import warnings.

---

## Notes

- Use `next/font/google` — never `<link>` tags — for font loading in Next.js projects. This enables automatic font optimization and avoids a network waterfall.
- `.card-glass` requires a visually rich layer behind it (gradient canvas, blurred blobs, etc.) — applied over a flat white background the effect will be invisible. Always place `.card-glass` panels on top of a background that gives the blur something to act on.
- `.card-interactive` uses a `::after` pseudo-element for the hover ring so the ring overlays the card without affecting layout. Make sure the element has `position: relative` (the class sets this) and `border-radius` on the element itself so `border-radius: inherit` works correctly.
- The `radius="full"` prop on `<Theme>` handles Radix's internal token; the `border-radius: 9999px !important` in CSS is a hard guarantee for buttons regardless of future theme changes.
- If the project uses Radix Primitives (`@radix-ui/react-dialog`, etc.) instead of Radix Themes, CSS class names differ — target `[data-radix-popper-content-wrapper]` and `[role="dialog"]` instead.
- Never mix Tailwind `rounded-*` classes on Radix buttons — let the CSS layer own border-radius to keep it consistent.
- **All data visualization must use `@carbon/charts-react`.** Never write custom SVG charts or reach for another charting library.
- The `--brand-gradient` tokens are a starting point — adjust the colors per project but keep the variable names consistent so components can reference them uniformly.
