# BalmUI 样式自定义与重写方案指导指南

## 一、概述
BalmUI 的样式架构基于 Material Design Components (MDC) Web。对 BalmUI 进行样式自定义，最核心的机制是利用 Sass 的 module 语法 `@use ... with (...)`，在加载相应的 MDC 模块或 BalmUI 模块前对其内部的 Sass 变量进行覆盖。这允许开发者（以及如 Gemini 的大模型）快速将 BalmUI 打造成任意品牌风格（如：现代极简、黑夜模式、拟物风等），而无需编写大量冗余的 CSS 覆盖代码。

## 二、核心工作流 (针对大模型)
当大模型被要求利用 BalmUI 制作特定风格的网站时，应遵循以下工作流：

1. **设计系统拆解**：根据用户期望的风格，确定核心色（Primary, Secondary, Background, Surface, Error）和字体排版（Font Family, 标题与正文字号）。
2. **创建 Sass 入口及变量覆盖层**：在导入 BalmUI 样式之前，按照层级关系（Theme -> Typography -> Components）使用 `@use ... with` 覆盖 MDC 变量。
3. **导入主样式**：变量覆盖完成后，再引入相应的 BalmUI 样式或者具体组件样式。
4. **局部微调 (Overrides)**：对 `@use ... with` 无法触及的特殊需求，通过 CSS 类选择器（如 `.mdc-button`, `.mdc-text-field` 等）进行适量的纯 CSS/Sass 覆盖。

## 三、样式自定义核心模块与语法

### 1. 主题与颜色 (Theme)
利用 `@material/theme/theme-color` 可以重写所有应用的基础色板。

```scss
@use '@material/theme/theme-color' with (
  $primary: #6200ee,
  $secondary: #018786,
  $background: #ffffff,
  $surface: #ffffff,
  $error: #b00020,
  $text-colors: (
    dark: (
      primary: rgba(black, 0.87),
      secondary: rgba(black, 0.54)
    ),
    light: (
      primary: white,
      secondary: rgba(white, 0.7)
    )
  )
);
```

### 2. 字体排版 (Typography)
通过 `@material/typography/typography` 可以自定义字体族和各级文字的属性。

```scss
@use '@material/typography/typography' with (
  $font-family: string.unquote('Inter, Roboto, sans-serif'),
  $styles-headline1: (
    font-size: 3rem,
    font-weight: 700
  ),
  $styles-body1: (
    font-size: 1rem,
    line-height: 1.5
  )
);
```

### 3. 组件层级自定义 (以 Button 为例)
不同的组件有各自的 Sass 变量空间。通常需要查看 BalmUI 源码或者 MDC 文档。

```scss
@use '@material/button/button-shared-theme' with (
  $height: 44px, // 改变按钮高度
  $shape-radius: 8px, // 改变圆角（如变为更现代的圆角按钮）
  $contained-horizontal-padding: 24px
);

@use '@material/button/button-outlined-theme' with (
  $outlined-border-width: 2px
);
```

### 4. 其它常用组件变量
除了按钮，表单、卡片等也是常用覆盖目标：
- **TextField**: `$height`, `$shape-radius`, `$fill-color`
- **Card**: `$shape-radius`, `$elevation`
- **Shape**: 定义全局的圆角尺寸（如 `$small-component-radius`）

## 四、少量样式重写 (Overrides) 策略
不要滥用 `!important`。BalmUI 组件多使用 `.mdc-` 作为前缀，少数使用 `.balm-`。大模型在做局部样式重写时，应当：
1. **高优先级选择器**：利用父级容器类名增加权重，如 `.my-custom-section .mdc-button { ... }`。
2. **利用 CSS 变量**：现代浏览器中，部分 MDC 组件会暴露 CSS Custom Properties，可以在 `.balm-ui-app` 级别进行覆盖。
3. **只针对非标准设计**：如果设计要求超出了 Material Design 的范畴（例如特殊的渐变背景、非常规的阴影），则使用普通 CSS 编写。

## 五、Skill 包结构建议
大模型可以加载这个 Skill 以快速获得 BalmUI 自定义能力：
- `SKILL.md`: 包含大模型 System Prompt / 指令，教导其在写代码时优先使用 `@use with` 策略。
- `examples/`: 包含几个典型风格（如 Dark Mode、Neon Cyberpunk、Clean Minimalist）的 `.scss` 文件模板。
