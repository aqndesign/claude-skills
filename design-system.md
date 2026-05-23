# Skill: Design System Setup

Apply a foundational design system using Radix UI Themes and a custom visual layer on top. Run this skill whenever starting a new app or when adding a design system to an existing one.

---

## Step 1 — Install Radix UI Themes

```bash
npm install @radix-ui/themes
```

---

## Step 2 — Import the Radix stylesheet

Add this import at the very top of the application entry point (e.g. `app/layout.tsx`, `src/main.tsx`, or `pages/_app.tsx`). It must come before any other CSS imports so the design tokens load first.

```ts
import "@radix-ui/themes/styles.css";
```

---

## Step 3 — Wrap the app in the Theme provider

In the root layout file, wrap all children inside the `<Theme>` component. Use the props below as the baseline — they can be adjusted per project, but keep `radius="full"` to satisfy the custom visual layer (see Step 5).

```tsx
import { Theme } from "@radix-ui/themes";

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        <Theme
          accentColor="indigo"
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

Create a global CSS file (e.g. `app/globals.css` or `src/styles/globals.css`) and add the rules below. Import this file in the root layout **after** the Radix stylesheet import.

```css
/* ─── Custom Visual Layer ─────────────────────────────────────────────────── */

/* 1. Fully rounded buttons
   Overrides Radix's radius tokens so every button variant is pill-shaped,
   regardless of the Theme radius prop. */
.rt-Button,
.rt-reset.rt-Button {
  border-radius: 9999px !important;
}

/* 2. Base transition for modals, dialogs, and popovers
   All overlay content panels animate in and out with a 350ms ease-in-out
   curve. Radix mounts/unmounts with data-state attributes we can target. */

/* Dialog & AlertDialog */
.rt-DialogContent,
.rt-AlertDialogContent {
  transition:
    opacity 350ms ease-in-out,
    transform 350ms ease-in-out;
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

/* Popover */
.rt-PopoverContent {
  transition:
    opacity 350ms ease-in-out,
    transform 350ms ease-in-out;
}

.rt-PopoverContent[data-state="open"] {
  opacity: 1;
  transform: scale(1);
}

.rt-PopoverContent[data-state="closed"] {
  opacity: 0;
  transform: scale(0.96);
}

/* Overlay backdrop */
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

## Step 6 — Verify the setup

1. Run the dev server and open the app in a browser.
2. Confirm buttons are pill-shaped (fully rounded).
3. Open and close a Dialog or Popover and confirm the 350ms fade+scale animation plays on both entry and exit.
4. Check the browser console for any missing CSS import warnings.

---

## Notes

- The `radius="full"` prop on `<Theme>` handles Radix's internal token, while the CSS override in Step 5 is a hard guarantee for buttons regardless of theme changes.
- If the project uses Radix Primitives (`@radix-ui/react-dialog`, etc.) instead of Radix Themes, the CSS class names will differ — target `[data-radix-popper-content-wrapper]` and `[role="dialog"]` instead.
- Never mix Tailwind `rounded-*` classes on Radix buttons — let the CSS layer own the border-radius to keep it consistent.
- The `!important` on the button rule is intentional: Radix inlines radius via a CSS variable that has higher specificity without it.
