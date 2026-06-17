---
name: balm-ui-pro-constants
description: Guide on using the centralized constant and dictionary management ($constant plugin) in BalmUI Pro projects to avoid hardcoding enum values.
---

# BalmUI Pro Constants Skill

This skill enforces the use of the centralized constants pattern found in Yiban/BalmUI Pro architectures.

## Core Philosophy
Never hardcode enum values, mapping texts, or select options inside Vue components. All constants belong in `app/scripts/constants/`.

## 🔍 Discovery & Investigation Protocol (CRITICAL)

Before defining a state mapping or select options array:
1. **Discover Constants**: Check the files in `app/scripts/constants/` (use `list_dir` and `view_file`) to see if the required dictionary already exists.

## Step-by-Step Workflow

### 1. Centralized Definition
If a new enum is required, add it to the constants index (e.g., `app/scripts/constants/index.js` or a specific domain file).

```javascript
// Example of definition in constants file
export const userStatus = [
  { label: 'Active', value: 1 },
  { label: 'Banned', value: 2 },
  { label: 'Pending', value: 3 }
];
```

### 2. Accessing Arrays for Dropdowns
When building a `ui-select` or `ui-radio` group inside a `ui-form-view` or anywhere else, use `this.$constant.use()` to get the array format.

```javascript
// Inside a Vue component
data() {
  return {
    formConfig: [
      {
        key: 'status',
        component: 'ui-select',
        attrOrProp: {
          options: this.$constant.use('userStatus') // Automatically fetches the array
        }
      }
    ]
  }
}
```

### 3. Accessing Maps for Table Displays
When displaying data in a `ui-list-view` (or any read-only view), you need to translate `1` to `Active`. Use `this.$constant.useMap()`.

```javascript
// Inside a Vue component
data() {
  return {
    tbody: [
      'username',
      {
        field: 'status',
        fn: ({ status }) => {
          // Creates a mapping object { 1: 'Active', 2: 'Banned' }
          const map = this.$constant.useMap('userStatus', { key: 'value', value: 'label' });
          return map[status] || 'Unknown';
        }
      }
    ]
  }
}
```

## Anti-Patterns
- ❌ **Anti-pattern**: `v-if="status === 1"` or `if (status === 2)` sprinkled throughout component logic without a constant reference.
- ❌ **Anti-pattern**: `const options = [{label: 'A', value: 1}]` directly in a `.vue` file.
