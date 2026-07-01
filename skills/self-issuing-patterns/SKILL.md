---
name: self-issuing-patterns
description: "Self Service Issuing (Tax Free) UI patterns and component reference for iPad kiosk applications. Use for: splash/language selection, multi-step wizard flows, receipt scanning, product list editing, personal details confirmation, refund method selection, camera/ID scanning, stepper navigation, card-based form layouts. Designed for iPad landscape (1194×834) with future portrait support."
---

# Self Service Issuing — UI Patterns

This skill provides **production-ready UI patterns**, a **component reference**, **coding conventions**, and **screen templates** for building the Tax Free Self Service Issuing kiosk app.

## When to use this skill

- Building a new screen or flow step in the Self Issuing iPad app
- Looking up the correct layout structure for a wizard step
- Need a code template for a common UI pattern (receipt list, personal details card, refund selection)
- Checking component usage rules specific to this product
- Need to understand the stepper navigation and footer patterns
- Implementing camera-based scanning flows (receipts, ID documents)

## Available references

| File | Contents |
|------|----------|
| [references/conventions.md](references/conventions.md) | Coding conventions: imports, styling, layout rules, iPad-specific constraints, typography, elevation tokens |
| [references/components.md](references/components.md) | Component inventory with props, usage context, and product-specific patterns |
| [references/patterns-screens.md](references/patterns-screens.md) | Full screen templates: Splash, Receipt Step, Personal Details Step, Refund Method Step, Camera Scan |

## Quick rules

1. **Target device**: iPad in landscape mode (1194×834 viewport). No sidebar — full-screen kiosk app.
2. **Layout shell**: Every step screen = `PageHead` (top bar) + centered content area (max-width 800px) + sticky `Footer` (Cancel / Back / Next buttons).
3. **Stepper**: 4-step horizontal `Stepper` at the top of the content area: Receipts → Profile → Refund → Form.
4. **Content cards**: Use `Paper` with `elevation={5}` (CornerLightning/5 shadow) and `borderRadius: 3` for content sections.
5. **Buttons**: Size `"large"` (XL in Figma). Primary actions are `variant="contained"`, secondary are `variant="outlined"`, destructive/cancel are `variant="text"`.
6. **Typography**: Title font uses negative letter-spacing. `h1` = 58px/-3.5, `h5` = 24px/-1, `h6` = 17px/-1. Body font is regular weight.
7. **Background**: Page background is `surface.surface` (#f4f4f5 light mode). Cards float on this surface.
8. **Footer pattern**: Fixed bottom bar with `Cancel` (outlined, left) and `Back` (text) + `Next` (contained, right). Footer height = 104px.
9. **No sidebar**: This is NOT a portal/dashboard app. No `<Sidebar>` component. The PageHead shows the Planet logo + "Tax Free form" title.
10. **Camera flows**: Full-screen dark overlay with a transparent viewport rectangle for barcode/ID scanning. Cancel link centered below.
11. **Spacing system**: Always use Solar DS spacing tokens (`theme.spacing(...)` / `sx` spacing scale). Do not hardcode ad-hoc pixel gaps for paddings/margins.
12. **UX best practices**: Prioritize clarity, touch ergonomics, progressive disclosure, strong contrast, explicit error/recovery states, and consistent action hierarchy across all steps.
13. **Mobile touch targets**: On mobile screens, all touchable controls (buttons, chips, dropdowns/selects, quantity selectors, icon buttons) must be at least **44×44px**.

## Screen inventory

| Screen | Flow | Description |
|--------|------|-------------|
| **SSO Login** | Staff Auth | Staff login with username/password + SSO option. Planet logo + hero image. Not part of the customer-facing kiosk flow. |
| **Splash** | Home | Language selection + soft consent (T&Cs link). Merchant logo + "Get your Tax Free refund with Planet" heading. |
| **Step 1: Receipts (empty)** | Add Receipts | "Scan the barcode of the receipt" + Start scanning CTA + receipt illustration. |
| **Step 1: Camera scan** | Add Receipts | Full-screen camera with barcode scanning viewport. |
| **Step 1: Receipts (with items)** | Add Receipts | Collapsible receipt cards with product list, category filter chips (All/Fashion/Food/Not eligible), quantity, delete buttons. Shows "Total estimated refund" summary. |
| **Step 2: Personal Details** | Fill Personal Details | Confirm personal details card (read-only fields from ID scan) + Country of residence select + Email input. |
| **Step 2: ID Camera** | Fill Personal Details | Full-screen camera with ID/passport scanning viewport. |
| **Step 3: Refund Method** | Refund Method | Card vs Cash radio selection (styled as selectable cards) + contextual instruction card. |
| **Step 4: Form** | Final | (Not yet designed — form generation/preview step) |

## Live prototypes

- Staff SSO login: https://tax-free-ssi-prototype-sso.vercel.app/
- Receipts flow with filters: https://tax-free-ssi-receipts.vercel.app/self-issuing/purchases-filter

## Template dependency matrix

| Screen Template | Required References (load ALL before generating) |
|----------------|--------------------------------------------------|
| **Splash** | `conventions.md` (§1 Layout, §2 Typography, §3 Elevation) · `components.md` (LanguageGrid, SoftConsent) · `patterns-screens.md` (Splash) |
| **Wizard Step** | `conventions.md` (§1 Layout, §4 Footer, §5 Stepper) · `components.md` (PageHead, Footer, Stepper) · `patterns-screens.md` (Step layout) |
| **Receipt List** | `conventions.md` (§6 Product List) · `components.md` (ReceiptCard, ProductRow, CategoryTabs) · `patterns-screens.md` (Receipts with items) |
| **Personal Details** | `conventions.md` (§7 Read-only fields) · `components.md` (PersonalDetailsCard, CountrySelect, EmailInput) · `patterns-screens.md` (Personal Details) |
| **Refund Method** | `conventions.md` (§8 Selectable cards) · `components.md` (RefundMethodCard, InstructionCard) · `patterns-screens.md` (Refund Method) |
| **Camera Scan** | `conventions.md` (§9 Camera overlay) · `patterns-screens.md` (Camera Scan) |
