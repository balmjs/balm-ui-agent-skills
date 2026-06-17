---
name: balm-ui-pro-mocking
description: Guide for setting up and handling frontend mock APIs using MirageJS in BalmUI Pro architectures before real backend APIs are available.
---

# BalmUI Pro Mocking Skill

This skill explains how to seamlessly build out UI features and mock APIs without being blocked by backend development.

## Core Philosophy
In standard BalmUI Pro projects, we use MirageJS (or similar tools) intercepting XHR requests under the `mock-server/` directory.

## 🔍 Discovery & Investigation Protocol (CRITICAL)

Before writing a mock:
1. **Discover Mock Setup**: Check `mock-server/apis/` or `mock-server/index.js` to understand where the current mock endpoints are defined.
2. **Discover API Registry**: Check `app/scripts/apis/content/` to ensure the route you are mocking is officially registered in the `$apiModel` definitions.

## Step-by-Step Workflow

### 1. The Mock Definition
Define the mock intercepts inside the mock server config. 

```javascript
// mock-server/apis/user.js
export default function(server) {
  server.get('/api/users/list', (schema, request) => {
    // Read pagination params if needed from request.queryParams
    return {
      data: [
        { id: 1, name: 'Alice', status: 1 },
        { id: 2, name: 'Bob', status: 2 }
      ],
      total: 2,
      code: 200,
      message: 'success'
    };
  });
  
  server.post('/api/users/save', (schema, request) => {
    const attrs = JSON.parse(request.requestBody);
    return { code: 200, message: 'Saved successfully', data: attrs };
  });
}
```

### 2. The API Registration Trap (CRITICAL)
Even if you only want to mock an API to test a `ui-list-view`, **the `ui-list-view` will fail silently or throw errors if the API is not registered in the frontend router config.**
Before testing your mock, ensure you register the API model.

```javascript
// app/scripts/apis/content/user.js
export default [
  // name, path, methods, config
  ['user', '/api/users', ['read', 'write'], {
    crud: {
      read: { list: '/list' },
      write: { save: '/save' }
    }
  }]
];
```

## Anti-Patterns
- ❌ **Anti-pattern**: Hardcoding fake arrays directly inside a Vue component's `data()` just to see how the table looks. Always use the mock server so you are actually testing the `ui-list-view` HTTP integration.
