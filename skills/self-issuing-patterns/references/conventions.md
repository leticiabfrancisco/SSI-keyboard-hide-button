# Self Service Issuing — Coding Conventions

You are an expert frontend developer building a **Tax Free Self Service Issuing** kiosk application for iPad. The app uses **MUI v6** components (imported from `@weareplanet/solar-ds-react`) and runs in **iPad landscape mode** (1194×834 viewport). Follow every rule below exactly.

## §1. Layout

- **Target device**: iPad landscape — viewport is 1194×834px. No desktop/mobile responsive breakpoints needed initially (portrait mode support coming later).
- **No sidebar**: This is a kiosk app, NOT a portal. Do not use `<Sidebar>`, `<Drawer>`, or navigation context.
- **Screen structure**: Every wizard step follows this vertical stack:
  ```
  ┌─────────────────────────────────────┐
  │ PageHead (57px)                      │  ← Planet logo + "Tax Free form"
  ├─────────────────────────────────────┤
  │ Content area (flexible height)       │  ← Stepper + cards, max-width 800px, centered
  ├─────────────────────────────────────┤
  │ Footer (104px)                       │  ← Cancel / Back / Next buttons
  └─────────────────────────────────────┘
  ```
- **Content centering**: The content area is horizontally centered with `max-width: 800px` and vertical padding `32px` from the PageHead.
- **Page background**: `bgcolor: 'surface.surface'` (light grey, #f4f4f5 in light mode).
- **Full-height layout**: The screen fills 100% of the viewport height. Use `height: '100vh'` on the root and `flex: 1` on the content area.

## §2. Typography

The design uses two font families with specific tokens:

| Token | Family | Weight | Size | Line Height | Letter Spacing |
|-------|--------|--------|------|-------------|----------------|
| `h1` | Title font | 500 (medium) | 58px | 77px | -3.5px |
| `h5` | Title font | 500 (medium) | 24px | 34px | -1px |
| `h6` | Title font | 500 (medium) | 17px | 25px | -1px |
| `28px` (custom) | Title font | 400 (regular) | 28px | 34px | -1px |
| `body1` | Body font | 400 (regular) | 16px | 1.5 | 0 |
| `body2` | Body font | 400 (regular) | 14px | 1.57 | 0 |
| `subtitle2` | Body font | 500 (medium) | 14px | 1.57 | 0 |
| `caption` | Body font | 500 (medium) | 12px | 1.66 | 0 |
| `button/XL` | Body font | 500 (medium) | 18px | 24px | 0 |

- **Negative letter-spacing** on all title variants (h1–h6, 28px). This is distinctive to the Tax Free product.
- Use `variant` prop on `<Typography>` for standard tokens. For the custom `28px` style, apply via `sx`.

## §3. Elevation & Surfaces

- **Content cards**: `elevation={5}` with the CornerLightning/5 shadow system (5-layer drop shadow). `borderRadius: 3` (24px).
- **Nested cards** (e.g. receipt item containers): `elevation={3}` with CornerLightning/3 shadow. `borderRadius: 2` (16px).
- **Page background**: `surface.surface` — light grey (#f4f4f5).
- **Card background**: White (`#ffffff` / `background.paper`).
- **Never** use `elevation={0}` with border for cards. Always use shadow-based elevation.

## §4. Footer

The footer is a persistent navigation bar at the bottom of every wizard step.

```tsx
<Box sx={{
  position: 'sticky',
  bottom: 0,
  width: '100%',
  height: 104,
  px: 3,
  display: 'flex',
  alignItems: 'center',
  justifyContent: 'space-between',
  borderTop: 1,
  borderColor: 'divider',
  bgcolor: 'background.paper',
}}>
  {/* Left: Cancel button */}
  <Button variant="outlined" size="large">Cancel</Button>
  
  {/* Right: Back + Next */}
  <Stack direction="row" spacing={2} alignItems="center">
    <Button variant="text" size="large" startIcon={<ChevronLeftIcon />}>Back</Button>
    <Button variant="contained" size="large">Next</Button>
  </Stack>
</Box>
```

- **Cancel** is always on the left, outlined style.
- **Back** is text style with a chevron-left icon (hidden on step 1).
- **Next** is always on the right, contained/primary style.
- Footer is `104px` tall with `borderTop: 1` separator.
- On the first step, only Cancel (left) and Next (right) appear.

## §5. Stepper

A horizontal 4-step stepper sits at the top of the content area (below PageHead, above cards).

**Visual anatomy** (from design reference):

```
● 1  Receipts  –  ○ 2  Profile  –  ○ 3  Refund  –  ○ 4  Form
```

- Each step is rendered **inline**: circle + label side-by-side on the same row. The label is never below the circle.
- Connector between steps is a **`–` dash character** (not a horizontal line or divider component).
- **Active step**: solid black filled circle (`#18181b`), white number inside, bold label in `text.primary`.
- **Future steps**: solid grey filled circle (`outline.outlinelow` / `#a1a1aa`), white number inside, grey label in `text.secondary`. Never use an outlined/transparent circle — always a solid grey fill.
- **Completed steps** (steps before active): solid black filled circle (`#18181b`), white `✓` checkmark inside, grey label in `text.secondary`.
- Centered horizontally at ~384px width, `mx: 'auto'`.
- Sits `32px` below the PageHead divider and `24px` above the first content card.

```tsx
// Canonical stepper implementation
<Box sx={{ display: 'flex', alignItems: 'center', width: 384, mx: 'auto', mb: 3 }}>
  {[
    { label: 'Receipts', index: 0 },
    { label: 'Profile',  index: 1 },
    { label: 'Refund',   index: 2 },
    { label: 'Form',     index: 3 },
  ].map((step, i, arr) => {
    const isDone   = activeStep > step.index;
    const isActive = activeStep === step.index;
    return (
      <React.Fragment key={step.label}>
        <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.75 }}>
          {/* Circle */}
          <Box sx={{
            width: 28, height: 28, borderRadius: '50%',
            bgcolor:     isDone || isActive ? '#18181b' : 'outline.outlinelow',
            border:      'none',
            color:       '#fff',  /* always white — black bg for done/active, grey bg for future */
            display: 'flex', alignItems: 'center', justifyContent: 'center',
            fontSize: 13, fontWeight: 600,
          }}>
            {isDone ? (
              <img src="assets/stepper-check.svg" width={14} height={14} alt="" />
            ) : step.index + 1}
          </Box>
          {/* Label */}
          <Typography
            variant="caption"
            sx={{
              fontWeight: isActive ? 700 : 500,
              color: isActive ? 'text.primary' : 'text.secondary',
            }}
          >
            {step.label}
          </Typography>
        </Box>
        {/* Dash connector — not a line */}
        {i < arr.length - 1 && (
          <Typography variant="caption" sx={{ mx: 1, color: 'text.secondary' }}>–</Typography>
        )}
      </React.Fragment>
    );
  })}
</Box>
```

## §6. Product List (Receipts)

Receipt cards contain a product list with these conventions:

- **Receipt card**: `Paper elevation={5}`, contains a collapsible header (chevron + "Receipt N" title + "Remove" delete button).
- **Category filter tabs**: Horizontal row of icon+label chips for filtering products:
  - `list` = All (default, shows all items)
  - `checkroom` = Fashion
  - `grocery` = Food
  - `block` = Not eligible (items that cannot be refunded)
  - Use MUI `Chip` with `icon` prop and `variant="outlined"` (filled when active).
- **Product row**: Each item shows: `[Name] [Quantity (if editable)] [Price] [Delete IconButton]`
  - Name: `subtitle2` typography, left-aligned.
  - Quantity: Only shown for eligible items with quantity > 1. Display as plain text or `<Select>` if editable.
  - Price: Right-aligned, `subtitle2`, formatted as `"XX.XX EUR"`.
  - Delete: `IconButton` with `delete` icon. Hidden for non-eligible items (they have no delete action).
- **Product row height**: 88px per item.
- **Non-eligible items**: Items like "Fresh Juice", "Toast with Butter", "Tuna Sandwich" cannot be deleted and show quantity as read-only. They are filtered into the "Not eligible" category tab.
- **Info alert**: `<Alert severity="warning">` at the top: "Remove any items you will use, open or leave in the EU."
- **Total estimated refund**: Displayed below the receipt list in a summary row: `"Total estimated refund: XX.XX EUR"` (bold/subtitle2 styling).
- **Footer extras**: On the receipts step, the footer adds a `"Scan new receipt"` button (outlined, with camera icon) between Cancel and Next.
- **"Scan new receipt" button**: `variant="outlined"`, `startIcon={<PhotoCameraIcon />}`, positioned in the center of the footer.

## §7. Read-only Fields (Personal Details)

The personal details confirmation card shows scanned ID data in a read-only grid:

```
┌─────────────────────────────────────────┐
│ Confirm personal details          (h5)  │
├──────────────────┬──────────────────────┤
│ Document number  │ Document date expiry │
│ P123456AA        │ 03/12/2027           │
├──────────────────┬──────────────────────┤
│ Document country │ Date of birth        │
│ 🇬🇧 United Kingdom │ 01/01/1990          │
├──────────────────┬──────────────────────┤
│ Given names      │ Surname              │
│ Sarah            │ Martin               │
└──────────────────┴──────────────────────┘
│ [📷 Try scanning again]  (outlined btn) │
└─────────────────────────────────────────┘
```

- Labels: `caption` (12px, medium weight, muted color).
- Values: `subtitle2` (14px, medium weight).
- 2-column grid layout with equal columns.
- Country field includes a flag emoji/icon before the value.
- "Try scanning again" button at the bottom (outlined, full-width, with camera icon).

## §8. Selectable Cards (Refund Method)

Payment method options live inside **one shared white `Paper` card**. The heading "Select your refund method" sits at the top of that card, followed by a 2×2 `Grid` of inner option rows. Options are **never individual floating cards** — they are bordered rows inside the container.

Heading rule (mandatory): use **24px** title size with **medium weight (500)** and negative letter-spacing (`-1px`).

Each option row:
- **Plain icon** — no rounded-square background wrapper. Just the `MaterialSymbol` inline
- Label only (`subtitle2`, medium weight) — no description subtitle text beneath it
- `Radio` on the far right: outlined grey when unselected, filled black dot when selected
- `minHeight: 56px`, `borderRadius: 8px`, `border: 1px solid divider`
- **Selected state**: `border: 1.5px solid #18181b` — the dark border is the selection indicator

```tsx
<Paper elevation={5} sx={{ borderRadius: '12px', p: 2 }}>
  <Typography sx={{ fontSize: 24, fontWeight: 500, letterSpacing: '-1px', lineHeight: 1.4, mb: 2 }}>
    Select your refund method
  </Typography>
  <Grid container spacing={2}>
    {methods.map((m) => (
      <Grid item xs={6} key={m.id}>
        <ButtonBase
          onClick={() => setMethod(m.id)}
          sx={{
            width: '100%', minHeight: 56,
            px: 2, gap: 1.75,
            display: 'flex', alignItems: 'center',
            borderRadius: '8px',
            border: selected === m.id ? '1.5px solid #18181b' : '1px solid',
            borderColor: selected === m.id ? '#18181b' : 'divider',
            bgcolor: 'background.paper',
          }}
        >
          <MaterialSymbol icon={m.icon} sx={{ fontSize: 24 }} />
          <Typography variant="subtitle2" sx={{ flex: 1 }}>{m.label}</Typography>
          <Radio checked={selected === m.id} size="small" />
        </ButtonBase>
      </Grid>
    ))}
  </Grid>
</Paper>
```

**Conditional instruction card** — a **separate `Paper`** below the selection card, only shown for methods that need guidance. Large `28px` heading, `body2` subtitle, card-reader illustration on the right.

Mandatory behavior: when `Card` is the selected option, this instruction card must always be rendered (initial state and every time the user switches back to `Card`).

| Method | Show instruction card? | Content |
|---|---|---|
| Card | ✅ Yes | "Tap, swipe or insert your card on the card reader" + terminal illustration |
| Alipay | ✅ Yes | "Open Alipay and scan the QR code shown at the desk" + QR illustration |
| Cash | ❌ No | — |
| Cash at Airport | ❌ No | — |

## §9. Camera Overlay

For barcode and ID scanning, a full-screen camera overlay:

- **Background**: Full-screen dark semi-transparent overlay (`rgba(0,0,0,0.6)`).
- **Viewport**: A transparent rounded rectangle in the center where the camera feed shows through. Dimensions vary:
  - Barcode scan: wider, shorter rectangle.
  - ID scan: 928×598px rounded rectangle.
- **Instruction text**: White text above the viewport ("Hold your passport or national ID still inside the frame").
- **Cancel link**: White text link below the viewport ("Cancel").
- Implementation: CSS `clip-path` or `mix-blend-mode` to create the transparent viewport effect. Alternatively, use a `<div>` with a large `box-shadow` covering the screen.

## §10. PageHead

The top bar for all wizard steps:

```tsx
<Box sx={{
  height: 57,
  width: '100%',
  display: 'flex',
  alignItems: 'center',
  px: 2,
  gap: 1,
  borderBottom: 1,
  borderColor: 'divider',
}}>
  <PlanetLogoSmall sx={{ width: 24 }} />
  <Typography variant="h6">Tax Free form</Typography>
</Box>
```

- Fixed height 57px.
- Always use the provided Planet SVG asset `assets/planet-p-icon-black.svg` for top-of-screen PageHead branding.
- Planet logo (small) + "Tax Free form" text.
- Bottom border separator.
- No navigation actions — this is a kiosk, not a portal.

## §11. Splash Screen (Home)

The splash screen is different from wizard steps — it has NO PageHead, NO Footer, NO Stepper:

- Full-screen centered layout.
- Merchant logo at the top.
- Large `h1` heading: "Get your Tax Free refund with Planet".
- Language selection card (`Paper elevation={5}`): grid of language buttons (outlined, 5 columns × 2 rows).
- Soft consent text at the bottom: "By continuing, I agree with the Terms and conditions and Privacy policy."
- Planet logo (small) at the very bottom.
- Background: `surface.surface`.

## §12. Imports

- Import components from `@weareplanet/solar-ds-react` (same as Solar DS).
- If a component is not available in Solar DS, import directly from `@mui/material`.
- Icons: Use `MaterialSymbol` for UI icons, or MUI icons as fallback.
- This product uses the **same Solar DS package** as Payments, but with product-specific patterns.

## §13. Accessibility

- All interactive elements need appropriate `aria-label` attributes (especially icon-only buttons).
- The kiosk is touch-operated — ensure touch targets are at least 48×48px.
- Language selection buttons must have `lang` attribute for each language.
- Camera scanning screens must provide a clear Cancel action accessible without completing the scan.

## §14. Internationalization

- The app supports 10+ languages (English, Arabic, Chinese, Spanish, French, Korean, Japanese, Portuguese, Turkish, Hindi).
- All user-facing text must be externalizable (use i18n keys, not hardcoded strings).
- RTL support required for Arabic — layout must flip appropriately.
- Language selection happens at the splash screen and persists throughout the flow.

## §15. Spacing (Solar DS Mandatory)

- Always use Solar DS/MUI spacing tokens. Prefer `theme.spacing(...)` and shorthand `sx` spacing props (`p`, `px`, `py`, `m`, `gap`) over raw pixel literals.
- Use an 8px base rhythm (e.g., 4, 8, 12, 16, 24, 32, 48) and keep vertical rhythm consistent across sections.
- Keep component internal spacing predictable:
  - Card padding: use tokenized spacing (commonly 24px equivalent).
  - Section gaps between cards: use tokenized spacing (commonly 16–24px equivalent).
  - Inline control gaps (icon + label, button groups): use tokenized spacing (commonly 8–16px equivalent).
- Avoid one-off values unless the design source explicitly requires them.
- Never mix multiple near-identical spacing values in the same context (for example 14px, 15px, 16px). Normalize to the nearest spacing token.

## §16. UX Best Practices (Mandatory)

- Preserve clear action hierarchy:
  - Primary action = contained button, visually dominant.
  - Secondary actions = outlined/text.
  - Destructive actions require clear labeling and visual caution.
- Design for touch first:
  - Minimum touch targets: 48×48px.
  - Keep sufficient spacing between interactive elements to reduce accidental taps.
- Keep cognitive load low:
  - One primary decision per section.
  - Use progressive disclosure (collapsed receipts, contextual instructions).
  - Surface key totals/status near the action area (for example estimated refund near Continue).
- Ensure state clarity:
  - Every interactive control should expose clear default, hover/focus (if applicable), active, disabled, and error states.
  - Loading and submission states must prevent duplicate actions.
- Support error prevention and recovery:
  - Validate early with actionable messages.
  - Provide explicit recovery actions (Rescan, Edit, Retry, Cancel).
  - Do not trap users in camera flows; always provide a visible Cancel/Back escape.
- Keep accessibility and readability first:
  - Maintain strong contrast for text and controls.
  - Keep body content concise and scannable.
  - Use plain language and avoid ambiguous labels.

## §17. Mobile Touch Targets (Mandatory)

- For mobile layouts, all interactive controls must be at least 44×44px.
- This includes: primary/secondary buttons, filter chips, icon buttons, dropdown/select controls, and quantity selectors.
- Use tokenized sizing in `sx` props and component APIs to guarantee minimum height/width (for example `minHeight: 44`, `minWidth: 44`).
- If a visual design appears smaller, keep the visible control style but increase the clickable hit area to meet 44×44px.
