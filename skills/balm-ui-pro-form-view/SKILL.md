---
name: balm-ui-pro-form-view
description: Guide for building standard create/edit form pages using BalmUI Pro's ui-form-view component and JSON configuration arrays.
---

# BalmUI Pro Form View Skill

This skill provides the workflow for creating highly configurable form views in a BalmUI Pro based admin system.

## Core Philosophy
In BalmUI Pro, forms are built by passing an array of configurations to `<ui-form-view>` rather than writing `<ui-textfield>`, `<ui-select>` HTML tags over and over.

## 🔍 Discovery & Investigation Protocol (CRITICAL)

Before building a form view:
- **Discover Complex Components**: Use `list_dir` to check `app/scripts/form-components/` to see if there are pre-existing complex form components (like image uploaders or custom pickers) that you should use instead of building from scratch.

## Step-by-Step Workflow

### 1. Template Setup
Always wrap your forms in `<ui-form-view>`. 

```vue
<template>
  <ui-form-view
    v-model="formData"
    :model-config="formConfig"
    :action-config="actionConfig"
    @action="onAction">
    
    <!-- Use Slots for complex UI only -->
    <template #customAvatar="{ formData }">
      <my-custom-uploader v-model="formData.avatar" />
    </template>
    
  </ui-form-view>
</template>
```

### 2. The `formConfig` Array
Define the fields in your script setup.
```javascript
data() {
  return {
    formData: {
      name: '',
      type: 1,
      avatar: ''
    },
    formConfig: [
      {
        key: 'name',
        label: 'User Name',
        component: 'ui-textfield',
        required: true,
        validator: 'required'
      },
      {
        key: 'type',
        label: 'Account Type',
        component: 'ui-select',
        attrOrProp: {
          options: this.$constant.use('accountTypes')
        }
      },
      {
        key: 'avatar',
        label: 'Avatar',
        slot: 'customAvatar' // Delegates rendering to the <template #customAvatar>
      }
    ],
    actionConfig: [
      { text: 'Submit', action: 'submit', type: 'raised' },
      { text: 'Cancel', action: 'cancel' }
    ]
  }
}
```

### 3. Action Handling
Capture button clicks with the `@action` event. It will automatically run validations if set up.
```javascript
methods: {
  onAction(action, { valid }) {
    if (action === 'submit') {
      if (valid) {
        // Validation passed, proceed with this.$apiModel.save(this.formData)
      } else {
        this.$toast('Please fill in required fields');
      }
    } else if (action === 'cancel') {
      this.$router.back();
    }
  }
}
```

## Anti-Patterns
- ❌ **Anti-pattern**: Writing out 20 `<ui-textfield>` tags in the template for a standard form.
- ❌ **Anti-pattern**: Creating custom submit buttons outside the `<ui-form-view>` and manually trying to trigger form validation.
