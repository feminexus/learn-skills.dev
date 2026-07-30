---
name: clone-style
description: Clone a website's visual design style and apply it to the current project. Extracts colors, typography, spacing, and component patterns, then rebuilds the UI to match. Trigger with /clone-style <url>.
---

# Clone Style Skill

Clone the visual design language of any website into the current project. This skill extracts design tokens from a live URL and methodically rebuilds the frontend to match.

## Usage

```
/clone-style <target-url>
```

Example: `/clone-style https://linear.app`

---

## Phase 1: Reconnaissance

### Step 1.1 — Fetch the target page

Use `WebFetch` to download the target URL. Request the full page content with this prompt:

> "Extract ALL design-relevant information from this page: 1) Every CSS color value used (hex, rgb, hsl, or CSS variable names — scan inline styles, class names that imply colors like bg-*, text-*, border-*), 2) Font families, font sizes, font weights, line heights, letter spacing — anything typography related, 3) Spacing patterns — padding, margin, gap values, 4) Border radius values, 5) Box shadows, 6) Any animation or transition patterns, 7) The layout structure — header/nav/sidebar/main/footer, 8) Button styles (primary, secondary, ghost variants), 9) Input/textarea styles, 10) Card/container styles. Be as EXHAUSTIVE as possible. List every color value you find."

### Step 1.2 — Fetch CSS files

If the page references external stylesheets, fetch those too. Use WebFetch on each CSS URL found. Extract:
- CSS custom properties (`--*`)
- Utility class definitions
- Component class definitions

### Step 1.3 — Write Design Spec

Create `docs/design-spec.md` with these sections:

```markdown
# Design Specification — [Site Name]

## Color Palette
- Background: #xxx → HSL(x, x%, x%)
- Surface/Card: #xxx → HSL(x, x%, x%)
- Text Primary: #xxx → HSL(x, x%, x%)
- Text Secondary: #xxx → HSL(x, x%, x%)
- Text Muted: #xxx → HSL(x, x%, x%)
- Border: #xxx → HSL(x, x%, x%)
- Brand/Accent: #xxx → HSL(x, x%, x%)
- Destructive: #xxx → HSL(x, x%, x%)

## Typography
- Display/Heading font: "Font Name", weight, size range
- Body font: "Font Name", 400, 14-16px
- Code font: "Font Name" (or monospace)

## Spacing
- Base unit: Xpx
- Component padding: Xpx
- Section gap: Xpx
- Card padding: Xpx

## Borders & Radius
- Border color: #xxx
- Border width: Xpx
- Border radius (sm/md/lg): Xpx / Xpx / Xpx

## Shadows
- sm: Xpx Xpx Xpx rgba(...)
- md: Xpx Xpx Xpx rgba(...)
- lg: Xpx Xpx Xpx rgba(...)

## Component Patterns
(Describe button variants, input styles, card styles, etc.)
```

---

## Phase 2: Apply Design Tokens

### Step 2.1 — Update `globals.css`

Read the current `src/app/globals.css`. Replace the `:root` and `.dark` CSS variable blocks with values derived from the design spec. Convert all hex colors to HSL for Tailwind v3 compatibility:

```css
:root {
  --background: H S% L%;
  --foreground: H S% L%;
  ...
}
```

**Conversion rule**: hex `#1a1a2e` → `hsl(240, 28%, 14%)`.

### Step 2.2 — Update `tailwind.config.js`

Read the current config. Update the `extend.colors` section to match new tokens. Update `extend.borderRadius` to match. If the target uses a distinctive font, add it to `fontFamily`.

### Step 2.3 — Update `layout.tsx`

If the target uses Google Fonts or custom fonts, add the appropriate `<link>` tags or `next/font` imports to `layout.tsx`.

---

## Phase 3: Rebuild Components

For each component file in the project, read it and update its styling to match the design spec:

1. **Button variants**: Match primary/secondary/ghost/outline to target's button styles
2. **Input/Textarea**: Match border, focus ring, padding, border-radius
3. **Card**: Match background, border, shadow, padding
4. **Badge**: Match colors for each variant
5. **Message bubbles**: Match the chat message styling

**Guiding principle**: Don't change the component's logic or props interface — only the visual styling (Tailwind classes, CSS variables).

---

## Phase 4: Verify

1. Run `npx tsc --noEmit` in the frontend directory — must pass with zero errors
2. Run `npm run build` if applicable (or at minimum verify no import errors)
3. Review the design spec against the actual rendered output — note any discrepancies
4. If dark mode is supported, verify both light and dark themes

---

## Rules

- **Never change component logic** — only visual styling (colors, fonts, spacing, borders, shadows)
- **Preserve existing functionality** — all event handlers, state management, and data flow stay intact
- **Extract first, code second** — always write the design spec before touching any component files
- **Convert to HSL** — all colors in CSS variables must use HSL format for Tailwind compatibility
- **Match existing patterns** — if the project already uses shadcn/UI components, keep using them; just update their styling tokens
- **Handle missing info gracefully** — if the target page doesn't reveal a specific value (e.g., a shadow), use a sensible default that fits the overall aesthetic
