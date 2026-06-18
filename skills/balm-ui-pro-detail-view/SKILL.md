---
name: balm-ui-pro-detail-view
description: Guide for building read-only detail pages using BalmUI Pro's ui-detail-view component and JSON configuration.
---

# BalmUI Pro Detail View Skill

This skill provides the workflow for creating highly configurable, read-only detail views in a BalmUI Pro based admin system.

## Core Philosophy
In BalmUI Pro, detail pages (such as user profiles or read-only order summaries) are built by passing an array of configurations to `<ui-detail-view>`. Like `<ui-form-view>`, this relies heavily on the `model-config` array to control layout and data mapping without hardcoding HTML labels and paragraphs.

## 🔍 Discovery & Investigation Protocol (CRITICAL)

Before building a detail view:
1. **Verify Component Schema**: Always check the source code in `node_modules/balm-ui/src` or `node_modules/balm-ui-pro/src` if you are unsure about a component's props or configuration schema. If referencing existing project files, verify the actual implementations in the workspace instead of hallucinating options.
2. **Discover Constants**: Ensure you check `app/scripts/constants/` because read-only detail views often require mapping integers back to human-readable strings using `$constant.useMap()`.
3. **Discover API Models**: Ensure the `model` property aligns with the `$apiModel` registry.

## Step-by-Step Workflow

### 1. Template Setup
Always use `<ui-detail-view>`.

```vue
<template>
  <ui-detail-view
    model="expert"
    :model-config="detailConfig">
    
    <!-- Use Slots for complex UI only (e.g., rendering HTML content or images) -->
    <template #customAvatar="{ formData }">
      <img :src="formData.avatar" alt="Avatar" class="detail-avatar" />
    </template>
    
  </ui-detail-view>
</template>
```

### 2. The `detailConfig` Array
Define the fields in your script setup. You can use standard text output, or delegate to slots for custom rendering.

```javascript
data() {
  return {
    detailConfig: [
      {
        key: 'name',
        label: 'User Name',
        // 'text' is the default behavior if component is omitted
      },
      {
        key: 'type',
        label: 'Account Type',
        fn: ({ type }) => this.$constant.useMap('accountTypes')[type] || 'Unknown'
      },
      {
        key: 'avatar',
        label: 'Avatar',
        slot: 'customAvatar' // Delegates rendering to the <template #customAvatar>
      }
    ]
  }
}
```

### 3. Data Fetching
Unlike `ui-form-view` which often receives a `v-model`, `ui-detail-view` typically fetches its own data based on route parameters (e.g., `id`). 
- If `model="expert"` is provided, the component automatically attempts to fetch data via `this.$apiModel.expert.detail({ id: this.$route.params.id })` (or similar configured parameters).
- If you need to pass data manually, you can often use `model="local:expert"` and pass the raw object to a corresponding prop or data hook depending on the specific BalmUI Pro version implementation.

## Anti-Patterns
- ❌ **Anti-pattern**: Manually writing `<div class="row"><label>Name</label><span>{{ data.name }}</span></div>` repeatedly for 20 fields.
- ❌ **Anti-pattern**: Attempting to use `<ui-form-view>` with all inputs set to `disabled="true"` just to show data. Use `<ui-detail-view>` instead.
- ❌ **Anti-pattern**: Guessing form configuration properties. If you don't know the exact schema, verify it by reading the actual source code or `form-config/` JSON files in the workspace.
