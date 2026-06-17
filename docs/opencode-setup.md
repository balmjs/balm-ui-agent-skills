# OpenCode Setup

在 OpenCode 生态下，智能代理技能可以通过显式赋予工作区权限来让 Agent 主动加载。

## 安装步骤
你可以将 `skills/` 下的子文件夹复制到你工程的 `.opencode/` 下，或者通过 `AGENTS.md` 将其指向外部存储库。

```bash
mkdir -p .opencode/skills/
cp -r ./skills/balm-ui-customization .opencode/skills/
```

## 使用方式
通知代理使用 `skill` 工具加载指定的 BalmUI 协议文件：

> *"在开始编写这个模块的 CSS 前，请使用你的 skill 工具加载 `.opencode/skills/balm-ui-customization`。按照里面的 Discovery Protocol 确定可用的 MDC 变量。"*
