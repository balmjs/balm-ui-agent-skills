# Claude Code Setup

`claude-code` (Anthropic 官方 CLI 工具) 虽然没有一个名为 "Skills" 的硬编码全局文件夹，但它非常善于理解工作区（Workspace）中的 markdown 规范文档，你可以通过文件引入的方式将其变为专属知识。

## 安装步骤
建议将 BalmUI 的相关技能放置在项目根目录下一个专门存放 AI 背景知识的隐藏文件夹中（例如 `.claude/` 或直接就叫 `.cursorrules` / `AI_RULES/`）。

```bash
# 在当前项目中创建 AI 规则存放点
mkdir -p .claude/balm-ui-skill
cp -r ./skills/balm-ui-customization/* .claude/balm-ui-skill/
```

## 使用方式
当你在终端运行 `claude` 时，可以通过 `@` 符号提及该协议文件，或者在初始 prompt 中要求它读取：

> *"@.claude/balm-ui-skill/SKILL.md 请仔细阅读这个样式自定义协议。接下来，帮我修改一下登录页面的文本框和按钮，让它们变成圆润极简风。"*

Claude 阅读该协议后，就会知道 BalmUI 基于 MDC，进而按照文档里的要求主动去探索和读取包内部的 Sass 变量。
