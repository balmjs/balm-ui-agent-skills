# Gemini CLI Setup

Gemini CLI 对 Agent Skills 也具有较好的支持，可以通过直接安装命令将其作为本地知识库的一部分。

## 安装步骤
你可以使用内置的 `skills install` 命令自动解析和拉取存储库中的技能包。

```bash
# 自动通过远端安装
gemini skills install https://github.com/balmjs/balm-ui-agent-skills.git --path skills

# 从本地克隆安装
gemini skills install ./balm-ui-agent-skills/skills/
```

## 使用方式
安装完成后，Gemini CLI 在其 Context Window 中将包含对于 BalmUI 自定义的认知：

> *"运用我们刚才安装的 BalmUI 样式定制技巧，帮我覆盖 Button 组件，去掉自带的大写限制（text-transform）。"*
