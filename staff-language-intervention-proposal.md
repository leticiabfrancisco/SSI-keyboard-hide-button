# Staff Language Intervention Proposal

## Problem

When the shopper is in a non-English language and needs help, staff may not understand the current UI or error state quickly enough to intervene.

Constraint: no persistent language button can be added to the top bar.

## Recommended Pattern

Use the **error state itself** as the staff handoff point.

On screens where the flow is blocked or likely to require assistance, add a small English-only helper line beneath the main action:

> Staff assisting? Press and hold to switch this session to English.

### Example on the receipt scan error screen

- Keep the shopper-facing error banner and primary CTA in the shopper language.
- Add a secondary helper row in English below the main scan button.
- Trigger the language switch with a **2-second press and hold** on the existing primary button.
- After the long press, show a confirmation sheet:
  - Title: `Switch session to English?`
  - Body: `Use English temporarily so staff can continue helping at the kiosk.`
  - Actions: `Switch to English` / `Keep current language`

## Why This Is Stronger Than A Hidden Button

- No new persistent chrome is added.
- Staff gets an explicit English cue exactly when they approach a blocked screen.
- The shopper's main action stays dominant.
- Long press reduces accidental language changes.
- The pattern can be reused only on high-friction states, instead of appearing everywhere.

## Behaviour Rules

1. The helper text appears only on blocked/error/help-needed states.
2. The switch is **session-scoped**, not global.
3. After completion, cancellation, or 60 seconds of inactivity, the kiosk returns to the shopper default language.
4. If the shopper restarts the flow, language resets to the original selection.

## Suggested Microcopy

### Shopper-facing

- Error banner: current language only
- Primary CTA: current language only

### Staff-facing

- Helper line: `Staff assisting? Press and hold to switch this session to English.`
- Confirmation title: `Switch session to English?`
- Confirmation body: `Use English temporarily so staff can continue helping at the kiosk.`

## Alternative Triggers

If long-pressing the primary CTA is too risky or conflicts with the scan action, use one of these instead:

1. Long-press the red error banner for 2 seconds.
2. Long-press the `Cancel` button for 2 seconds.
3. Tap the warning icon 5 times to reveal the confirmation sheet.

The preferred option is the primary CTA long press because it is the closest affordance to the task staff is trying to resume.