# Anatomy of a BalmUI Agent Skill

This guide explains how to write new skills for the `balm-ui-agent-skills` project. A skill is a standardized markdown file that encodes best practices for AI agents, teaching them *how* to use BalmUI properly without hallucinating code.

## File Structure

Every skill lives in a sub-folder under `/skills/` and must contain a `SKILL.md` file:

```
skills/
├── your-new-skill/
│   ├── SKILL.md            <-- Required
│   ├── examples/           <-- Optional references
│   └── references/         <-- Optional cheat sheets
```

## Anatomy of SKILL.md

A proper `SKILL.md` consists of several strict sections:

### 1. Frontmatter
Every skill must start with YAML frontmatter containing metadata so tools like `agy` can automatically index them.
```yaml
---
name: your-skill-name
description: A short, 1-2 sentence description of when and why an agent should use this skill.
---
```

### 2. Core Philosophy (Overview)
Briefly state what the skill is meant to achieve within the BalmUI ecosystem. Emphasize standard MDC architectures.

### 3. Discovery Protocol (CRITICAL)
**All BalmUI skills must contain a Discovery & Investigation Protocol.** 
Because AI agents cannot memorize every single `$variable` or `@mixin` inside Material Design Components, you must explicitly instruct them to use their file reading tools (like `list_dir` or `grep_search`) to inspect `node_modules/@material` or `node_modules/balm-ui` before writing code.
*Example: "Do not guess Sass variables. Search `node_modules/@material/...` first."*

### 4. Step-by-Step Workflow
Define the concrete steps the AI should take. 
- Bad: "Make the button look good."
- Good: "1. Locate button theme variables. 2. Define `@use with` block. 3. Import `balm-ui/button`."

### 5. Fallbacks & Overrides
Provide clear boundaries on when the AI is allowed to write pure CSS (`.mdc-button { ... }`). Encourage utilizing the variable ecosystem first.

## Best Practices
- **Be prescriptive, not descriptive.** Tell the agent what to *do*, not just what BalmUI *is*.
- **Use examples.** Give the agent syntax snippets they can copy.
- **Reference local documents.** Link to `references/` if the list of variables is too long for the main prompt.
