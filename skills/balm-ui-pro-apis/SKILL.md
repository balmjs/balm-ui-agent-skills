---
name: balm-ui-pro-apis
description: Guide for utilizing the $apiModel plugin to define and interact with backend APIs, strictly decoupling HTTP requests from UI components.
---

# BalmUI Pro API Model Skill

This skill enforces the use of the centralized `$apiModel` pattern to handle HTTP requests gracefully.

## Core Philosophy
Never scatter `axios.get` or `fetch` calls randomly across Vue components. All APIs must be registered via the `$apiModel` plugin config. This ensures components (especially `ui-list-view`) can be purely data-driven.

## 🔍 Discovery & Investigation Protocol (CRITICAL)

Before writing any data fetching logic:
1. **Discover API Models**: Read the files inside `app/scripts/apis/content/` to see how APIs are currently registered.

## Step-by-Step Workflow

### 1. Registering the API Model
Create or edit a module in `app/scripts/apis/content/`.

```javascript
// app/scripts/apis/content/article.js
export default [
  // [modelName, basePath, allowedMethods, configObj]
  ['article', '/api/v1/articles', ['read', 'write'], {
    crud: {
      read: { 
        list: '',      // maps to GET /api/v1/articles
        detail: '/:id' // maps to GET /api/v1/articles/:id
      },
      write: {
        create: '',    // maps to POST /api/v1/articles
        update: '/:id' // maps to PUT /api/v1/articles/:id
      }
    }
  }]
];
```

### 2. Using API Models in Components
Once registered, the `$apiModel` plugin injects these into the Vue prototype. You access them via `this.$apiModel.<modelName>`.

```javascript
// In a Vue component method
async fetchArticleDetails(id) {
  try {
    // Calling the 'detail' method defined in the config above
    const res = await this.$apiModel.article.detail({ id });
    this.articleData = res.data;
  } catch (error) {
    this.$toast('Failed to load article');
  }
}

async submitArticle(payload) {
  try {
    // Calling the 'create' method
    await this.$apiModel.article.create(payload);
    this.$toast('Saved!');
  } catch (error) {
    // Error handling
  }
}
```

### 3. Binding to High-Level Components
The real power is passing the string name to components.
```html
<!-- The component automatically resolves this.$apiModel.article.list() -->
<ui-list-view model="article" ... />
```

## Anti-Patterns
- ❌ **Anti-pattern**: `import axios from 'axios'` inside `pages/Dashboard.vue`.
- ❌ **Anti-pattern**: Hardcoding the full URL path `/api/v1/articles/123` inside a component method.
