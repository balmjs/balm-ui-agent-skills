---
name: balm-ui-pro-module
description: Meta-skill that orchestrates all BalmUI Pro skills to scaffold a complete CRUD business module (constants, APIs, mocking, form config, list/detail views, and routing) in one pipeline. Use when asked to create a new feature module, management page, or admin section.
---

# BalmUI Pro Module Scaffolding Skill

This is a **meta-skill** that chains together the individual BalmUI Pro skills (`balm-ui-pro-apis`, `balm-ui-pro-constants`, `balm-ui-pro-mocking`, `balm-ui-pro-form-view`, `balm-ui-pro-list-view`, `balm-ui-pro-detail-view`, `balm-ui-pro-routing`) into a single, dependency-ordered pipeline.

When a developer asks you to "create a [something] management module", this skill ensures you generate **all required files in the correct order**, so no file references an import that hasn't been created yet.

## Core Philosophy

> **"Configuration over Code, Pipeline over Patchwork."**

A complete BalmUI Pro business module is not just one Vue page — it is a constellation of 6–8 files across different directories, each with strict dependency relationships. Generating them out of order causes import errors, runtime crashes, or silent failures (e.g., `ui-list-view` failing to fetch data because the API model wasn't registered).

## 🔍 Pre-flight Discovery Protocol (CRITICAL)

Before generating ANY files, you MUST investigate the existing project structure to understand its conventions.

> **⚠️ Path Warning**: Directory layouts vary between projects. Some legacy projects use sub-folders like `apis/new/` or `constants/new/` for versioning purposes; newer projects place files directly in `apis/` and `constants/`. **Never assume a fixed path — always discover it first.**

Run these discovery steps using `list_dir` and `view_file`:

```
1. Locate the APIs directory:
   - Check `app/scripts/apis/` → if it directly contains module JS files and an `index.js`, use this as {apisDir}
   - If it contains sub-folders (e.g., `new/`, `old/`), read into the active one to find {apisDir}

2. Locate the Constants directory:
   - Check `app/scripts/constants/` → same logic as above to find {constantsDir}

3. Read `{apisDir}/index.js`          → Understand how API modules are imported and merged
4. Read `{constantsDir}/index.js`     → Understand how constant modules are imported and merged
5. Read `app/scripts/routes/modules/` → Understand how route modules are organized
6. Read `mock-server/modules/index.js`→ Understand how mock APIs are registered
7. Read `mock-server/handler.js`      → Understand the standard response/error wrapper
8. Read `app/scripts/form-config/`    → Understand how search/form configs are structured (JSON or JS)
```

**You must adapt your output to match the exact patterns you discover.** Do not assume a structure — verify it.

In all examples below, `{apisDir}` and `{constantsDir}` refer to the actual paths you discovered above (e.g., `app/scripts/apis/` or `app/scripts/apis/new/`).

---

## The Pipeline: 6 Steps in Strict Order

Assume the developer asks: **"Create an `article` management module with list, detail, create/edit form."**

The placeholder `{module}` below represents the module name (e.g., `article`).

---

### Step 1: Constants Definition (`balm-ui-pro-constants`)

**File**: `{constantsDir}/{module}.js`

Define all enums, status codes, and type dictionaries that the module's list and form views will reference.

```javascript
// {constantsDir}/article.js

const articleStatus = [
  { key: 'draft', value: 0, label: '草稿' },
  { key: 'published', value: 1, label: '已发布' },
  { key: 'archived', value: 2, label: '已归档' }
];

const articleCategory = [
  { key: 'news', value: 1, label: '新闻' },
  { key: 'notice', value: 2, label: '通知' },
  { key: 'activity', value: 3, label: '活动' }
];

export default {
  articleStatus,
  articleCategory
};
```

**Then register it** in the constants index:
```javascript
// {constantsDir}/index.js — ADD import and spread
import articleConstants from './article';

export default {
  // ...existing constants...
  ...articleConstants
};
```

---

### Step 2: API Model Registration (`balm-ui-pro-apis`)

**File**: `{apisDir}/{module}.js`

Register the API endpoints. The project typically has a **global default CRUD config** defined in `{apisDir}/index.js`:
```javascript
crud: {
  create: 'add',
  read: { list: 'index', detail: 'info' },
  update: 'edit',
  delete: 'delete'
}
```
So for a standard CRUD module, you often need minimal or zero custom CRUD config — the defaults will apply automatically.

```javascript
// {apisDir}/article.js
export default [
  // Standard CRUD: uses global defaults (add/index/info/edit/delete)
  ['article', '/article', ['create', 'read', 'update', 'delete']],

  // If you need additional non-CRUD endpoints:
  // ['article-stats', '/article/stats', ['read'], {
  //   crud: { read: { overview: 'overview' } }
  // }]
];
```

**Then register it** in the APIs index:
```javascript
// {apisDir}/index.js — ADD import and spread
import articleApis from './article';

export default {
  // ...existing config...
  apis: [
    // ...existing APIs...
    ...articleApis
  ]
};
```

---

### Step 3: Mock Data (`balm-ui-pro-mocking`)

**File**: `mock-server/apis/{module}.js`

Provide CRUD mock endpoints using the project's `responseHandler` / `errorHandler` wrappers.

```javascript
// mock-server/apis/article.js
import { responseHandler, errorHandler } from '@mock-server/handler';

function generateArticleData(count = 10) {
  const statuses = [0, 1, 2];
  const categories = [1, 2, 3];
  const data = [];
  for (let i = 1; i <= count; i++) {
    data.push({
      id: i,
      title: `Article Title ${i}`,
      category: categories[i % categories.length],
      status: statuses[i % statuses.length],
      author: `Author ${i}`,
      createdAt: new Date(Date.now() - Math.random() * 30 * 24 * 60 * 60 * 1000).toISOString(),
      updatedAt: new Date().toISOString()
    });
  }
  return data;
}

const articleData = generateArticleData();

export function getArticleApis(server) {
  // POST /article/index - List with pagination
  server.post('/article/index', (schema, request) => {
    const requestData = JSON.parse(request.requestBody);
    const { page = 1, pageSize = 10, ...filters } = requestData;
    let filteredData = articleData;
    Object.keys(filters).forEach((key) => {
      if (filters[key]) {
        filteredData = filteredData.filter((item) =>
          String(item[key]).toLowerCase().includes(String(filters[key]).toLowerCase())
        );
      }
    });
    const start = (page - 1) * pageSize;
    const paginatedData = filteredData.slice(start, start + parseInt(pageSize));
    return responseHandler({ list: paginatedData, total: filteredData.length });
  });

  // POST /article/info - Get detail
  server.post('/article/info', (schema, request) => {
    const { id } = JSON.parse(request.requestBody);
    const item = articleData.find((item) => item.id == id);
    return item ? responseHandler(item) : errorHandler('Article not found');
  });

  // POST /article/add - Create
  server.post('/article/add', (schema, request) => {
    const data = JSON.parse(request.requestBody);
    const newItem = { id: articleData.length + 1, ...data, createdAt: new Date().toISOString() };
    articleData.push(newItem);
    return responseHandler(newItem);
  });

  // POST /article/edit - Update
  server.post('/article/edit', (schema, request) => {
    const data = JSON.parse(request.requestBody);
    const index = articleData.findIndex((item) => item.id == data.id);
    if (index !== -1) {
      articleData[index] = { ...articleData[index], ...data, updatedAt: new Date().toISOString() };
      return responseHandler(articleData[index]);
    }
    return errorHandler('Article not found');
  });

  // POST /article/delete - Delete
  server.post('/article/delete', (schema, request) => {
    const { id } = JSON.parse(request.requestBody);
    const index = articleData.findIndex((item) => item.id == id);
    if (index !== -1) { articleData.splice(index, 1); return responseHandler(); }
    return errorHandler('Article not found');
  });
}
```

**Then register it** in the mock modules index:
```javascript
// mock-server/modules/index.js — ADD import and call
import { getArticleApis } from '../apis/article.js';

export default function createApis(server) {
  // ...existing mocks...
  getArticleApis(server);
}
```

---

### Step 4: Search Form / Form Configurations

**Search Config File**

While simple forms can use JSON, **ANY form that requires dropdowns populated from `$constant` MUST use a JS function**. Do not use `model: "local:..."` in JSON files as it relies on complex project-specific resolvers.

Create `app/scripts/form-config/{module}-list.js`:

```javascript
// app/scripts/form-config/article-list.js
export default function getArticleSearchConfig(data, { categoryOptions = [], statusOptions = [] }) {
  return [
    {
      component: "ui-textfield",
      label: "Title",
      key: "title"
    },
    {
      component: "ui-select",
      label: "Category",
      key: "category",
      value: 0,
      model: "local:articleCategory",
      attrOrProp: {
        options: categoryOptions,
        defaultLabel: "All"
      }
    },
    {
      component: "ui-select",
      label: "Status",
      key: "status",
      value: "",
      model: "local:articleStatus",
      attrOrProp: {
        options: statusOptions,
        defaultLabel: "All"
      }
    }
  ];
}
```

Then in your `.vue` file, pass the options via the `model-options` prop to `<ui-list-view>` or `<ui-form-view>`:
```vue
<ui-list-view
  model="article"
  :model-config="getArticleSearchConfig"
  :model-options="{
    categoryOptions: $constant.use('articleCategory'),
    statusOptions: $constant.use('articleStatus')
  }">
```

**Create/Edit Form Config** (if using separate config files): `app/scripts/form-config/{module}.js`

```javascript
// app/scripts/form-config/article.js
export default function getArticleFormConfig(options = {}) {
  const { categoryOptions = [], statusOptions = [] } = options;
  return [
    { key: 'title', label: 'Title', component: 'ui-textfield', required: true, validator: 'required' },
    { key: 'category', label: 'Category', component: 'ui-select', model: 'local:articleCategory', attrOrProp: { options: categoryOptions } },
    { key: 'status', label: 'Status', component: 'ui-select', model: 'local:articleStatus', attrOrProp: { options: statusOptions } },
    { key: 'content', label: 'Content', slot: 'contentEditor' }
  ];
}
```

---

### Step 5: Vue Pages (`balm-ui-pro-list-view`, `balm-ui-pro-detail-view`, `balm-ui-pro-form-view`)

Create the pages directory: `app/scripts/pages/{module}/`

**5a. List View**: `app/scripts/pages/{module}/{module}-list.vue`

```vue
<template>
  <list-view
    model="article"
    :thead="thead"
    :tbody="tbody"
    :action-config="actionConfig"
  >
    <template #status="{ data: { status } }">
      {{ statusMap[status] || status }}
    </template>
    <template #category="{ data: { category } }">
      {{ categoryMap[category] || category }}
    </template>
  </list-view>
</template>

<script>
export default {
  name: 'article-list',
  data() {
    const statusMap = this.$constant.useMap('articleStatus', { key: 'value', value: 'label' });
    const categoryMap = this.$constant.useMap('articleCategory', { key: 'value', value: 'label' });

    return {
      statusMap,
      categoryMap,
      thead: ['ID', 'Title', 'Category', 'Status', 'Author', 'Created', 'Actions'],
      tbody: [
        'id',
        'title',
        { slot: 'category' },
        { slot: 'status' },
        'author',
        'createdAt',
        { slot: 'actions', fixed: 'right', width: 160 }
      ],
      actionConfig: [
        {
          type: 'link',
          text: 'View',
          url: ({ id }) => ({ name: 'article.detail', params: { id } })
        },
        {
          type: 'link',
          text: 'Edit',
          url: ({ id }) => ({ name: 'article.edit', params: { id } })
        }
      ]
    };
  }
};
</script>
```

**5b. Detail View**: `app/scripts/pages/{module}/{module}-detail.vue`

```vue
<template>
  <detail-view model="article" routeName="article-detail">
    <template #title>Article Detail</template>
    <template #actions>
      <ui-button outlined @click="$router.back()">Back</ui-button>
    </template>
  </detail-view>
</template>

<script>
export default {
  name: 'article-detail'
};
</script>
```

**5c. Form View** (Create/Edit): `app/scripts/pages/{module}/{module}-form.vue`

```vue
<template>
  <ui-form-view
    v-model="formData"
    :model-config="formConfig"
    :action-config="formActionConfig"
    @action="onAction"
  >
    <template #contentEditor="{ formData }">
      <!-- Custom rich text editor slot -->
      <ui-textfield v-model="formData.content" rows="6" input-type="textarea" />
    </template>
  </ui-form-view>
</template>

<script>
import getArticleFormConfig from '@/form-config/article';

export default {
  name: 'article-form',
  data() {
    return {
      formData: { title: '', category: '', status: 0, content: '' },
      formConfig: getArticleFormConfig({
        categoryOptions: this.$constant.use('articleCategory'),
        statusOptions: this.$constant.use('articleStatus')
      }),
      formActionConfig: [
        { text: 'Submit', action: 'submit', type: 'raised' },
        { text: 'Cancel', action: 'cancel' }
      ]
    };
  },
  methods: {
    async onAction(action, { valid }) {
      if (action === 'submit' && valid) {
        const id = this.$route.params.id;
        if (id) {
          await this.$apiModel.article.update({ id, ...this.formData });
        } else {
          await this.$apiModel.article.create(this.formData);
        }
        this.$toast('Saved!');
        this.$router.back();
      } else if (action === 'cancel') {
        this.$router.back();
      }
    }
  },
  async created() {
    const id = this.$route.params.id;
    if (id) {
      const res = await this.$apiModel.article.detail({ id });
      this.formData = res.data || res;
    }
  }
};
</script>
```

---

### Step 6: Route Registration (`balm-ui-pro-routing`)

**File**: `app/scripts/routes/modules/{module}.js`

Use `$routerModel.createRoutes()` (from `@yiban-shared/routes/config` or `@balm-ui-pro`) to register list, detail, and form views together.

```javascript
// app/scripts/routes/modules/article.js
import { $routerModel, indexView } from '@yiban-shared/routes/config';

const ArticleList = () => import('@/pages/article/article-list');
const ArticleDetail = () => import('@/pages/article/article-detail');
const ArticleForm = () => import('@/pages/article/article-form');

export default [
  $routerModel.createRoutes('article', {
    indexView,
    listView: ArticleList,
    detailView: [
      $routerModel.createRoute('detail/:id?', 'article.detail', ArticleDetail),
      $routerModel.createRoute('edit/:id?', 'article.edit', ArticleForm),
      $routerModel.createRoute('create', 'article.create', ArticleForm)
    ]
  })
];
```

**Then register it** in the routes model/index that aggregates all route modules.

---

## Anti-Patterns & Guardrails

### ❌ DO NOT create a Vuex/Store module
Standard CRUD modules do not need dedicated store modules. The `ui-list-view` fetches data via `$apiModel` internally. The `ui-form-view` uses local `v-model`. Only truly global state (logged-in user, theme, menu tree) belongs in the store.

### ❌ DO NOT guess configuration schemas
Always check the source code in `node_modules/balm-ui/src` or `node_modules/balm-ui-pro/src` if you are unsure about a component's props or configuration schema. If referencing existing project files, verify the actual implementations in the workspace instead of hallucinating options.

### ❌ DO NOT scatter `axios` calls in Vue pages
All HTTP interactions go through `$apiModel`. The only acceptable `axios` usage is inside the mock server setup (`miragejs-axios-adapter`).

### ❌ DO NOT hardcode enums in Vue templates
All status/type mappings belong in `{constantsDir}/{module}.js` and are accessed via `$constant.use()` / `$constant.useMap()`.

### ❌ DO NOT generate files out of order
If you write the list view first but forget to register the API model, the page will crash on load. Always follow **Steps 1→6** sequentially.

---

## Quick Reference: Files Generated Per Module

| Step | File Path | Purpose |
|------|-----------|---------|
| 1 | `{constantsDir}/{module}.js` | Enum definitions |
| 1 | `{constantsDir}/index.js` | ← Register import |
| 2 | `{apisDir}/{module}.js` | API model registration |
| 2 | `{apisDir}/index.js` | ← Register import |
| 3 | `mock-server/apis/{module}.js` | Mock data & endpoints |
| 3 | `mock-server/modules/index.js` | ← Register import |
| 4 | `form-config/{module}-list.json` | Search form config |
| 4 | `form-config/{module}.js` | Create/edit form config |
| 5 | `pages/{module}/{module}-list.vue` | List page |
| 5 | `pages/{module}/{module}-detail.vue` | Detail page |
| 5 | `pages/{module}/{module}-form.vue` | Create/Edit page |
| 6 | `routes/modules/{module}.js` | Route definitions |
| 6 | `routes/modules/index or model` | ← Register import |

> **Note**: `{apisDir}` and `{constantsDir}` are placeholders — resolve them via the Pre-flight Discovery Protocol above. Common values are `app/scripts/apis/` (standard) or `app/scripts/apis/new/` (legacy projects).
