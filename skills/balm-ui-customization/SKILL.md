---
name: balm-ui-customization
description: Comprehensive guide and protocols for customizing BalmUI styles using Sass variables and minimal CSS overrides, including discovery methods for component variables.
---

# BalmUI Customization Skill

This skill equips you with the knowledge and workflow to rapidly customize BalmUI (which is built on top of Material Design Components Web) into various distinct visual styles (e.g., modern, dark mode, minimalist) using proper variable overrides.

## Core Philosophy

BalmUI customization heavily relies on overriding Sass variables *before* the MDC or BalmUI components are imported. This is done using Sass `@use ... with` blocks. 
**Never write pure CSS overrides (`!important`) unless absolutely necessary.** Always check if there is a variable for it first.

## 🔍 Discovery & Investigation Protocol (CRITICAL)

As an AI, you might not know every exact Sass variable for every single BalmUI or MDC component. **Do not guess variables.** If you need to customize a component and don't know the variables:

1. **Search Material Components (`@material`)**: BalmUI heavily uses MDC. If you need to customize a component (e.g., button, dialog, textfield), you must search inside the `node_modules/@material/<component-name>` directory of the user's project.
   - **How to find variables**: Use the `list_dir` tool on `node_modules/@material/<component-name>` to look for files like `_variables.scss`, `_theme.scss`, or `_custom-properties.scss`.
   - Use `view_file` to read those files. Any variable defined without an underscore prefix (e.g., `$shape-radius`, `$height`) can be overridden using `@use ... with`.
2. **Search BalmUI Source**: If the component is specific to BalmUI (e.g., `alert`, `tree`), search inside `node_modules/balm-ui/src/styles/` or the local project's `balm-ui` source directory. Look for the component's SCSS files.
3. **Use the Reference**: Check the `references/balm-ui-variables-reference.md` included in this skill for a cheat sheet of the most common component module paths.

## Customization Workflow

Whenever you are tasked with styling or creating a UI using BalmUI:

1. **Identify the Theme Variables**: Determine the primary color, secondary color, background, and surface colors based on the design request.
2. **Setup the Entry SCSS File**: In your main SCSS file (e.g., `app.scss`), setup the `@use` blocks for variables first.
3. **Import BalmUI**: Only after defining the variables, import the main BalmUI styles or individual component styles.

## Syntax & Examples

### 1. Theme Colors
Customize global colors using the `theme-color` module.
```scss
@use '@material/theme/theme-color' with (
  $primary: #ff5722,
  $secondary: #03a9f4,
  $background: #121212,
  $surface: #1e1e1e
);
```

### 2. Typography
Customize fonts and header styles using the `typography` module.
```scss
@use '@material/typography/typography' with (
  $font-family: string.unquote('Inter, sans-serif'),
  $styles-button: (
    font-size: 14px,
    font-weight: 600,
    text-transform: none
  )
);
```

### 3. Component Specifics
Most components have a theme module where you can configure sizes and shapes.

**Buttons:**
```scss
@use '@material/button/button-shared-theme' with (
  $height: 48px, // Larger touch target
  $shape-radius: 24px // Pill-shaped buttons
);
```

**Shapes:**
```scss
@use '@material/shape/shape' with (
  $small-component-radius: 8px,
  $medium-component-radius: 12px,
  $large-component-radius: 16px
);
```

## Fallback: Minor Style Overrides

If a specific visual design cannot be achieved through variables (e.g., complex gradients, specific layout tweaks), use standard CSS overrides.
- Use high-specificity selectors to avoid `!important` (e.g., `.my-custom-wrapper .mdc-button { background: linear-gradient(...); }`).
- BalmUI components render with `.mdc-` prefixes (e.g., `.mdc-card`, `.mdc-button`, `.mdc-text-field`).
