# Codex / Other Agents Setup

类似于普通的 LLM 客户端，`codex` 或是其他不具备硬编码 Skill 系统的工具，通常依赖于给定的任务上下文（Context）或者是项目级的配置文件注入。

## 安装步骤
将技能包放入项目目录内，例如放在 `.codex/skills/balm-ui/` 中。如果你有 `codex.yaml` 配置文件，可以将其添加到默认的 context 包含路径中。

```bash
mkdir -p .codex/skills/balm-ui
cp -r ./skills/balm-ui-customization/* .codex/skills/balm-ui/
```

## 使用方式
在下达命令时，显式让它加载或者参考这些文件作为上下文：

> *"codex: 请参考 `.codex/skills/balm-ui/SKILL.md` 中的规范，帮我定制一个基于 BalmUI 的紫罗兰主题色。如果有你不确定的组件参数，请务必遵照文件中的 Discovery Protocol 使用你的命令行和文件搜索工具去 node_modules 中提取真实的变量名。"*
