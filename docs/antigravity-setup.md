# Antigravity CLI Setup

`agy`（内部代号 Antigravity）对 Skill 包有着完善的原生支持。它在启动或运行过程中，会自动扫描特定的配置目录并加载里面带有 `SKILL.md` 的文件夹，从而将其作为 Agent 随时可以调用的能力扩展。

## 安装步骤
将你需要的技能文件夹（如 `skills/balm-ui-customization`）拷贝至全局配置中。该目录通常位于 `~/.gemini/skills/` 或 `~/.gemini/config/skills/`：

```bash
# 整体作为插件安装（推荐）
agy plugin install https://github.com/balmjs/balm-ui-agent-skills.git

# 或者手动安装单个技能
mkdir -p ~/.gemini/skills/
cp -r ./skills/balm-ui-customization ~/.gemini/skills/
```

## 使用方式
安装完成后，正常启动 `agy` 进行对话即可。你可以直接通过自然语言唤醒这个技能：

> *"运用 balm-ui-customization 技能，帮我给这个项目写一个具有赛博朋克风格的样式入口文件。"*

`agy` 在处理任务时会自动检索到该技能，读取 `SKILL.md` 和 `references`，并遵循里面的**「探索与检索协议（Discovery Protocol）」**自动使用工具去查找 `node_modules` 里的正确变量并完成代码编写。
