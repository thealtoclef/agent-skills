# agent-skills

## Install this repository

Requires Node.js and npm (`npx`).

Install **all skills** from [thealtoclef/agent-skills](https://github.com/thealtoclef/agent-skills)
globally for Claude Code:

```sh
npx skills add thealtoclef/agent-skills \
  --global \
  --agent claude-code \
  --yes \
  --skill '*'
```

Skills are installed globally in `~/.agents/skills`, with symlinks in
`~/.claude/skills`. Agents that read the shared directory can also discover them.

## External skills

Source: [mattpocock/skills](https://github.com/mattpocock/skills).
These skills are maintained upstream, not copied into this repository.

### Skills we use

- `codebase-design`
- `domain-modeling`
- `grill-me`
- `grill-with-docs`
- `grilling`
- `improve-codebase-architecture`
- `pr`
- `research`
- `tdd`
- `wait-what`
- `writing-for-agents`

### Install

Install only the selected skills globally for Claude Code:

```sh
npx skills add mattpocock/skills \
  --global \
  --agent claude-code \
  --yes \
  --skill \
    codebase-design \
    domain-modeling \
    grill-me \
    grill-with-docs \
    grilling \
    improve-codebase-architecture \
    pr \
    research \
    tdd \
    wait-what \
    writing-for-agents
```