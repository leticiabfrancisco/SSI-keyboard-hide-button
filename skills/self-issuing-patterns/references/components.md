# Self Service Issuing — Component Reference

All components are imported from `@weareplanet/solar-ds-react` unless noted otherwise.

## Layout

| Component | Summary | Recommended Props | Notes |
|-----------|---------|-------------------|-------|
| `Box` | Generic container | `sx`, `component` | Use for layout wrappers |
| `Container` | NOT USED | — | Kiosk app uses fixed 1194px viewport, not responsive containers |
| `Stack` | Flex container | `direction`, `spacing`, `alignItems` | Primary layout tool |
| `Paper` | Elevated surface card | `elevation={5}`, `sx={{ borderRadius: 3 }}` | Content cards |

## Navigation

### Stepper

The canonical progress indicator for the 4-step Tax Free wizard. **Do NOT use the MUI `<Stepper>` / `<StepLabel>` defaults** — build it with `Box` + `Typography` per the spec below.

#### Anatomy

```
● 1  Receipts  –  ● 2  Profile  –  ● 3  Refund  –  ● 4  Form
▲                  ▲                  ▲                  ▲
solid black       solid grey         solid black        solid grey
(done / active)   (future)           (active)           (future)
white ✓ or num    white num          white num          white num
bold label        grey label         bold label         grey label
```

#### Circle states

| State | Circle background | Text inside | Label colour | Label weight |
|-------|-------------------|-------------|--------------|--------------|
| **Done** (steps before active) | `#18181b` (black) | `<img src="assets/stepper-check.svg" />` (white check SVG) | `text.secondary` | 500 |
| **Active** (current step) | `#18181b` (black) | white number | `text.primary` | 700 |
| **Future** (steps after active) | `#a1a1aa` (solid grey) | white number | `text.secondary` | 500 |

> **Rule**: future steps are ALWAYS a solid grey-filled circle with a white number. Never use an outlined or transparent circle.

#### Connector

A plain `–` dash character (`&ndash;`) between steps. Never a `<Divider>`, `<hr>`, or CSS line.

#### Layout

- All steps and dashes on **one horizontal row** — label sits to the **right** of the circle, never below.
- Centered horizontally, total width ~384px.
- Sits 32px below the PageHead divider and 24px above the first content card.

#### Canonical TSX

```tsx
const STEPS = [
  { label: 'Receipts' },
  { label: 'Profile'  },
  { label: 'Refund'   },
  { label: 'Form'     },
];

export const WizardStepper = ({ activeStep }: { activeStep: number }) => (
  <Box sx={{ display: 'flex', alignItems: 'center', gap: 1, width: 384, mx: 'auto' }}>
    {STEPS.map((step, i) => {
      const isDone   = activeStep > i;
      const isActive = activeStep === i;
      return (
        <React.Fragment key={step.label}>
          {/* Step: circle + label inline */}
          <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.75, flexShrink: 0 }}>
            {/* Circle — solid black (done/active) or solid grey (future) */}
            <Box sx={{
              width: 28, height: 28, borderRadius: '50%',
              bgcolor: isDone || isActive ? '#18181b' : '#a1a1aa',
              color: '#fff',
              display: 'flex', alignItems: 'center', justifyContent: 'center',
              fontSize: 13, fontWeight: 600, flexShrink: 0,
            }}>
              {isDone ? (
                <img src="assets/stepper-check.svg" width={14} height={14} alt="" />
              ) : i + 1}
            </Box>
            {/* Label */}
            <Typography variant="caption" sx={{
              fontWeight: isActive ? 700 : 500,
              color: isActive ? 'text.primary' : 'text.secondary',
              whiteSpace: 'nowrap',
            }}>
              {step.label}
            </Typography>
          </Box>
          {/* Dash connector — character, never a line */}
          {i < STEPS.length - 1 && (
            <Typography variant="caption" sx={{ color: 'text.secondary', flexShrink: 0 }} aria-hidden>
              –
            </Typography>
          )}
        </React.Fragment>
      );
    })}
  </Box>
);
```

## Page Chrome

### PageHead
Top bar showing Planet branding. Fixed height 57px, full width.

```tsx
<Box sx={{ height: 57, width: '100%', display: 'flex', alignItems: 'center', px: 2, gap: 1, borderBottom: 1, borderColor: 'divider' }}>
  <PlanetLogoSmall sx={{ width: 24 }} />
  <Typography variant="h6">Tax Free form</Typography>
</Box>
```

### Footer
Sticky bottom navigation bar. Always 104px tall.

Props pattern:
- `showBack`: boolean — show/hide Back button (hidden on step 1)
- `onCancel`: callback
- `onBack`: callback
- `onNext`: callback
- `nextLabel`: string (default "Next", can be "Submit" on last step)
- `nextDisabled`: boolean

## Inputs & Forms

| Component | Summary | Recommended Props | Notes |
|-----------|---------|-------------------|-------|
| `TextField` | Text input | `size="medium"`, `label`, `fullWidth` | This product uses `size="medium"` (NOT small — larger touch targets for kiosk) |
| `Select` | Dropdown select | `size="medium"`, `sx={{ width: 123 }}` | Used for quantity selects in product rows |
| `Autocomplete` | Country select | `size="medium"`, with flag icons | Used for "Country of residence" |
| `Radio` | Radio button | `color="primary"`, `size="medium"` | Used inside SelectableCard pattern |
| `Button` | Action button | `size="large"`, `variant` | ALL buttons are size="large" (XL in Figma) |
| `IconButton` | Icon-only button | `size="medium"` | Used for delete actions in product rows |

**IMPORTANT — `size="medium"` for all form inputs**: Unlike the Payments portal (which uses `size="small"`), this kiosk app uses `size="medium"` for all inputs. The iPad touch interface requires larger tap targets.

## Data Display

| Component | Summary | Recommended Props | Notes |
|-----------|---------|-------------------|-------|
| `Typography` | Text element | `variant`, `sx` | See conventions §2 for type scale |
| `Chip` | Category tab/filter | `label`, `variant="outlined"`, `onClick` | Used in receipt category tabs |
| `Alert` | Info banner | `severity="info"` | Used to show eligibility warnings in receipt step |
| `Divider` | Separator line | — | Between product rows |

## Product-Specific Patterns

### LanguageGrid
Grid of language selection buttons on the splash screen.

```tsx
<Box sx={{ display: 'grid', gridTemplateColumns: 'repeat(5, 1fr)', gap: 2 }}>
  {languages.map(lang => (
    <Button
      key={lang.code}
      variant="outlined"
      size="large"
      onClick={() => selectLanguage(lang.code)}
      lang={lang.code}
      sx={{ borderRadius: 2, height: 56, borderColor: 'outline.outlinelow' }}
    >
      {lang.name}
    </Button>
  ))}
</Box>
```

### SelectableCard
Radio selection presented as a bordered card (used in Refund Method step).

```tsx
interface SelectableCardProps {
  selected: boolean;
  icon: ReactNode;
  label: string;
  onClick: () => void;
}

const SelectableCard = ({ selected, icon, label, onClick }: SelectableCardProps) => (
  <Paper
    onClick={onClick}
    sx={{
      flex: 1,
      display: 'flex',
      alignItems: 'center',
      px: 3,
      py: 2,
      cursor: 'pointer',
      border: 2,
      borderColor: selected ? 'primary.main' : 'outline.outlinelow',
      borderRadius: 2,
      gap: 2,
    }}
  >
    {icon}
    <Typography variant="body1" sx={{ flex: 1 }}>{label}</Typography>
    <Radio checked={selected} color="primary" size="medium" />
  </Paper>
);
```

### ReceiptCard
Collapsible card showing a scanned receipt's items.

```tsx
interface ReceiptCardProps {
  receiptNumber: number;
  items: ProductItem[];
  expanded: boolean;
  onToggle: () => void;
  onDelete: () => void;
}
```

Structure:
- Header: Chevron icon + "Receipt {N}" title + Delete button (right-aligned)
- Category tabs: Horizontally scrollable chips
- Product list: Rows with [name, quantity select, price, delete button]
- Each row is 88px tall

### PersonalDetailsCard
Read-only display of scanned ID information.

```tsx
interface PersonalDetailsCardProps {
  documentNumber: string;
  expiryDate: string;
  documentCountry: string;
  countryFlag: string;
  dateOfBirth: string;
  givenNames: string;
  surname: string;
  onRescan: () => void;
}
```

- 2-column grid layout
- Labels in `caption` variant (muted)
- Values in `subtitle2` variant
- "Try scanning again" button at the bottom

### InstructionCard
Contextual card shown below the refund method selection.

```tsx
interface InstructionCardProps {
  title: string;       // e.g. "Tap, swipe or insert your card on the card reader"
  subtitle: string;    // e.g. "Your VAT refund goes to this card once approved"
  illustration: ReactNode;  // Right-aligned illustration
}
```

- `Paper elevation={5}`, `borderRadius: 3`
- Title in `h5` variant (24px, medium weight)
- Subtitle in `body2` variant (muted)
- Illustration floats to the right

### CameraOverlay
Full-screen scanning overlay.

```tsx
interface CameraOverlayProps {
  instructionText: string;  // Text above the viewport
  onCancel: () => void;
  viewportSize: { width: number; height: number };  // Size of transparent scanning area
}
```

- Full-screen positioned (`position: fixed, inset: 0`)
- Dark overlay with transparent viewport rectangle
- White instruction text above viewport
- White "Cancel" text link below viewport
