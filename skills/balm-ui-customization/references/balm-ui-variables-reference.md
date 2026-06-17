# BalmUI (MDC) Module Import Reference

When using `@use ... with` to customize components in BalmUI, you need to import the correct MDC module. Below is a cheat sheet of the most commonly used module paths for customization. 

**Note for LLMs:** If you need to know *what* variables exist inside these modules, use the `view_file` tool on the corresponding file in `node_modules/@material/...` (e.g., `node_modules/@material/button/_button-shared-theme.scss`).

## Foundation & Theming
- **Theme/Colors**: `@use '@material/theme/theme-color' with (...)`
- **Typography**: `@use '@material/typography/typography' with (...)`
- **Shape**: `@use '@material/shape/shape' with (...)`
- **Elevation**: `@use '@material/elevation/variables' with (...)`
- **Grid**: `@use '@material/layout-grid' with (...)`
- **Ripple**: `@use '@material/ripple/ripple-theme' with (...)`

## Common Components
- **Button (Shared)**: `@use '@material/button/button-shared-theme' with (...)`
- **Button (Outlined)**: `@use '@material/button/button-outlined-theme' with (...)`
- **Card**: `@use '@material/card' as card-variables with (...)`
- **Checkbox**: `@use '@material/checkbox/checkbox-theme' with (...)`
- **Chips**: `@use '@material/chips/deprecated/variables' with (...)`
- **Dialog**: `@use '@material/dialog' with (...)`
- **Drawer**: `@use '@material/drawer' with (...)`
- **FAB (Normal)**: `@use '@material/fab/fab-theme' with (...)`
- **FAB (Extended)**: `@use '@material/fab/extended-fab-theme' with (...)`
- **Icon Button**: `@use '@material/icon-button' with (...)`
- **Image List**: `@use '@material/image-list' with (...)`
- **List**: `@use '@material/list' with (...)`
- **Menu Surface**: `@use '@material/menu-surface' with (...)`
- **Menu**: `@use '@material/menu' with (...)`
- **Radio**: `@use '@material/radio/radio-theme' with (...)`
- **Select (Theme)**: `@use '@material/select/select-theme' with (...)`
- **Select (Icon)**: `@use '@material/select/select-icon-theme' with (...)`
- **Snackbar**: `@use '@material/snackbar' with (...)`
- **Switch**: `@use '@material/switch/deprecated/variables' with (...)`
- **Table**: `@use '@material/data-table' with (...)`
- **Tabs (Tab)**: `@use '@material/tab' as tab-variables with (...)`
- **Tabs (Scroller)**: `@use '@material/tab-scroller' with (...)`
- **Tabs (Bar)**: `@use '@material/tab-bar' with (...)`
- **Textfield**: `@use '@material/textfield' with (...)`
- **Textfield (Icon)**: `@use '@material/textfield/icon' with (...)`
- **Top App Bar**: `@use '@material/top-app-bar' with (...)`
- **Linear Progress**: `@use '@material/linear-progress' with (...)`
- **Circular Progress**: `@use '@material/circular-progress' with (...)`

*Remember: Always inspect the node_modules files if you need to know exactly what variables like `$height`, `$shape-radius`, or color mappings are available.*
