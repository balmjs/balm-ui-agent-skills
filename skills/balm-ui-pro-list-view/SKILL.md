---
name: balm-ui-pro-list-view
description: Guide for building data-driven list pages using BalmUI Pro's ui-list-view component, relying on models and configuration rather than raw HTML/CSS.
---

# BalmUI Pro List View Skill

This skill equips you with the exact workflow to build complex list/table pages in BalmUI Pro (or a similarly configured admin framework).

## Core Philosophy
In BalmUI Pro, list views are **Data & Config Driven**. You should almost never write a raw `<table>`, `pagination`, or `search form`. The high-level component `<ui-list-view>` handles API mapping, searching, pagination, and rendering simultaneously.

## 🔍 Discovery & Investigation Protocol (CRITICAL)

Before building a list view:
1. **Verify Component Schema**: Always check the source code in `node_modules/balm-ui/src` or `node_modules/balm-ui-pro/src` if you are unsure about a component's props or configuration schema. If referencing existing project files, verify the actual implementations in the workspace instead of hallucinating options.
2. **Discover APIs**: Check `app/scripts/apis/content/` to ensure the API model you intend to use (e.g., `user`) is registered. The `<ui-list-view>` relies on this registration to fire HTTP requests.
3. **Discover Form Configs**: Check `app/scripts/pages/` or `app/scripts/form-config/` to see if a search form config array exists for the `model-path` property.

## Step-by-Step Workflow

### 1. The Template Setup
Always use `<ui-list-view>`. 

```vue
<template>
  <ui-list-view
    model="audio"
    model-path="project/model-config/audio-list"
    :thead="thead"
    :tbody="tbody"
    :row-action-config="rowActionConfig"
    :top-action-config="topActionConfig">
    <template #list-view-title>
      {{ $store.currentMenuName }}列表
    </template>
  </ui-list-view>
</template>
```

### 2. Properties Breakdown
- **`model`**: The string identifier corresponding to the API module registered in `app/scripts/apis/content/*.js`. If the model is `audio`, the component will auto-fetch `/api/audio/list`.
- **`model-path`**: Path to the search form configuration (usually an array defining inputs like `ui-textfield`).
- **`thead`**: An array of strings for table headers. E.g., `['ID', 'Name', 'Status', 'Actions']`
- **`tbody`**: An array of strings or objects mapping to data fields.

### 3. Formatting Data (The `tbody` trick)
If you need to translate an enum (e.g. `status: 1` to `Active`), **DO NOT** use slot templates. Instead, use the `fn` property within the `tbody` definition inside the script section:

```javascript
// Good Practice
data() {
  return {
    tbody: [
      'id',
      'name',
      {
        field: 'status',
        fn: ({ status }) => this.$constant.useMap('statusMap')[status] || status
      }
    ]
  };
}
```

### 4. Actions Configuration
Define table row actions and top actions using configuration objects:
```javascript
data() {
  return {
    rowActionConfig: [
      { text: 'Edit', type: 'router-link', routeName: 'audio.edit' },
      { text: 'Delete', handler: (row) => this.deleteRow(row), show: (row) => row.status === 1 }
    ],
    topActionConfig: [
      { text: 'Create New', type: 'router-link', routeName: 'audio.create', icon: 'add' }
    ]
  };
}
```

## Anti-Patterns & Verification
- ❌ **Anti-pattern**: Writing `<table class="mdc-data-table">` manually for a standard backend list page.
- ❌ **Anti-pattern**: Manually doing `axios.get('/api/xxx/list')` inside `mounted()` instead of letting `ui-list-view` handle it via the `model` prop.
- ❌ **Anti-pattern**: Guessing form or action configuration properties. If you don't know the exact schema, verify it by reading the actual source code or `form-config/` JSON files in the workspace.
