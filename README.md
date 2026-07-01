# Self Service Issuing — Copilot Skill

A VS Code Copilot skill that teaches AI assistants how to generate screens for the **Tax Free Self Service Issuing** kiosk application.

## What this provides

- **UI patterns** for the iPad kiosk wizard flow (splash, receipts, personal details, refund method)
- **Coding conventions** specific to this product (layout rules, typography, elevation, iPad constraints)
- **Component reference** with props and usage examples
- **Screen templates** with production-ready code

## Structure

```
skills/
  self-issuing-patterns/
    SKILL.md                          ← Entry point (loaded by Copilot)
    references/
      conventions.md                  ← Coding rules, layout, typography
      components.md                   ← Component inventory
      patterns-screens.md             ← Full screen templates
```

## Usage

### Local (for development)

1. Open this folder in VS Code
2. The skill will be available to Copilot when working in this workspace
3. Reference it by describing Tax Free / Self Issuing screens

### Shared (via GitHub)

1. Push this repo to GitHub
2. Team members clone and open the workspace
3. The skill files are automatically picked up by Copilot

### As a VS Code Extension (future)

Package as a `.vsix` extension to distribute via the marketplace or internally.

## Adding new screens

1. Get the Figma URL for the new screen
2. Use Copilot with the Figma MCP tools to extract the design
3. Add the pattern to `references/patterns-screens.md`
4. Update `SKILL.md` screen inventory and dependency matrix
