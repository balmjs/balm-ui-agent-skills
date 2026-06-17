# BalmUI Agent Skills

This repository contains official agent skills for [BalmUI](https://material.balmjs.com/), designed to empower AI coding agents (like `agy`, `claude-code`, and `codex`) with deep knowledge of the framework's architecture, styling conventions, and customization patterns.

## Available Skills

### `balm-ui-customization`

A comprehensive guide and workflow protocol for customizing BalmUI styles using Sass variables (`@use ... with`) and minimal CSS overrides. It includes a strict "Discovery Protocol" ensuring AI agents actively search for Material Design Components (`@material`) variables instead of hallucinating them.

## Directory Structure

- `skills/`: Contains the actual skill packages (e.g., `balm-ui-customization/`). Each skill has its own `SKILL.md` and related reference/example files.
- `docs/`: Contains additional documentation and guides for users and developers.
  - `balm-ui-customization-guide.md`: The complete guide to styling strategies.
  - `skill-anatomy.md`: Specification for writing new BalmUI skills.
  - `<tool>-setup.md`: Specific setup guides for various CLI tools.

## Quick Start

<details>
<summary><b>Claude Code</b></summary>

Copy any `SKILL.md` into your `.claude/` directory, or reference the full `skills/` directory. See [docs/claude-setup.md](docs/claude-setup.md).

**Local / development:**

```bash
git clone https://github.com/balmjs/balm-ui-agent-skills.git
claude --plugin-dir /path/to/balm-ui-agent-skills
```

</details>

<details>
<summary><b>Antigravity CLI</b></summary>

Install as a native plugin for skills. See [docs/antigravity-setup.md](docs/antigravity-setup.md).

**Install from the repo:**

```bash
agy plugin install https://github.com/balmjs/balm-ui-agent-skills.git
```

**Install from a local clone:**

```bash
git clone https://github.com/balmjs/balm-ui-agent-skills.git
agy plugin install ./balm-ui-agent-skills
```

</details>

<details>
<summary><b>Gemini CLI</b></summary>

Install as native skills for auto-discovery. See [docs/gemini-cli-setup.md](docs/gemini-cli-setup.md).

**Install from the repo:**

```bash
gemini skills install https://github.com/balmjs/balm-ui-agent-skills.git --path skills
```

**Install from a local clone:**

```bash
gemini skills install ./balm-ui-agent-skills/skills/
```

</details>

<details>
<summary><b>OpenCode</b></summary>

Uses agent-driven skill execution via `AGENTS.md` and the `skill` tool.
Copy the `skills/` directory into your `.opencode/` or workspace directory and instruct your agent to use them. See [docs/opencode-setup.md](docs/opencode-setup.md).

</details>

<details>
<summary><b>Codex / Other Agents</b></summary>

Skills are plain Markdown - they work with any agent that accepts system prompts or instruction files. Copy the contents into your project's `.codex/` directory and explicitly reference the `SKILL.md` in your prompts to instruct the AI. See [docs/codex-setup.md](docs/codex-setup.md).

</details>

---

## License

MIT - use these skills in your projects, teams, and tools.
