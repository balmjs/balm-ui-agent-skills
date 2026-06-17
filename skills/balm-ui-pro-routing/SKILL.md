---
name: balm-ui-pro-routing
description: Guide for adding and configuring new pages, routes, and modifying the dynamic sidebar menus in BalmUI Pro layouts.
---

# BalmUI Pro Routing & Layout Skill

This skill governs how you register new Vue pages and ensure they appear correctly in the system's layout and sidebar navigation.

## Core Philosophy
Routing is centralized. New `.vue` pages must be registered into the core route configuration to be loaded by the main layouts.

## 🔍 Discovery & Investigation Protocol (CRITICAL)

Before configuring routes:
1. **Discover Routes File**: Find the central route registry, typically located at `app/scripts/routes/config.js` or `app/scripts/routes/index.js`.
2. **Discover Layout Structure**: Understand the layout wrappers (like `admin-layout.vue` or `indexView`). 

## Step-by-Step Workflow

### 1. Registering the Route
Add your new Vue component to the `contentRoutes` (or equivalent) array inside the layout's children.

```javascript
// app/scripts/routes/config.js
import Dashboard from '@/pages/dashboard';
import UserList from '@/pages/users/list';

const contentRoutes = [
  {
    path: 'dashboard',
    name: 'dashboard',
    component: Dashboard
  },
  {
    path: 'users',
    name: 'user.list',
    component: UserList
  }
];

export default [
  {
    path: '/',
    component: IndexView, // The main Layout
    children: contentRoutes
  }
];
```

### 2. Sidebar Menu Expansion Hacks
Often, sidebars are generated asynchronously from state `this.$store.getMenu()`.
If menus are failing to auto-expand to the current active route, the standard fix is observing the `$route` in the layout component.

```javascript
// admin-layout.vue (Script section)
watch: {
  '$route'(to) {
    // Find the current route in the menu tree
    // and push its parent indices to `this.expanded` array to force open the collapse.
    this.autoExpandMenu(to.name);
  }
}
```

### 3. Menu Indentation Styling
When dealing with multi-level menus (3rd or 4th level), sometimes BalmUI components need slight visual CSS adjustments. Do not build complex JS logic for this.

**Recommended approach**: Use straightforward CSS overriding. If Webpack `sass-loader` complains about deep parsing, just use a plain `<style>` tag without `lang="scss"`.

```html
<style>
/* Force indent level 3 menus */
.sidebar-menu .level-3-item {
  padding-left: 48px !important; 
}
</style>
```

## Anti-Patterns
- ❌ **Anti-pattern**: Using `window.location.href` or traditional `<a>` tags for internal navigation. Use `this.$router.push` or `<router-link>`.
